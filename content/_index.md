+++
title = "Yolc"

# The homepage contents
[extra]
lead = "A safe, expressive, fun language for Ethereum"

code_example = """
```haskell
mint :: OmniFn (ADDR -> U256 -> ())
mint = $lfn $ ylvm'pv
  \\to amount -> LVM.do
    Ur balanceBefore <- ycall balanceOf (ver to)
    Ur newAmount <- ywithrv_1 (balanceBefore, ver amount)
                    (\\x y -> x + y)
    -- ⚠️ NOTE: DATA VERSIONING AT COMPILE-TIME:
    -- update balance must happen before external calls.
    balances #-> to <<:= newAmount
    -- call **untrusted** external contract onTokenMinted
    ycall (to @-> onTokenMinted) (ver to) (ver amount)
```
"""

left_url_button = "Checkout Docs"
left_url = "/docs/concepts/edsl"

right_url_button = "Get Started"
right_url = "/docs/getting-started/introduction"

[[extra.list]]
title = "Safe"
content = "Yolc is purely functional with compile-time data versioning made for the Ethereum virtual machine."
explainer = 'What does <span class="fst-italic">data versioning</span> mean here?'
explainer_url = "/docs/concepts/data-versioning"

[[extra.list]]
title = "Expressive"
content = "Yolc embeds itself in the Haskell language before being compiled into Solidity/Yul code."
explainer = 'What does <span class="fst-italic">embedding</span> mean here to expressiveness?'
explainer_url = "/docs/concepts/edsl"

[[extra.list]]
title = "Fun"
content = "Yolc allows you to write safe code in production, a joyful experience for super coders."
explainer = 'How can I <span class="fst-italic">get started?</span>'
explainer_url = "/docs/getting-started/introduction"

+++
