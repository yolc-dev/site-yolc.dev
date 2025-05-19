+++
title = "Data Versioning"
date = 2025-05-19
weight = 20
template = "docs/page.html"

[extra]
toc = true
lead = "In two examples."
+++

Yolc has a unique safety feature called "data versioning." The key idea is that in compile time, Yolc tracks the state
of the EVM when data was read, aka. versioning the data. Later on, if there is any chance that the state of the virtue
machine is updated, then the previously read data must be considered outdated and shall not be used again.

Here are two inspiring examples to show how this safety feature can prevent bugs

## Example 1: Requiring "from != to"

In the following example, an innocuous ERC20 transfer function which does routinely:

1) Retrieve sender and receiver's balances.
2) Calculate new balances.
3) Do something with the new balances.
4) Update balances.

```haskell
transfer :: OmniFn (ADDR -> U256 -> BOOL)
transfer = $lfn $ ylvm'pv
  \to amount -> LVM.do
    Ur from <- ycaller

    -- ⛔ INCORRECT CODE, CANNOT PASS COMPILATION:
    Ur senderBalance <- ycall balanceOf (ver from)
    Ur receiverBalance <- ycall balanceOf (ver to)

    -- calculate new balances
    Ur (newSenderBalance, newReceiverBalance) <- ywithrv
      @(U256 -> U256 -> U256 -> (U256, U256))
      (ver amount, senderBalance, receiverBalance)
      \amount' senderBalance' receiverBalance' ->
        be (senderBalance' - amount', receiverBalance' + amount')

    -- Do something with the new balances and reuse later to avoid recalculation
    -- ...

    -- WARNING: THIS IS WRONG
    -- Have you found the issue?
    balances #-> from <<:= newSenderBalance
    balances #-> to   <<:= newReceiverBalance
```

However, there is a deadly mistake: it should check that if "from != to".

In canonical ERC20 examples, it typically checks the condition. It may seem contrived, but in custom transfer
implementations, where such intermediate values may seem an optimization, such a mistake can occur.

Nonetheless, with Yolc, it is a compile-time error:

```
src/ERC20.hs:63:23: error: [GHC-22250]
    • Outdated data version
    • In a stmt of a 'do' block:
        balances #-> to <<:= newReceiverBalance
...
```

With **outdated data version**, what Yolc says is: "Since you don't provide a "run-time witness" (we will explain this
concept in a future article) that 'from' not equal 'to', I have no choice but assume that newReceiverBalance may have
changed since then." And Yolc throws an error instead.

Magic! But how can I write the correct code instead?

You can always rearrange the code, such that you perform the update after each reading:

```haskell
    Ur senderBalance <- ycall balanceOf (ver from)
    Ur newSenderBalance <- ywithrv_1 @(U256 -> U256 -> U256) (ver amount, senderBalance)
      \amount' senderBalance' -> senderBalance' - amount'
    balances #-> from <<:= newSenderBalance

    Ur receiverBalance <- ycall balanceOf (ver to)
    Ur newReceiverBalance <- ywithrv_1 @(U256 -> U256 -> U256) (ver amount, receiverBalance)
      \amount' receiverBalance' -> receiverBalance' + amount'
    balances #-> to <<:= newReceiverBalance
```

In a more general setting, there are two more solutions:

1) Provide a run-time witness in a type-safe way that compiler is happy with. We will explain this solution in a future
article.
2) Re-read the data! This solution is expensive, but always works.

## Example 2: Type-Safe "Checks Effects Interactions"

Another data version-related common bug is re-entrancy attack vulnerability, where the data you are using may be
outdated due to an non-trusted external call may have altered it. Common Solidity practice such as "check effects
interactions" provide a good guideline of how to prevent this from happening; open-zeppelin also provides
reentrance-lock that serves as a hammer to the problem.

Yolc data version provides a type-safe solution to this, instead.

Here is a mint function:

```haskell
mint :: OmniFn (ADDR -> U256 -> ())
mint = $lfn $ ylvm'pv
  \to amount -> LVM.do
    Ur balanceBefore <- ycall balanceOf (ver to)

    -- calculate new balance
    Ur newAmount <- ywithrv_1 @(U256 -> U256 -> U256)
      (balanceBefore, ver amount)
      (\x y -> x + y)

    -- call **untrusted** external contract onTokenMinted
    ycall (to @-> onTokenMinted) (ver to) (ver amount)

    -- update balance
    balances #-> to <<:= newAmount
```

And it has a glaring re-entrancy vulnerability. It certainly didn't follow the "checks-effects-interactions"
guideline. Again, Yolc catches it:

```
src/ERC20.hs:85:21: error: [GHC-22250]
    • Outdated data version
    • In a stmt of a 'do' block: balances #-> to <<:= newAmount
...
```

We get the same **outdated data version** error!

# Under The Hood

Data versioning is only the beginning of how Yolc explores more type-level safety guarantees.

Under the hood, it is the [GHC's
"LinearTypes"](https://ghc.gitlab.haskell.org/ghc/doc/users_guide/exts/linear_types.html) feature provides the horse
power. One usage of linear types allows type-level enforcement of linearly transitioned state, a form of model checking
closely related to Linear-time Temporal Logic (LTL). We will demonstrate such a claim in the future development Yolc.

Linear types is also closely related to uniqueness types. Some claims GHC's linearity-on-arrows is more general purpose
than uniqueness types. An example of uniqueness type is the borrow-checker in Rust to ensure unique ownership of a
resource handle enforced in type-level.

Read [here](/docs/advanced/linear-types) to learn more about how linear types is used in Yolc to internally for not just
data versioning.
