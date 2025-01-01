+++
title = "Introduce Yolc, a Safe, Expressive, Fun Language for Ethereum"
description = "Introducing Doks, a Hugo theme helping you build modern documentation websites that are secure, fast, and SEO-ready — by default."
date = 2025-01-01T02:00:00
draft = false
template = "blog/page.html"

[extra]
lead = "This is the source code of the traditional <b>Hello World</b> program."
+++

```haskell
add_maybe_int96_with_default = fn @(I96 -> I96 -> I96 -> I96)
  \x y def -> match (inCase (Just x) + inCase (Just y)) \case
    Nothing -> def
    Just z  -> z
```
