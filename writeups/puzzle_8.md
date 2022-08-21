############
# Puzzle 8 #
############

```
00      36        CALLDATASIZE
01      6000      PUSH1 00
03      80        DUP1
04      37        CALLDATACOPY
05      36        CALLDATASIZE
06      6000      PUSH1 00
08      6000      PUSH1 00
0A      F0        CREATE
0B      6000      PUSH1 00
0D      80        DUP1
0E      80        DUP1
0F      80        DUP1
10      80        DUP1
11      94        SWAP5
12      5A        GAS
13      F1        CALL
14      6000      PUSH1 00
16      14        EQ
17      601B      PUSH1 1B
19      57        JUMPI
1A      FD        REVERT
1B      5B        JUMPDEST
1C      00        STOP

? Enter the calldata: 
```

## Stack layout

| Line | Opcode | Name         | Stack                                        |
| ---- | ------ | ------------ | -------------------------------------------- |
| 00   | 36     | CALLDATASIZE | [size]                                       |
| 01   | 6000   | PUSH1 00     | [0x00, size]                                 |
| 03   | 80     | DUP1         | [0x00, 0x00, size]                           |
| 04   | 37     | CALLDATACOPY | []                                           |
| 05   | 36     | CALLDATASIZE | [size]                                       |
| 06   | 6000   | PUSH1 00     | [0x00, size]                                 |
| 08   | 6000   | PUSH1 00     | [0x00, 0x00, size]                           |
| 0A   | F0     | CREATE       | [address]                                    |
| 0B   | 6000   | PUSH1 00     | [0x00, address]                              |
| 0D   | 80     | DUP1         | [0x00, 0x00, address]                        |
| 0E   | 80     | DUP1         | [0x00, 0x00, 0x00, address]                  |
| 0F   | 80     | DUP1         | [0x00, 0x00, 0x00, 0x00, address]            |
| 10   | 80     | DUP1         | [0x00, 0x00, 0x00, 0x00, 0x00, address]      |
| 11   | 94     | SWAP5        | [address, 0x00, 0x00, 0x00, 0x00, 0x00]      |
| 12   | 5A     | GAS          | [gas, address, 0x00, 0x00, 0x00, 0x00, 0x00] |
| 13   | F1     | CALL         | [success]                                    |
| 14   | 6000   | PUSH1 00     | [0x00, success]                              |
| 16   | 14     | EQ           | [0x00 == success]                            |
| 17   | 601B   | PUSH1 1B     | [0x1b, 0x01]                                 |

## Opcodes reference 

| OPCODE | NAME  | MINIMUM GAS | DESCRIPTION                                                                                             | Stack output                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Error cases                                                                                                                                                                      |
| ------ | ----- | ----------- | ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 94     | SWAP5 | 3           | Exchange 1st and 6th stack items                                                                        | **Stack input**: `1. a`: value to swap. `2.` ignored value. `3.` ignored value. `4.` ignored value. `5.` ignored value. `6. b`: value to swap. - **Stack output**: `1. b`: swapped value. `2.` ignored value. `3.` ignored value. `4.` ignored value. `5.` ignored value. `6. a`: swapped value.                                                                                                                                                                                                                                                                                                                                                             | `1.` Not enough gas. `2.` Not enough values on the stack.                                                                                                                        |
| 5A     | GAS   | 2           | Get the amount of available gas, including the corresponding reduction for the cost of this instruction | `gas`: remaining gas (after this instruction).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | `1.` Not enough gas. `2.` Stack overflow.                                                                                                                                        |
| F1     | CALL  | 100         | Message-call into an account                                                                            | **Stack input**: `1. gas`: amount of gas to send to the sub context to execute. The gas that is not used by the sub context is returned to this one. `2. address`: the account which context to execute. `3. value`: value in wei to send to the account. `4. argsOffset`: byte offset in the memory in bytes, the calldata of the sub context. `5. argsSize`: byte size to copy (size of the calldata). `6. retOffset`: byte offset in the memory in bytes, where to store the return data of the sub context. `7. retSize`: byte size to copy (size of the return data). - **Stack output**: `success`: return 0 if the sub context reverted, 1 otherwise. | `1.` Not enough gas. `2.` Not enough values on the stack. `3.` The current execution context is from a STATICCALL and the value (stack index 2) is not 0 (since Byzantium fork). |

## Solution

We must reach `STOP`. To make this happen we have:
1.  `JUMPI` must take a jump to `JUMPDEST`. From first opcode up to `JUMPDEST` is equal to *27* (beginning counting from **zero**). It will take this value from the top of the stack but also the *top - 1* of the stack must be *1*.
2.  The `EQ` must succeed (true) to push *1* but also the code will continue and push *0x1b* that we want.
3.  `CALL` must be equal to *0x00*.
4.  Opcode for `REVERT` is **0xfd**.
5.  The sequence for `CREATE` can be `PUSH1 FD` `PUSH1 0` `MSTORE8` `PUSH1 1` `PUSH1 0` `RETURN` (`0x60FD60005360016000F3`).

So, the input we need to give is **0x60FD60005360016000F3**.

## Proof of Concept

```
? Enter the calldata: 0x60FD60005360016000F3

Puzzle solved!

Run it in evm.codes: https://www.evm.codes/playground?callValue=0&unit=Wei&callData=0x60FD60005360016000F3&codeType=Bytecode&code='36600080373660006000F0600080808080945AF1600014601B57FD5B00'_

? Do you want to play the next puzzle? (Y/n) 

```