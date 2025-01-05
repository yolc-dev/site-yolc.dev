+++
title = "Introduction"
date = 2025-01-06T09:00:00
weight = 0
template = "docs/page.html"

[extra]
toc = true
+++

Yolc is a safe, expressive, fun language for Ethereum, powered by YulDSL/Haskell.

> 🚧 Yolc is currently in technical preview: it works for technical demos, but feature-parity with Solidity will come by
> 2025/Q1.

## Try It Out

Because it is still in technical preview, to give the current technical demo a try, you should use
[Nix](https://nixos.org/) to get a reproducible, declarative, and reliable development environment
for settingup Yolc:


* Step 1: Follow the official instruction for [installing Nix the package
manager.](https://nixos.org/download/#download-nix)

* Step 2: After installing Nix, you may also need to add this to `nix.conf` (located in `.config/nix` in single-user
installs and in `/etc/nix` in multi-user installs):
  ```
  experimental-features = nix-command flakes
  ```

* Step 3: Checkout the [yul-dsl-monorepo](https://github.com/yolc-dev/yul-dsl-monorepo/) where Yolc is built from.
  ```
  $ git clone https://github.com/yolc-dev/yul-dsl-monorepo.git
  ```

* Step 4: Enter the Development Environment Through Nix Shell
  ```
  $ cd yul-dsl-monorepo
  $ nix develop .
  ```

* Step 5: Run the ERC20 demo that test it using [foundry](https://github.com/foundry-rs/foundry).
  ```shell
  $ make test-demo-foundry
  ```
  ```
  yolc -m yul examples/demo
  cd examples/demo && forge test -vvv
  [⠊] Compiling...
  [⠒] Compiling 25 files with Solc 0.8.28
  [⠢] Solc 0.8.28 finished in 1.00s
  Compiler run successful!

  Ran 2 tests for examples/demo/test/ERC20.t.sol:ERC20ProgramTest
  [PASS] testBalanceOfIsViewFunction() (gas: 13897)
  [PASS] testMintAndTransfer(uint128,uint128) (runs: 257, μ: 78223, ~: 78766)
  Suite result: ok. 2 passed; 0 failed; 0 skipped; finished in 39.49ms (39.26ms CPU time)

  Ran 1 test suite in 39.98ms (39.49ms CPU time): 2 tests passed, 0 failed, 0 skipped (2 total tests)
  ```


Great success!

You should now also have a look of how the Solidity/Yul code looks like:

```shell
$ make test-demo-yul | grep -v '//dbg:'
```

<details>
  <summary>Click here to see the generated Solidity/Yul code</summary>

```
yolc -m yul examples/demo:ERC20
object "ERC20" {
 code /* object init code */ {
  datacopy(0, dataoffset("runtime"), datasize("runtime"))

  // constructor
  {

  }

  return(0, datasize("runtime"))
 }

 object "runtime" {
  code {
   /* dispatcher */ {
    switch selector()
    case 0x70a08231 /* balanceOf(address) */ {
     let v_a, v_b
     v_a := __abidec_dispatcher_c_a(4, calldatasize())
     v_b := u$ERC20_20_25(v_a)
     let memPos := allocate_unbounded()
     let memEnd := __abienc_from_stack_c_u32(memPos, v_b)
     return(memPos, sub(memEnd, memPos))
    }
    case 0x40c10f19 /* mint(address,uint256) */ {
     let v_c, v_d
     v_c, v_d := __abidec_dispatcher_c_au32(4, calldatasize())
     u$ERC20_23_19(v_c, v_d)
     return(0, 0)
    }
    case 0xbeabacc8 /* transfer(address,address,uint256) */ {
     let v_e, v_f, v_g, v_h
     v_e, v_f, v_g := __abidec_dispatcher_c_aau32(4, calldatasize())
     v_h := u$ERC20_43_23(v_e, v_f, v_g)
     let memPos := allocate_unbounded()
     let memEnd := __abienc_from_stack_c_b(memPos, v_h)
     return(memPos, sub(memEnd, memPos))
    }
    default { revert(0, 0) }
   }
   // exported functions
   function u$ERC20_20_25(v_i) -> v_j {
    v_j := sload(u$ERC20_9_47(v_i))
    leave
   }

   function u$ERC20_23_19(v_a, v_b) {
    let memPos := allocate_unbounded()
    mstore(memPos, shl(224, 0x3676a601))
    let memEnd := __abienc_from_stack_c_u32(add(memPos, 4), v_b)
    let success := call(gas(), v_a, 0, memPos, sub(memEnd, memPos), memPos, 0)
    if iszero(success) { revert_forward_1() }
    if success {
     let rsize := 0
     if gt (rsize, returndatasize()) { rsize := returndatasize() }
     finalize_allocation(memPos, rsize)
    }
    sstore(u$ERC20_9_47(v_a), __checked_add_t_uint256(sload(u$ERC20_9_47(v_a)), v_b))
    leave
   }

   function u$ERC20_43_23(v_a, v_b, v_c) -> v_d {
    sstore(u$ERC20_9_47(v_a), __checked_sub_t_uint256(u$ERC20_20_25(v_a), v_c))
    sstore(u$ERC20_9_47(v_b), __checked_add_t_uint256(u$ERC20_20_25(v_b), v_c))
    v_d := true
    leave
   }

   // dependent functions
   function u$ERC20_9_47(v_a) -> v_b {
    v_b := __keccak_c_u32a(88805073310878547854323506196443343264722613599023821393996832376402040743314, v_a)
    leave
   }

   // builtin functions
   function __abidec_dispatcher_c_a(headStart, dataEnd) -> v_a {
    if slt(sub(dataEnd, headStart), 32) { revert(0, 0) }
    {
     let offset := 0
     v_a := __abidec_from_calldata_c1_a(add(headStart, offset), dataEnd)
    }
   }

   function __abidec_dispatcher_c_aau32(headStart, dataEnd) -> v_a, v_b, v_c {
    if slt(sub(dataEnd, headStart), 96) { revert(0, 0) }
    {
     let offset := 0
     v_a := __abidec_from_calldata_c1_a(add(headStart, offset), dataEnd)
    }
    {
     let offset := 32
     v_b := __abidec_from_calldata_c1_a(add(headStart, offset), dataEnd)
    }
    {
     let offset := 64
     v_c := __abidec_from_calldata_c1_u32(add(headStart, offset), dataEnd)
    }
   }

   function __abidec_dispatcher_c_au32(headStart, dataEnd) -> v_a, v_b {
    if slt(sub(dataEnd, headStart), 64) { revert(0, 0) }
    {
     let offset := 0
     v_a := __abidec_from_calldata_c1_a(add(headStart, offset), dataEnd)
    }
    {
     let offset := 32
     v_b := __abidec_from_calldata_c1_u32(add(headStart, offset), dataEnd)
    }
   }

   function __abidec_from_calldata_c1_a(offset, end) -> value {
    value := calldataload(offset)
    __validate_t_address(value)
   }

   function __abidec_from_calldata_c1_u32(offset, end) -> value {
    value := calldataload(offset)
    __validate_t_uint256(value)
   }

   function __abienc_from_stack_c1_a(pos, value) {
    mstore(pos, __cleanup_t_address(value))
   }

   function __abienc_from_stack_c1_b(pos, value) {
    mstore(pos, __cleanup_t_bool(value))
   }

   function __abienc_from_stack_c1_u32(pos, value) {
    mstore(pos, __cleanup_t_uint256(value))
   }

   function __abienc_from_stack_c_b(headStart, v_a) -> tail {
    tail := add(headStart, 32)
    __abienc_from_stack_c1_b(add(headStart, 0), v_a)
   }

   function __abienc_from_stack_c_u32(headStart, v_a) -> tail {
    tail := add(headStart, 32)
    __abienc_from_stack_c1_u32(add(headStart, 0), v_a)
   }

   function __abienc_from_stack_c_u32a(headStart, v_a, v_b) -> tail {
    tail := add(headStart, 64)
    __abienc_from_stack_c1_u32(add(headStart, 0), v_a)
    __abienc_from_stack_c1_a(add(headStart, 32), v_b)
   }

   function __checked_add_t_uint256(x, y) -> result {
    let failed := false
    result, failed := __safe_add_t_uint256(x, y)
    if eq(failed, true) { panic_error_0x11() }
   }

   function __checked_sub_t_uint256(x, y) -> result {
    let failed := false
    result, failed := __safe_sub_t_uint256(x, y)
    if eq(failed, true) { panic_error_0x11() }
   }

   function __cleanup_t_address(value) -> cleaned { cleaned := __cleanup_t_uint160(value) }

   function __cleanup_t_bool(value) -> cleaned { cleaned := iszero(iszero(value)) }

   function __cleanup_t_uint160(value) -> cleaned {
    cleaned := and(value, 0xffffffffffffffffffffffffffffffffffffffff)
   }

   function __cleanup_t_uint256(value) -> cleaned {
    cleaned := value
   }

   function __keccak_c_u32a(v_a, v_b) -> hash {
    let memPos := allocate_unbounded()
    let memEnd := __abienc_from_stack_c_u32a(memPos, v_a, v_b)
    hash := keccak256(memPos, sub(memEnd, memPos))
   }

   function __safe_add_t_uint256(x, y) -> sum, failed {
    x := __cleanup_t_uint256(x)
    y := __cleanup_t_uint256(y)
    sum := add(x, y)
    if gt(x, sum) { failed := true }
   }

   function __safe_sub_t_uint256(x, y) -> diff, failed {
     x := __cleanup_t_uint256(x)
     y := __cleanup_t_uint256(y)
     diff := sub(x, y)
     if gt(diff, x) { failed := true}
   }

   function selector() -> s {
    s := div(calldataload(0), 0x100000000000000000000000000000000000000000000000000000000)
   }

   function __validate_t_address(value) { if neq(value, __cleanup_t_address(value)) { revert(0, 0) } }

   function __validate_t_uint256(value) { if neq(value, __cleanup_t_uint256(value)) { revert(0, 0) } }

   function allocate_unbounded() -> memPtr { memPtr := mload(64) }

   function finalize_allocation(memPtr, size) {
    size := and(add(size, 31), not(31)) // round_up_to_mul_of_32
    let newFreePtr := add(memPtr, size)
    // protect against overflow
    if or(gt(newFreePtr, 0xffffffffffffffff), lt(newFreePtr, memPtr)) { panic_error_0x41() }
    mstore(64, newFreePtr)
   }

   function ge(a, b) -> r { r := iszero(lt(a, b)) }

   function le(a, b) -> r { r := iszero(gt(a, b)) }

   function neq(a, b) -> r { r := iszero(eq(a, b)) }

   function panic_error_0x11() {
     mstore(0, 0x4e487b7100000000000000000000000000000000000000000000000000000000)
     mstore(4, 0x11)
     revert(0, 0x24)
   }

   function panic_error_0x41() {
     mstore(0, 0x4e487b7100000000000000000000000000000000000000000000000000000000)
     mstore(4, 0x41)
     revert(0, 0x24)
   }

   function revert_forward_1() {
    let pos := allocate_unbounded()
    returndatacopy(pos, 0, returndatasize())
    revert(pos, returndatasize())
   }

   function sge(a, b) -> r { r := iszero(slt(a, b)) }

   function sle(a, b) -> r { r := iszero(sgt(a, b)) }
  }
 }

}
```
</details>

The code that produces this is under
[/examples/demo/src/ERC20.hs](https://github.com/yolc-dev/yul-dsl-monorepo/blob/master/examples/demo/src/ERC20.hs).

## Going Further

Find the best channel for you on [linktr.ee/yolc](https://linktr.ee/yolc) to follow the updates of the project. The project has
a road map and is still early, we can shape its direction together.

If you wish to understand the inner working of the project, please checkout its [code
base](https://github.com/yolc-dev/yul-dsl-monorepo) where the code are well commented with additional details inside
their README files.

## Help

Here are the [frequently asked questions](/docs/help/faq).
