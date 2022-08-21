############
# Puzzle 9 #
############

```
00      36        CALLDATASIZE
01      6003      PUSH1 03
03      10        LT
04      6009      PUSH1 09
06      57        JUMPI
07      FD        REVERT
08      FD        REVERT
09      5B        JUMPDEST
0A      34        CALLVALUE
0B      36        CALLDATASIZE
0C      02        MUL
0D      6008      PUSH1 08
0F      14        EQ
10      6014      PUSH1 14
12      57        JUMPI
13      FD        REVERT
14      5B        JUMPDEST
15      00        STOP

? Enter the value to send: (0) 

```

## Stack layout

| Line | Opcode | Name         | Stack                  |
| ---- | ------ | ------------ | ---------------------- |
| 00   | 36     | CALLDATASIZE | [size]                 |
| 01   | 6003   | PUSH1 03     | [0x03, size]           |
| 03   | 10     | LT           | [0x03 < size]          |
| 04   | 6009   | PUSH1 09     | [0x09, 0x1]            |
| 0A   | 34     | CALLVALUE    | [value]                |
| 0B   | 36     | CALLDATASIZE | [size, value]          |
| 0C   | 02     | MUL          | [size * value]         |
| 0D   | 6008   | PUSH1 08     | [0x08, size * value]   |
| 0F   | 14     | EQ           | [0x08 == size * value] |
| 10   | 6014   | PUSH1 14     | [0x14, 0x01]           |


## Opcodes reference 

| OPCODE | NAME | MINIMUM GAS | DESCRIPTION          | Stack output                                                                                                                                     | Error cases                                               |
| ------ | ---- | ----------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------- |
| 10     | LT   | 3           | Less-than comparison | **Stack input**: `1. a`: left side integer. `2. b`: right side integer. - **Stack output**: `a < b`: 1 if the left side is smaller, 0 otherwise. | `1.` Not enough gas. `2.` Not enough values on the stack. |

## Solution

We must reach `STOP`. To make this happen we have:
1.  `JUMPI` must take a jump to `JUMPDEST`. From first opcode up to first `JUMPDEST` is equal to *9* (beginning counting from **zero**) and to the second `JUMPDEST` is equal to *20*. It will take this value from the top of the stack but also the *top - 1* of the stack must be *1*.
2.  The `LT` must return true to push *1* but also the code will continue and push *0x09* that we want.
3.  The `EQ` must succeed (true) to push *1* but also the code will continue and push *0x14* that we want.
4.  `size > 0x03` && `value * size = 0x08`. 

So, the input we can give is **size = 0x04 && value = 0x02** or **size = 0x08 && value = 0x01**.

## Proof of Concept

```
? Enter the value to send: 2
? Enter the calldata: 0x00000000

Puzzle solved!

Run it in evm.codes: https://www.evm.codes/playground?callValue=2&unit=Wei&callData=0x00000000&codeType=Bytecode&code='36600310600957FDFD5B343602600814601457FD5B00'_

? Do you want to play the next puzzle? (Y/n) 

```

```
? Enter the value to send: 1
? Enter the calldata: 0x0000000000000000

Puzzle solved!

Run it in evm.codes: https://www.evm.codes/playground?callValue=1&unit=Wei&callData=0x0000000000000000&codeType=Bytecode&code='36600310600957FDFD5B343602600814601457FD5B00'_

? Do you want to play the next puzzle? (Y/n) 

```