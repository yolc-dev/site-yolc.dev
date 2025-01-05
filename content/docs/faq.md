+++
title = "Frequently Asked Questions"
weight = 99
template = "docs/page.html"

[extra]
lead = "Likely things you always wanted to know about Yolc."
toc = true
+++

## ⋄ Does it work?

Yes. While it is not yet feature parity with Solidity, we have released the technical review and deployed examples to
Ethereum networks. Read the [the instruction here](/getstarted) to get started.

Yolc will not be a complete toolkit for building Ethereum contracts. Instead, it works best with
[foundry](https://app.grammarly.com/ddocs/2699404776). More improvements to boilerplate generation will come, so, for
now you can see a working example from [here](https://github.com/yolc-dev/yul-dsl-monorepo/tree/master/examples/demo).

The following milestones (with monthly cadence) will be more than feature parity with Solidity. Yolc is always about
pioneering new possibilities. Follow the [project kanban board](https://github.com/orgs/yolc-dev/projects/1/) for more
details.

## ⋄ Is Yolc a completely new language?

No. Yolc embeds itself in the [Haskell language](https://www.haskell.org/), more specifically, [GHC version
9.10](https://downloads.haskell.org/ghc/9.10.1/docs/users_guide/).

To do so, Yolc implements both the [Ethereum contract ABI
specification](https://docs.soliditylang.org/en/latest/abi-spec.html) and a domain model of
[Solidity/Yul](https://docs.soliditylang.org/en/latest/yul.html) entirely inside the Haskell language so that you can
access all Haskell language features before the Haskell code gets compiled into the Solidity/Yul code.

## ⋄ What was the motivation behind Yolc?

The author is behind the [Superfluid Protocol](https://github.com/superfluid-finance/protocol-monorepo/) on Ethereum
networks, a money protocol that adds richer semantics to otherwise dull payment infrastructures. While looking for
improvement opportunities for version 3 of the Superfluid protocol, it became evident that the Ethereum ecosystem needed
a safer and more expressive language that is also fun to program on.

Instead of waiting for such a thing to appear, the author took the matter into his own hands. Meanwhile, remembering the
paper ["The Next 700 Programming Languages" by
P. J. Landin](https://github.com/yolc-dev/yul-dsl-monorepo/blob/master/hs-pkgs/yul-dsl/README.md#motivation), the author
created Yolc by embedding it in the Haskell language instead of an entirely new language.

## ⋄ Where to learn more about Yolc?

If you haven't, read the [introduction blog post](/blog/introduce-yolc) to get familiar with the key concepts of Yolc.

The next step is to visit the [community front page](/community) to follow the updates and hopefully to get stuck in
soon.

## ⋄ Does Yolc's logo mean something?

Yes. The Ethereum logo in the center is self-evident. The circling arrows and dots around the logo means
[isomorphism](https://abuseofnotation.github.io/category-theory-illustrated/02_category/#identity-and-isomorphisms) in
category theory.

Yolc is powered by YulDSL/Haskell, and YulDSL [grounds itself on category
theory](https://github.com/yolc-dev/yul-dsl-monorepo/blob/master/hs-pkgs/yul-dsl/README.md#a-dsl-based-on-category-theory).
