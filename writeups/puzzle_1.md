############
# Puzzle 1 #
############

```
00      34      CALLVALUE
01      56      JUMP
02      FD      REVERT
03      FD      REVERT
04      FD      REVERT
05      FD      REVERT
06      FD      REVERT
07      FD      REVERT
08      5B      JUMPDEST
09      00      STOP

? Enter the value to send: (0) 
```

## Stack layout

| Line | Opcode | Name      | Stack   |
| ---- | ------ | --------- | ------- |
| 00   | 34     | CALLVALUE | [value] |

## Opcodes reference 

| OPCODE | NAME      | MINIMUM GAS | DESCRIPTION                                                                       | Stack output                                                                                                                                     | Error cases                                                                                                                                                        |
| ------ | --------- | ----------- | --------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 34     | CALLVALUE | 2           | Get deposited value by the instruction/transaction responsible for this execution | `value`: the value of the current call in wei.                                                                                                   | `1.` Not enough gas. `2.` Stack overflow.                                                                                                                          |
| 56     | JUMP      | 8           | Alter the program counter                                                         | `counter`: byte offset in the deployed code where execution will continue from. Must be a JUMPDEST instruction.                                  | `1.` Not enough gas. `2.` Not enough values on the stack. `3.` Counter offset is not a JUMPDEST. The error is generated even if the JUMP would not have been done. |
| FD     | REVERT    | 0           | Halt execution reverting state changes but returning data and remaining gas       | `1. offset`: byte offset in the memory in bytes. The return data of the calling context. `2. size`: byte size to copy (size of the return data). | `1.` Not enough gas. `2.` Not enough values on the stack.                                                                                                          |
| 5B     | JUMPDEST  | 1           | Mark a valid destination for jumps                                                | -                                                                                                                                                | -                                                                                                                                                                  |
| 00     | STOP      | 0           | Halts execution                                                                   | -                                                                                                                                                | -                                                                                                                                                                  |


## Solution

We must reach `STOP`. To make this happen we have:
1.  `JUMP` must take a jump to `JUMPDEST`. From first opcode up to `JUMPDEST` is equal to *8* (beginning counting from **zero**). It will take this value from the stack.
2.  `CALLVALUE` is pushing to the stack the value we give.

So, the input we need to give is **8**.

## Proof of Concept

```
? Enter the value to send: 8

Puzzle solved!

Run it in evm.codes: https://www.evm.codes/playground?callValue=8&unit=Wei&callData=&codeType=Bytecode&code='3456FDFDFDFDFDFD5B00'_

? Do you want to play the next puzzle? (Y/n) 
```