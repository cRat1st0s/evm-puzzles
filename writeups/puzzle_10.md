#############
# Puzzle 10 #
#############

```
00      38          CODESIZE
01      34          CALLVALUE
02      90          SWAP1
03      11          GT
04      6008        PUSH1 08
06      57          JUMPI
07      FD          REVERT
08      5B          JUMPDEST
09      36          CALLDATASIZE
0A      610003      PUSH2 0003
0D      90          SWAP1
0E      06          MOD
0F      15          ISZERO
10      34          CALLVALUE
11      600A        PUSH1 0A
13      01          ADD
14      57          JUMPI
15      FD          REVERT
16      FD          REVERT
17      FD          REVERT
18      FD          REVERT
19      5B          JUMPDEST
1A      00          STOP

? Enter the value to send: (0) 

```

## Stack layout

| Line | Opcode | Name         | Stack                |
| ---- | ------ | ------------ | -------------------- |
| 00   | 38     | CODESIZE     | [size]               |
| 01   | 34     | CALLVALUE    | [value, size]        |
| 02   | 90     | SWAP1        | [size, value]        |
| 03   | 11     | GT           | [size > value]       |
| 04   | 6008   | PUSH1 08     | [0x08, 0x01]         |
| 09   | 38     | CALLDATASIZE | [size]               |
| 0A   | 610003 | PUSH2 0003   | [0x0003, size]       |
| 0D   | 90     | SWAP1        | [size, 0x0003]       |
| 0E   | 06     | MOD          | [size % 0x0003]      |
| 0F   | 15     | ISZERO       | [size % 0x0003 == 0] |
| 10   | 34     | CALLVALUE    | [value, 0x01]              |
| 11   | 600A   | PUSH1 0A     | [0x0A, value, 0x??]        |
| 13   | 01     | ADD          | [0x0A + value, 0x??]       |

## Opcodes reference 

| OPCODE | NAME   | MINIMUM GAS | DESCRIPTION                      | Stack output                                                                                                                                                                               | Error cases                                               |
| ------ | ------ | ----------- | -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------- |
| 90     | SWAP1  | 3           | Exchange 1st and 2nd stack items | **Stack input**: `1. a`: value to swap. `2. b`: value to swap. - **Stack output**: `1. b`: swapped value. `2. a`: swapped value.                                                           | `1.` Not enough gas. `2.` Not enough values on the stack. |
| 11     | GT     | 3           | Greater-than comparison          | **Stack input**: `1. a`: left side integer. `2. b`: right side integer. - **Stack output**: `a > b`: 1 if the left side is smaller, 0 otherwise.                                           | `1.` Not enough gas. `2.` Not enough values on the stack. |
| 06     | MOD    | 5           | Modulo remainder operation       | **Stack input**: `1. a`: integer numerator. `2. b`: integer denominator. - **Stack output**: `a % b`: integer result of the integer modulo. If the denominator is 0, the result will be 0. | `1.` Not enough gas. `2.` Not enough values on the stack. |
| 0F     | ISZERO | 3           | Simple not operator              | **Stack input**: `1. a`: integer. - **Stack output**: `a == 0`: 1 if a is 0, 0 otherwise.                                                                                                  | `1.` Not enough gas. `2.` Not enough values on the stack. |
| 13     | ADD    | 3           | Addition operation               | **Stack input**: `1. a`: first integer value to add. `2. b`: second integer value to add. - **Stack output**: `a + b`: integer result of the addition modulo 2^256.                        | `1.` Not enough gas. `2.` Not enough values on the stack. |

## Solution

We must reach `STOP`. To make this happen we have:
1.  `JUMPI` must take a jump to `JUMPDEST`. From first opcode up to first `JUMPDEST` is equal to *8* (beginning counting from **zero**) and to the second `JUMPDEST` is equal to *25*. It will take this value from the top of the stack but also the *top - 1* of the stack must be *1*.
2.  The `GT` must return true to push *1* but also the code will continue and push *0x08* that we want.
3.  `0x0A + value = 0x19` => `value = 0x0F`
4.  `size % 3 = 0`
5.  `size > value` => `size > 0x0F`

So, the input we can give is **size = 0x03 && value = 0x0f** or **size = 0x06 && value = 0x0f** and etc.

## Proof of Concept

```
? Enter the value to send: 15
? Enter the calldata: 0x000000

Puzzle solved!

Run it in evm.codes: https://www.evm.codes/playground?callValue=15&unit=Wei&callData=0x000000&codeType=Bytecode&code='38349011600857FD5B3661000390061534600A0157FDFDFDFD5B00'_

? Do you want to play the next puzzle? Yes

All puzzles are solved!
```

```
? Enter the value to send: 15
? Enter the calldata: 0x000000000000

Puzzle solved!

Run it in evm.codes: https://www.evm.codes/playground?callValue=15&unit=Wei&callData=0x000000000000&codeType=Bytecode&code='38349011600857FD5B3661000390061534600A0157FDFDFDFD5B00'_

? Do you want to play the next puzzle? Yes

All puzzles are solved!
```