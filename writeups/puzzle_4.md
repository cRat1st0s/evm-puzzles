############
# Puzzle 4 #
############

```
00      34      CALLVALUE
01      38      CODESIZE
02      18      XOR
03      56      JUMP
04      FD      REVERT
05      FD      REVERT
06      FD      REVERT
07      FD      REVERT
08      FD      REVERT
09      FD      REVERT
0A      5B      JUMPDEST
0B      00      STOP

? Enter the value to send: (0) 
```

## Stack layout

| Line | Opcode | Name      | Stack          |
| ---- | ------ | --------- | -------------- |
| 00   | 34     | CALLVALUE | [value]        |
| 01   | 38     | CODESIZE  | [0x0c, value]  |
| 02   | 18     | XOR       | [0x0c ^ value] |

## Opcodes reference 

| OPCODE | NAME | MINIMUM GAS | DESCRIPTION           | Stack output                                                                                                             | Error cases                                               |
| ------ | ---- | ----------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------- |
| 18     | XOR  | 3           | Bitwise XOR operation | **Stack input**: `a`: first binary value. `b`: second binary value. - **Stack output**: `a ^ b`: the bitwise XOR result. | `1.` Not enough gas. `2.` Not enough values on the stack. |

## Solution

We must reach `STOP`. To make this happen we have:
1.  `JUMP` must take a jump to `JUMPDEST`. From first opcode up to `JUMPDEST` is equal to *10* (beginning counting from **zero**). It will take this value from the stack.
2.  `CODESIZE` is *12*.

So, the input we need to give is **6** (`0xc XOR 0xa = 0x6`).

## Proof of Concept

```
? Enter the value to send: 6

Puzzle solved!

Run it in evm.codes: https://www.evm.codes/playground?callValue=6&unit=Wei&callData=&codeType=Bytecode&code='34381856FDFDFDFDFDFD5B00'_

? Do you want to play the next puzzle? (Y/n) 

```