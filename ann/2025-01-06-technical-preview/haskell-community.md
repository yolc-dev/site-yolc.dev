[ANN] Yolc - a Haskell-powered, safe, expressive, fun language for Ethereum

Yolc is for programming Ethereum using Haskell as an EDSL. Specifically, it uses GHC 9.10.

As a Haskell fan, I have more to say about Yolc for the Haskell community and also spare you details related to Ethereum
by simply sharing its complete introduction blog post for the Ethereum community angle of the Yolc project
[here](https://yolc.dev/blog/introduce-yolc/) should you find it relevant to you after all.

**Category Based**

First, the EDSL (called YulDSL) used in Yolc is category-based, namely a big GADT that has these categories defined:

```haskell
data YulCat (eff :: k) a b where
-- ...
  -- ** Category
  YulId   :: forall eff a.     YulO2 a a   => YulCat eff a a
  YulComp :: forall eff a b c. YulO3 a b c => YulCat eff c b %1-> YulCat eff a c %1-> YulCat eff a b
  -- ** Monoidal Category
  YulProd :: forall eff a b c d. YulO4 a b c d => YulCat eff a b %1-> YulCat eff c d %1-> YulCat eff (a, c) (b, d)
  YulSwap :: forall eff a b.     YulO2 a b     => YulCat eff (a, b) (b, a)
  -- ** Cartesian Category
  YulFork :: forall eff a b c. YulO3 a b c => YulCat eff a b %1-> YulCat eff a c %1-> YulCat eff a (b, c)
  YulExl  :: forall eff a b.   YulO2 a b   => YulCat eff (a, b) a
  YulExr  :: forall eff a b.   YulO2 a b   => YulCat eff (a, b) b
  YulDis  :: forall eff a. YulO1 a => YulCat eff a ()
  YulDup  :: forall eff a. YulO1 a => YulCat eff a (a, a)
-- ...
```

Every constructor of this type is a morphism from type `a` to `b`, tagged with a type "eff" used for some special
purpose explained later.

**Conversion between SMC to LinearTypes**

Now that YulCat is a symmetric monoidal category (SMC), using [linear-smc
package](https://hackage.haskell.org/package/linear-smc), one can convert between linear functions and YuLCat:

Namely, there are two functions from linear-smc:

```haskell
encode :: {-<-} (O3 k r a b, TensorClosed con, con ~ Obj k)
       => {->-}  (a `k` b) -> (P k r a ⊸ P k r b)
decode :: {-<-} forall a b k con.
          (con (), con ~ Obj k, Monoidal k, con a, con b, (forall α β. (con α, con β) => con (α,β)))
       => {->-} (forall r. {-<-}Obj k r => {->-} P k r a ⊸ P k r b) -> (a `k` b)
```

The above code may look very scary, but if you remove the noisy constraints and use the notion of "↝" to mean a morphism
in the category of "k", then we have:

```
P k r a ⊸ P k r b ≅ a ↝ b
```

P is also called "port API" provided by the linear-smc package. It provides a way to get the linear lambda you can program
with from a morphism (in the case of YulDSL, any of its constructors.)

**Linearly Versioned Monad**

The "kick" in the end is that I experimented with a (sort of) monad called "linearly versioned monad" (LVM) that can
work with these linearly-typed "ports" and attach a version to each port.

The idea of data versioning is specifically applicable to the Ethereum domain since the state of the system evolves
linearly, where using outdated data often leads to security vulnerability and results in countless financial losses.

The following code is the signature of the QualifiedDo "bind" operator for the LVM:

```
-- | Linear versioned monad (LVM) is a parameterized reader monad with linear safety.
newtype LVM ctx (va :: Nat) (vb :: Nat) a = MkLVM (ctx ⊸ (Dict (va <= vb), ctx, a))

-- | Monad bind operator for 'LVM', working with the QualifiedDo syntax.
--
-- (It conforms to the) usual _Law of Monad_
-- ... omitted ...
-- 1) Law of linearly versioned monad: @ ma [va <= vb] >>= mb [vb <= vc] ≡ mc [va <= vc] @
(>>=) :: forall ctx va vb vc a b. ()
      => LVM ctx va vb a ⊸ (a ⊸ LVM ctx vb vc b) ⊸ LVM ctx va vc b
```

Note that `va`, `vb`, `vc` above are `Nat` typed version numbers. And the LVM type carries a Dict/proof that `va <= vb`.

Here is an example of correct code:

```haskell
  \account'p mintAmount'p -> LVM.do
  -- fetch balance of the account
  (account'p, balanceBefore) <- pass account'p balance_of
  -- use linear port (naming convention, "*'p") values safely
  (account'p, mintAmount'p) <- passN_ (account'p, mintAmount'p) \(account'p, mintAmount'p) ->
    -- update balance
    sput (balance_ref_of account'p) (balanceBefore + ver'l mintAmount'p)
  -- call unsafe external contract onTokenMinted
  externalCall onTokenMinted (ver'l account'p) (ver'l mintAmount'p)
```

The line of "sput" is a storage effect monad that increases the version number by 1.

If we swap the order of the code, it won't compile because the data version numbers are mismatched:

```haskell
  -- fetch balance of the account
  (account'p, balanceBefore) <- pass account'p balance_of
  -- use linear port (naming convention, "*'p") values safely
  (account'p, mintAmount'p) <- passN_ (account'p, mintAmount'p) \(account'p, mintAmount'p) ->
    -- call unsafe external contract onTokenMinted
    externalCall onTokenMinted (ver'l account'p) (ver'l mintAmount'p)
  -- update balance, but using out dated "balanceBefore value" will fail to compile
  sput (balance_ref_of account'p) (balanceBefore + ver'l mintAmount'p)
```

Data versioning eliminates a vulnerability that has caused damage in the real world many times, and I believe that with
the help of linear types, a class of errors related to using information that is outdated is eliminated.

It's been quite an effort for me to get the first technical review version out, but I plan to continue to get it to
feature-parity with [Solidity](https://docs.soliditylang.org/) by Q1/2025.

Please consider following the progress of my further work at [linktr.ee/yolc](https://linktr.ee/yolc), and more
importantly happy Haskelling.

-----

Post Tracking
-------------

- Haskell Discourse: https://www.reddit.com/r/haskell/comments/1huujfz/ann_yolc_a_haskellpowered_safe_expressive_fun/
- Reddit: https://www.reddit.com/r/haskell/comments/1huujfz/ann_yolc_a_haskellpowered_safe_expressive_fun/
