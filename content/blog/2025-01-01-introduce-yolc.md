+++
title = "Introducing Yolc"
description = "Introducing Yolc, a Safe, Expressive, Fun Language for Ethereum."
date = 2025-01-01T02:00:00
draft = false
template = "blog/page.html"

[extra]
lead = "Yolc is a safe, expressive, fun language for Ethereum, powered by YulDSL/Haskell."
+++

> [!WARNING]
>
> (Coming soon...)

```haskell
integer_pattern_matching = fn
  @(Maybe I96 -> Maybe I96 -> I96 -> I96) $locId
  \x y def -> match (x + y) \case
    Nothing -> def
    Just z  -> z
```
