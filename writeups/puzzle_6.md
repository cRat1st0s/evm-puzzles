############
# Puzzle 6 #
############

```
00      6000      PUSH1 00
02      35        CALLDATALOAD
03      56        JUMP
04      FD        REVERT
05      FD        REVERT
06      FD        REVERT
07      FD        REVERT
08      FD        REVERT
09      FD        REVERT
0A      5B        JUMPDEST
0B      00        STOP

? Enter the calldata: 
```

## Stack layout

| Line | Opcode | Name         | Stack     |
| ---- | ------ | ------------ | --------- |
| 00   | 6000   | PUSH1 00     | [0x00]    |
| 01   | 35     | CALLDATALOAD | [data[i]] |

## Opcodes reference 

| OPCODE | NAME         | MINIMUM GAS | DESCRIPTION                           | Stack output                                                                                                                                                                                          | Error cases                                               |
| ------ | ------------ | ----------- | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| 35     | CALLDATALOAD | 3           | Get input data of current environment | **Stack input**: `i`: byte offset in the calldata. - **Stack output**: `data[i]`: 32-byte value starting from the given offset of the calldata. All bytes after the end of the calldata are set to 0. | `1.` Not enough gas. `2.` Not enough values on the stack. |

## Solution

We must reach `STOP`. To make this happen we have:
1.  `JUMP` must take a jump to `JUMPDEST`. From first opcode up to `JUMPDEST` is equal to *10* (beginning counting from **zero**). It will take this value from the top of the stack.
2.  `CALLDATALOAD` is a **32-byte** value.

So, the input we need to give is **0x000000000000000000000000000000000000000000000000000000000000000a**.

## Proof of Concept

```
? Enter the calldata: 0x000000000000000000000000000000000000000000000000000000000000000a

Puzzle solved!

Run it in evm.codes: https://www.evm.codes/playground?callValue=0&unit=Wei&callData=0x000000000000000000000000000000000000000000000000000000000000000a&codeType=Bytecode&code='60003556FDFDFDFDFDFD5B00'_

? Do you want to play the next puzzle? (Y/n) 

```