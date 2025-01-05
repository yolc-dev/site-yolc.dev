+++
title = "Introducing Yolc"
date = 2025-01-06T09:00:00
template = "blog/page.html"

[extra]
lead = "Yolc is a safe, expressive, fun language for Ethereum, powered by YulDSL/Haskell."
+++

## Background

On 30th July 2015, the Ethereum network [went live](https://blog.ethereum.org/2015/07/30/ethereum-launches).

Fast-forward 10 years. By the time of writing this post, according to [etherscan](https://etherscan.io/), the Ethereum
network has made 2.6 billion transactions and processed 21.6 million blocks. It currently processes 13 transactions per
second on its base layer alone, while a few layer 2s further scale its capacity at a much higher throughput of
transactions per second. Ethereum has already significantly impacted the world and is here to stay.

Programming on Ethereum, as a smart contract platform, has never been easier since the creation of the [Solidity
language](https://soliditylang.org/). Many have benefited from it, creating worldwide socially and economically
impactful experiments and applications simply by having a computer and an internet connection.

Ethereum's success has attracted much brainpower to its ecosystem. Over the years, some have also left and attempted to
replicate its success by bringing the best of the current human knowledge through a clean slate start. It is a healthy
market phenomenon of competition. Still, as a citizen of the Ethereum ecosystem, it is also an important signal that
there are areas where we can learn and progress further.

## What The Yolc!?

**Yolc** (as opposed to... solc, the solidity compiler) was born in response to one specific area: the Ethereum
ecosystem needs a general-purpose programming language that is safe, expressive, and as fun as Solidity to work with.

### General Purpose

Yolc is not entirely a new language. Instead, it embeds itself in [Haskell
language](https://www.haskell.org/). Therefore, programming in Yolc is programming in Haskell, a general-purpose
language with its [share of success in the industry](https://github.com/erkmos/haskell-companies), including the latest
ones such as Groq.

### Safe

Haskell is a purely functional language that provides many type-level safety features when programming. One of the
safety features is
"[LinearTypes](https://ghc.gitlab.haskell.org/ghc/doc/users_guide/exts/linear_types.html#design-and-further-reading),"
which powers **one of the unique features of Yolc: "linearly versioned data."**  In short, this makes a class of
vulnerabilities, such as reentrance issues, impossible to pass the type-checker. It is a direct result of having
implicit data versioning when dealing with data retrieved from the "world," whereby "outdated information" cannot be
used in the current state of the "world."

*Here is an example (with some code omission for simplicity) of data versioning:*

```haskell
-- STEP 1: Fetching account balance.
--         It is tagged with data version 0
balanceBefore <- balance_of account

-- ... some code omitted ...

-- STEP 2: Calling external contract's onTokenMinted function,
--         which advances world data version to 1
externalCall onTokenMinted account mintAmount

-- STEP 3: ⛔ CANNOT COMPILE: balanceBefore has data version of 0,
--         but the wolrd has moved on to version 1.
sput (balance_ref_of account) (balanceBefore + mintAmount)

```

Swapping step 2 and step3 will produce correct code.


Read [here](/docs/linearsafety) to learn more about linear-type safety.

### Expressive

With Yolc, one should write one's core smart contract in pure functions where storage or external contract interactions
are not involved. Pure functions do not require linear-type safety.

Yolc incentivizes you to write more pure functions because writing linear-type safety code requires some crafting, a
trade-off in exchange for higher-level code safety.

In contrast, writing pure function code in Yolc is expressive since you can access all Haskell language features,
including pattern matching, generalized algebraic data types, parametrically polymorphic functions, etc. More future
blog posts will explain these features as more becomes available to Yolc.


*Here is an example of patter matching on optional (Haskell's Maybe type) numbers:*

```haskell
\x y def -> match (x + y) \case
  Nothing -> def  -- number overflown
  Just z  -> z    -- just do it
```

### Fun

Once you have access to more advanced and modern language features, writing code that is more likely to be safe in a
production environment, programming on Ethereum becomes a fun experience.

> **Thus, the Yolc project's core motivation is to balance safety, expressiveness, and fun when programming on
> Ethereum.**

## Join!

Today is the launch of the technical preview of the Yolc project. It is the first milestone where Yolc can produce a
[minimal ERC20 code](https://github.com/yolc-dev/yul-dsl-monorepo/tree/master/examples/demo/src) deployable to Ethereum
networks, demonstrating the key idea of linear-type safety. The technical preview still has its rough edges, but you can
already [get started](/docs/getstarted).

It has a roadmap to reach key feature parity with Solidity by 2025 Q1, shipping unique features beyond Solidity all the
time, and adoption from key projects, such as [Superfluid
Protocol](https://github.com/superfluid-finance/protocol-monorepo/) prototyping its core logic of next version using
Yolc.

I invite you to [join the journey of Project Yolc as a new community](/community), [follow the updates and learn about
Yolc](https://linktr.ee/yolc), and let's shape the future of Yolc and the Ethereum ecosystem together!
