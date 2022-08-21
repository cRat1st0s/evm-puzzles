############
# Puzzle 5 #
############

```
00      34          CALLVALUE
01      80          DUP1
02      02          MUL
03      610100      PUSH2 0100
06      14          EQ
07      600C        PUSH1 0C
09      57          JUMPI
0A      FD          REVERT
0B      FD          REVERT
0C      5B          JUMPDEST
0D      00          STOP
0E      FD          REVERT
0F      FD          REVERT

? Enter the value to send: (0) 
```

## Stack layout

| Line | Opcode | Name       | Stack                   |
| ---- | ------ | ---------- | ----------------------- |
| 00   | 34     | CALLVALUE  | [value]                 |
| 01   | 80     | DUP1       | [value, value]          |
| 02   | 02     | MUL        | [value * value]         |
| 03   | 610100 | PUSH2 0100 | [0x0100, value * value] |
| 06   | 14     | EQ         | [0x0100 == value]       |
| 07   | 600C   | PUSH1 0C   | [0x0c, 0x01]            |

## Opcodes reference 

| OPCODE                                                                                                                                                                                                 | NAME                                                                                                                                                           | MINIMUM GAS | DESCRIPTION                             | Stack output                                                                                                                                                                  | Error cases                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- | --------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| 80                                                                                                                                                                                                     | DUP1                                                                                                                                                           | 3           | Duplicate 1st stack item                | **Stack input**: `value`: value to duplicate. - **Stack output**: `1. value`: duplicated value. `2. value`: original value.                                                   | `1.` Not enough gas. `2.` Not enough values on the stack. `3.` Stack overflow. |
| 02                                                                                                                                                                                                     | MUL                                                                                                                                                            | 5           | Multiplication operation                | **Stack input**: `a`: first integer value to multiply. `b`: second integer value to multiply. - **Stack output**: `a * b`: integer result of the multiplication modulo 2^256. | `1.` Not enough gas. `2.` Not enough values on the stack.                      |
| 61                                                                                                                                                                                                     | PUSH2                                                                                                                                                          | 3           | Place 2 byte item on stack              | `value`: pushed value, aligned to the right (put in the lowest significant bytes).                                                                                            | `1.` Not enough gas. `2.` Stack overflow.                                      |
| 14                                                                                                                                                                                                     | EQ                                                                                                                                                             | 3           | Equality comparison                     | **Stack input**: `a`: left side integer. `b`: right side integer. - **Stack output**: `a == b`: 1 if the left side is equal to the right side, 0 otherwise.                   | `1.` Not enough gas. `2.` Not enough values on the stack.                      |
| 60                                                                                                                                                                                                     | PUSH1                                                                                                                                                          | 3           | Place 1 byte item on stack              | `value`: pushed value, aligned to the right (put in the lowest significant bytes).                                                                                            | `1.` Not enough gas. `2.` Stack overflow.                                      |
| 57                                                                                                                                                                                                     | JUMPI                                                                                                                                                          | 10          | Conditionally alter the program counter | `1. counter`: byte offset in the deployed code where execution will continue from. Must be a JUMPDEST instruction.                                                            |
| `2. b`: the program counter will be altered with the new value only if this value is different from 0. Otherwise, the program counter is simply incremented and the next instruction will be executed. | `1.` Not enough gas. `2.` Not enough values on the stack. `3.` Counter offset is not a JUMPDEST. The error is generated only if the JUMP would have been done. |

## Solution

We must reach `STOP`. To make this happen we have:
1.  `JUMPI` must take a jump to `JUMPDEST`. From first opcode up to `JUMPDEST` is equal to *12* (beginning counting from **zero**). It will take this value from the top of the stack but also the *top - 1* of the stack must be *1*.
2.  The `EQ` must succeed (true) to push *1* but also the code will continue and push *0xc* that we want.
3.  `CALLVALUE` * `CALLVALUE` = 0x100.

So, the input we need to give is **16**.

## Proof of Concept

```
? Enter the value to send: 16

Puzzle solved!

Run it in evm.codes: https://www.evm.codes/playground?callValue=16&unit=Wei&callData=&codeType=Bytecode&code='34800261010014600C57FDFD5B00FDFD'_

? Do you want to play the next puzzle? (Y/n) 

```