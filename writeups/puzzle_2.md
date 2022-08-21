############
# Puzzle 2 #
############

```
00      34      CALLVALUE
01      38      CODESIZE
02      03      SUB
03      56      JUMP
04      FD      REVERT
05      FD      REVERT
06      5B      JUMPDEST
07      00      STOP
08      FD      REVERT
09      FD      REVERT

? Enter the value to send: (0) 
```

## Stack layout

| Line | Opcode | Name      | Stack         |
| ---- | ------ | --------- | ------------- |
| 00   | 34     | CALLVALUE | [value]       |
| 01   | 38     | CODESIZE  | [0x0a, value] |

## Opcodes reference 

| OPCODE | NAME     | MINIMUM GAS | DESCRIPTION                                     | Stack output                                                                                                                                                         | Error cases                                               |
| ------ | -------- | ----------- | ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| 38     | CODESIZE | 2           | Get size of code running in current environment | `size`: byte size of the code.                                                                                                                                       | `1.` Not enough gas. `2.` Stack overflow.                 |
| 03     | SUB      | 3           | Subtraction operation                           | **Stack input**: `a`: first integer value. `b`: integer value to subtract to the first. - **Stack output**: `a - b`: integer result of the subtraction modulo 2^256. | `1.` Not enough gas. `2.` Not enough values on the stack. |

## Solution

We must reach `STOP`. To make this happen we have:
1.  `JUMP` must take a jump to `JUMPDEST`. From first opcode up to `JUMPDEST` is equal to *6* (beginning counting from **zero**). It will take this value from the stack.
2.  `SUB` is pushing to the stack the result of the subtraction. This result must be *4*.
3.  `CODESIZE` is *10*.

So, the input we need to give is **4** (`10 - 6`).

## Proof of Concept

```
? Enter the value to send: 4

Puzzle solved!

Run it in evm.codes: https://www.evm.codes/playground?callValue=4&unit=Wei&callData=&codeType=Bytecode&code='34380356FDFD5B00FDFD'_

? Do you want to play the next puzzle? (Y/n) 

```