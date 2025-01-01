+++
title = "Yolc (Coming soon...)"

# The homepage contents
[extra]
lead = "A safe, expressive, fun language for Ethereum"

code_example = """
```haskell
\\x y def -> match (x + y) \\case
  Nothing -> def
  Just z  -> z
```
"""

left_url_button = "Read the Introduction"
left_url = "/blog/introduce-yolc"

right_url_button = "Join the Journey"
right_url = "/ecosystem/"

[[extra.list]]
title = "Safe"
content = "Yolc is purely functional with linear type safety, made for the Ethereum virtual machine."
explainer = 'What does <span class="fst-italic">linear type safety</span> mean here?'
explainer_url = "#"

[[extra.list]]
title = "Expressive"
content = "Yolc embeds itself in the Haskell language before being compiled into Solidity/Yul code."
explainer = 'What does <span class="fst-italic">embedding</span> mean here to expressiveness? (Coming soon...)'
explainer_url = "#"

[[extra.list]]
title = "Fun"
content = "Yolc allows you to write safe code in production, a joyful experience for super coders."
explainer = 'How can I get started? (Coming soon...)'
explainer_url = "#"

+++
