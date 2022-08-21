############
# Puzzle 3 #
############

```
00      36      CALLDATASIZE
01      56      JUMP
02      FD      REVERT
03      FD      REVERT
04      5B      JUMPDEST
05      00      STOP

? Enter the calldata: 
```

## Stack layout

| Line | Opcode | Name         | Stack  |
| ---- | ------ | ------------ | ------ |
| 00   | 36     | CALLDATASIZE | [size] |

## Opcodes reference 

| OPCODE | NAME         | MINIMUM GAS | DESCRIPTION                                     | Stack output                       | Error cases                               |
| ------ | ------------ | ----------- | ----------------------------------------------- | ---------------------------------- | ----------------------------------------- |
| 36     | CALLDATASIZE | 2           | Get size of code running in current environment | `size`: byte size of the calldata. | `1.` Not enough gas. `2.` Stack overflow. |

## Solution

We must reach `STOP`. To make this happen we have:
1.  `JUMP` must take a jump to `JUMPDEST`. From first opcode up to `JUMPDEST` is equal to *4* (beginning counting from **zero**). It will take this value from the stack.
2.  `CALLDATASIZE` is pushing to the stack the size of the calldata. The **size** must be *4*.

So, the input we need to give can be **0xdeadbeef**.

## Proof of Concept

```
? Enter the calldata: 0xdeadbeef

Puzzle solved!

Run it in evm.codes: https://www.evm.codes/playground?callValue=0&unit=Wei&callData=0xdeadbeef&codeType=Bytecode&code='3656FDFD5B00'_

? Do you want to play the next puzzle? (Y/n) 

```