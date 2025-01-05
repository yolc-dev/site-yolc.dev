+++
title = "Yolc"

# The homepage contents
[extra]
lead = "A safe, expressive, fun language for Ethereum"

code_example = """
```haskell
-- ⭐ pattern matching coming to Ethereum
\\x y def -> match (x + y) \\case
  Nothing -> def  -- number overflown
  Just z  -> z    -- just do it

-- enforced linear type safety for side effects
sputs $ -- ⚠️ after this, data version increased by 1
  senderBalanceRef   := newSenderBalance   :|
  receiverBalanceRef := newReceiverBalance : []

-- linearly versioned data is here to help: --->
-- 🌟 reentrance vulnerability gone forever 🌟
externalCall onTokenMinted hackerAccount mintAmount
```
"""

left_url_button = "Read the Introduction"
left_url = "/blog/introduce-yolc"

right_url_button = "Join the Community"
right_url = "/community"

[[extra.list]]
title = "Safe"
content = "Yolc is purely functional with linear type safety, made for the Ethereum virtual machine."
explainer = 'What does <span class="fst-italic">linear type safety</span> mean here?'
explainer_url = "/docs/linearsafety"

[[extra.list]]
title = "Expressive"
content = "Yolc embeds itself in the Haskell language before being compiled into Solidity/Yul code."
explainer = 'What does <span class="fst-italic">embedding</span> mean here to expressiveness? (Coming soon...)'
explainer_url = "#"

[[extra.list]]
title = "Fun"
content = "Yolc allows you to write safe code in production, a joyful experience for super coders."
explainer = 'How can I <span class="fst-italic">get started?</span>'
explainer_url = "/docs/getstarted"

+++
