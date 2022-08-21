############
# Puzzle 7 #
############

```
00      36        CALLDATASIZE          ->  [0x05]
01      6000      PUSH1 00              ->  [0x00 0x05]
03      80        DUP1                  ->  [0x00 0x00 0x05]
04      37        CALLDATACOPY          ->  []
05      36        CALLDATASIZE          ->  [0x05]
06      6000      PUSH1 00              ->  [0x00 0x05]
08      6000      PUSH1 00              ->  [0x00 0x00 0x05]
0A      F0        CREATE                ->  [address]
0B      3B        EXTCODESIZE
0C      6001      PUSH1 01
0E      14        EQ
0F      6013      PUSH1 13
11      57        JUMPI
12      FD        REVERT
13      5B        JUMPDEST
14      00        STOP

? Enter the calldata: 
```

## Stack layout

| Line | Opcode | Name         | Stack              |
| ---- | ------ | ------------ | ------------------ |
| 00   | 36     | CALLDATASIZE | [size]             |
| 01   | 6000   | PUSH1 00     | [0x00, size]       |
| 03   | 80     | DUP1         | [0x00, 0x00, size] |
| 04   | 37     | CALLDATACOPY | []                 |
| 05   | 36     | CALLDATASIZE | [size]             |
| 06   | 6000   | PUSH1 00     | [0x00, size]       |
| 08   | 6000   | PUSH1 00     | [0x00, 0x00, size] |
| 0A   | F0     | CREATE       | [address]          |
| 0B   | 3B     | EXTCODESIZE  | [size]             |
| 0C   | 6001   | PUSH1 01     | [0x01, size]       |
| 0E   | 14     | EQ           | [0x01 == size]     |
| 0F   | 6013   | PUSH1 13     | [0x13, 0x01]       |

## Opcodes reference 

| OPCODE | NAME         | MINIMUM GAS | DESCRIPTION                                      | Stack output                                                                                                                                                                                                                                                                                                                         | Error cases                                                                                                                               |
| ------ | ------------ | ----------- | ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------- |
| 37     | CALLDATACOPY | 3           | Copy input data in current environment to memory | `1. destOffset`: byte offset in the memory where the result will be copied. `2. offset`: byte offset in the calldata to copy. `3. size`: byte size to copy.                                                                                                                                                                          | `1.` Not enough gas. `2.` Not enough values on the stack.                                                                                 |
| F0     | CREATE       | 32000       | Create a new account with associated code        | **Stack input**: `1. value`: value in wei to send to the new account. `2. offset`: byte offset in the memory in bytes, the initialisation code for the new account. `3. size`: byte size to copy (size of the initialisation code). - **Stack output**: `address`: the address of the deployed contract, 0 if the deployment failed. | `1.` Not enough gas. `2.` Not enough values on the stack. `3.` The current execution context is from a STATICCALL (since Byzantium fork). |
| 3B     | EXTCODESIZE  | 100         | Get size of an account’s code                    | **Stack input**: `1. address`: 20-byte address of the contract to query. - **Stack output**: `size`: byte size of the code.                                                                                                                                                                                                          | `1.` Not enough gas. `2.` Not enough values on the stack.                                                                                 |

## Solution

We must reach `STOP`. To make this happen we have:
1.  `JUMPI` must take a jump to `JUMPDEST`. From first opcode up to `JUMPDEST` is equal to *19* (beginning counting from **zero**). It will take this value from the top of the stack but also the *top - 1* of the stack must be *1*.
2.  The `EQ` must succeed (true) to push *1* but also the code will continue and push *0x13* that we want.
3.  `EXTCODESIZE` must be equal to *0x1*.
4.  The sequence for `CREATE` can be `PUSH1 1` `PUSH1 0` `RETURN` (`0x60016000f3`).

So, the input we need to give is **0x60016000f3**.

### Alternative solution

The sequence for `CREATE` can be `PUSH1 1` `PUSH1 0` `MSTORE8` `PUSH1 1` `PUSH1 0` `RETURN` (`0x600060005360016000F3`).

### Opcodes reference 

| OPCODE | NAME   | MINIMUM GAS | DESCRIPTION                          | Stack output                                                                                                                                                   | Error cases                                               |
| ------ | ------ | ----------- | ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| F3     | RETURN | 0           | Halt execution returning output data | `1. offset`: byte offset in the memory in bytes, to copy what will be the return data of this context. `2. size`: byte size to copy (size of the return data). | `1.` Not enough gas. `2.` Not enough values on the stack. |
| 53     | MSTORE8 | 3           | Save byte to memory | `1. offset`: byte offset in the memory in bytes. `2. value`: 1-byte value to write in the memory (the least significant byte of the 32-byte stack value). | `1.` Not enough gas. `2.` Not enough values on the stack. |

## Proof of Concept

```
? Enter the calldata: 0x60016000f3

Puzzle solved!

Run it in evm.codes: https://www.evm.codes/playground?callValue=0&unit=Wei&callData=0x60016000f3&codeType=Bytecode&code='36600080373660006000F03B600114601357FD5B00'_

? Do you want to play the next puzzle? (Y/n) 

```

```
? Enter the calldata: 0x600060005360016000F3

Puzzle solved!

Run it in evm.codes: https://www.evm.codes/playground?callValue=0&unit=Wei&callData=0x600060005360016000F3&codeType=Bytecode&code='36600080373660006000F03B600114601357FD5B00'_

? Do you want to play the next puzzle? (Y/n) 

```