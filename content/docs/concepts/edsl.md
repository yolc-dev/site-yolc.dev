+++
title = "Embedded DSL (EDSL) in Haskell"
date = 2025-05-19
weight = 5
template = "docs/page.html"

[extra]
toc = true
lead = "This makes Yolc expressive."
+++

# What Is Deep Embedding

Haskell is a great host language for writing Domain Specific Languages (DSLs), and Yolc provides a deep-embedded DSL in
Haskell called "YulDSL/Haskell."

Programming in Yolc is programming in Haskell:

```haskell
foo :: PureFn (U256 -> U256 -> U256 -> U256)
foo = $fn \a b c -> a + b * c
```

The above is a valid Haskell code, and Yolc's Yul code generator can interpret[^1] and generate the
following valid Solidity/Yul code:

```zig
function u$$pfn_Basic_Tests_59_15(v_a, v_b, v_c) -> v_d {
 v_d := __checked_add_t_uint256(v_a, __checked_mul_t_uint256(v_b, v_c))
 leave
}
```

The difference between shallow and deep embedding lies in that deep embedding can reinterpret the original programs into
different forms. In our case, a different interpreter can also translate the same program into a GraphViz diagram.

![GraphViz preview of foo](/concepts/edsl/previewfn-graphviz.png)

> 💡 Read [here](/docs/advanced/category-dsl/) to learn more about the inner category-theoretical working of the "YulDSL."

# Interoperability

Yolc will be able to interact with Solidity code, and Yolc will generate Solidity code helpers for Solidity code to
interact with Yolc generated code.

## Contract ABI Compatible

First of all, Yolc will be fully-compatible with [Contract
ABI](https://docs.soliditylang.org/en/latest/abi-spec.html). You can interact with contracts written by Solidity, and
vice versa.

> ⚠️ Some parts of the support is still work-in-progress.

## Storage Layout

To access contract storage, a similar layout mechanism to Solidity's "map" is provided. The following are code for static
functions to balances and allowances in a ERC20 example contract.

```haskell
-- | Storage map of account balances
balances :: SMap (ADDR -> U256)
balances = makeSMap "Yolc.Demo.ERC20.Storage.AccountBalances"

-- | ERC20 balance of the account.
balanceOf :: StaticFn (ADDR -> U256)
balanceOf = $lfn $ ylvm'pv \owner -> sgetM $ balances #-> owner

-- | Storage map of allowances
allowances :: SMap (ADDR {- Owner -} -> ADDR {- spender -} -> U256)
allowances = makeSMap "Yolc.Demo.ERC20.Storage.Allowances"

-- | ERC20 allowance function.
allowance :: StaticFn (ADDR -> ADDR -> U256)
allowance = $lfn $ ylvm'pv
  \owner spender -> sgetM $ allowances #-> (owner, spender)
```

Note that you can specify storage location with a string that is hashed by keccak256.

# Expressive Type System

Thanks to the deep embedding, Yolc has access to all the Haskell's type level features. Here are a few notable ones.

## Polymorphic Types, or "Generics"

You could write a function that works for any integer types. In the following example, the same `poly_foo` function is
instantiated to uint8, int32 and uint256 types.

```haskell
poly_foo :: forall a s n.
  ( a ~ INTx s n, ValidINTx s n
  ) => PureFn (a -> a -> a)
poly_foo = $fn \x y -> x * 2 + y

-- Test same poly_foo instantiated in different types:
test_poly_foo :: Bool
test_poly_foo = and
  [ evalFn (poly_foo @U8) (4 :* 2 :* Nil)   == 10
  , evalFn (poly_foo @I32) (4 :* 2 :* Nil)  == 10
  , evalFn (poly_foo @U256) (4 :* 2 :* Nil) == 10
  ]
```

## Higher Kinded Types

This allows you to define type that takes a parameter of another type. The following example shows a "Maybe" type, or
sometimes called optional value in some other languages.

```haskell
-- Add @b@ to a using @a@ functor, since @a@ is a Maybe type.
maybe_functor_fn2 :: PureFn (Maybe U8 -> U8 -> Maybe U8)
maybe_functor_fn2 = $fn \a b -> (+ b) <$$> a
```

And if you knew some Haskell already, you may have guessed that `<$$>` is the operator version of fmap in Yolc.

## Algebraic Data Type

With Yolc, you can define more than just "struct" in Solidity. A struct is called a "product" type in Algebraic Data
Type (ADT). You should feel free to ignore such a pedantic naming. However, what makes ADT more powerful the dual of the
"product" type, "sum" type.

```haskell
data BuyOrder
  = MarketOrder Time {- expiring at -}
  | LimitOrder Time {- expiring at -} Price {- limit price -}
```

Now you can represent a buy order with two different cases: A market order, or a limit order.

To work with it, you can use Yolc's pattern-matching feature:

```haskell
match order \case
  MarketOrder t     -> _ {- ... do something ... -}
  LimitOrder  t prc -> _ {- ... do something differently ... -}
```

> 💡 Read [here](/docs/concepts/adt) to learn more about how to define and work with ADT in Yolc.

## LinearTypes and Data Versioning

Most notably, Yolc uses LinearTypes to provide a constrained environment to program effectful code (storage access,
external contract messaging, etc.) with data versioning.

With data versioning, programming bugs such as "re-entrance" errors, or more generally using "outdated" data, are
eliminated in compile-times.

Read [here](/docs/concepts/data-versioning) to learn more about Data versioning in Yolc.