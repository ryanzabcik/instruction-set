Instruction Format
------------------

| Type | -- 4 -- | 1   | 3   | 3   | 3   | 2   |
| ---- | ------- | --- | --- | --- | --- | --- |
| A    | op      | f   | Rd  | Ra  | Rd  | sh  |
| B    | op      | f   | Rd  | Ra  | imd | -   |
| C    | op      | f   | Rd  | imd | -   | -   |


Instruction List
----------------

| Op  | Name | Type f=0 | Type f=1 | Description          | Operation f=1                    | Operation f=0                       |
| --- | ---- | -------- | -------- | -------------------- | -------------------------------- | ----------------------------------- |
| 0x0 | or   | B        | A        | bitwise or           | Rd := Ra or imd                  | Rd := Ra or (Rb << sh)              |
| 0x1 | xor  | B        | A        | bitwise xor          | Rd := Ra xor imd                 | Rd := Ra xor (Rb << sh)             |
| 0x2 | and  | B        | A        | bitwise and          | Rd := Ra and imd                 | Rd := Ra and (Rb << sh)             |
| 0x3 | andn | B        | A        | bitwise and with not | Rd := Ra and not imd             | Rd := Ra and not (Rb << sh)         |
| 0x4 | add  | B        | A        | integer add          | Rd := Ra + imd                   | Rd := Ra + (Rb << sh)               |
| 0x5 | sub  | B        | A        | integer sub          | Rd := Ra - imd                   | Rd := Ra - (Rb << sh)               |
| 0x6 | slt  | B        | A        | set if less          | Rd := Ra < imd                   | Rd := Ra < (Rb << sh)               |
| 0x7 | sltu | B        | A        | set if unsigned less | Rd := Ra < imd                   | Rd := Ra < (Rb << sh)               |
| 0x8 | mul  | B        | A        | multiply             | (Rd,Ra) := Ra * imd              | (Rd,Ra) := Ra * Rb                  |
| 0x9 | div  | B        | A        | divide               | Ra := Ra / Rb ; Rd := Ra % imd   | Ra := Ra / Rb ; Rd := Ra % Rb       |
| 0xa | ld   | B        | C        | load word            | Rd := mem[Rd + imd<<1]           | Rd := mem[PC + imd<<1]              |
| 0xb | st   | B        | C        | store word           | mem[Rd + imd<<1] := Rd           | mem[PC + imd<<1] := Rd              |
| 0xc | lea  | B        | C        | load address         | Rd := Rd + imd<<1                | Rd := PC + imd<<1                   |
| 0xd | call | B        | C        | branch and link      | Rd := PC+2 ; PC := Rd + imd<<1   | Rd := PC+2 ; PC := PC + imd<<1      |
| 0xe | breq | B        | C        | branch if zero       | PC := Rd + imd<<1 if Rd == 0     | PC := PC + imd<<1 if Rd == 0        |
| 0xf | brne | B        | C        | branch if not zero   | PC := Rd + imd<<1 if Rd != 0     | PC := PC + imd<<1 if Rd != 0        |


Instruction Decoding
--------------------

| Op   | Name | Type | Reg Reads | Reg Writes | Mem Access | Operation                           |
| ---- | ---- | ---- | --------- | ---------- | ---------- | ----------------------------------- |
| 0x00 | or   | B    | 1         | 1          |            | Rd := Ra or imd                     |
| 0x10 | xor  | B    | 1         | 1          |            | Rd := Ra xor imd                    |
| 0x20 | and  | B    | 1         | 1          |            | Rd := Ra and imd                    |
| 0x30 | andn | B    | 1         | 1          |            | Rd := Ra and not imd                |
| 0x40 | add  | B    | 1         | 1          |            | Rd := Ra + imd                      |
| 0x50 | sub  | B    | 1         | 1          |            | Rd := Ra - imd                      |
| 0x60 | slt  | B    | 1         | 1          |            | Rd := Ra < imd                      |
| 0x70 | sltu | B    | 1         | 1          |            | Rd := Ra < imd                      |
| 0x08 | or   | A    | 2         | 1          |            | Rd := Ra or (Rb << sh)              |
| 0x18 | xor  | A    | 2         | 1          |            | Rd := Ra xor (Rb << sh)             |
| 0x28 | and  | A    | 2         | 1          |            | Rd := Ra and (Rb << sh)             |
| 0x38 | andn | A    | 2         | 1          |            | Rd := Ra and not (Rb << sh)         |
| 0x48 | add  | A    | 2         | 1          |            | Rd := Ra + (Rb << sh)               |
| 0x58 | sub  | A    | 2         | 1          |            | Rd := Ra - (Rb << sh)               |
| 0x68 | slt  | A    | 2         | 1          |            | Rd := Ra < (Rb << sh)               |
| 0x78 | sltu | A    | 2         | 1          |            | Rd := Ra < (Rb << sh)               |
| 0x80 | mul  | B    | 1         | 2          |            | (Rd,Ra) := Ra * imd                 |
| 0x90 | div  | B    | 1         | 2          |            | Ra := Ra / Rb ; Rd := Ra % imd      |
| 0x88 | mul  | A    | 2         | 2          |            | (Rd,Ra) := Ra * Rb                  |
| 0x98 | div  | A    | 2         | 2          |            | Ra := Ra / Rb ; Rd := Ra % Rb       |
| 0xa0 | ld   | B    | 1         | 1          | r align    | Rd := mem[Rd + imd<<1]              |
| 0xb0 | st   | B    | 2         | 0          | w align    | mem[Rd + imd<<1] := Rd              |
| 0xc0 | lea  | B    | 2         | 1          |            | Rd := Rd + imd<<1                   |
| 0xd0 | call | B    | 1         | 1          |            | Rd := PC+2 ; PC := Rd + imd<<1      |
| 0xe0 | breq | B    | 1         | 0          |            | PC := Rd + imd<<1 if Rd == 0        |
| 0xf0 | brne | B    | 1         | 0          |            | PC := Rd + imd<<1 if Rd != 0        |
| 0xa8 | ld   | C    | 0         | 1          | r align    | Rd := mem[PC + imd<<1]              |
| 0xb8 | st   | C    | 1         | 0          | w align    | mem[PC + imd<<1] := Rd              |
| 0xc8 | lea  | C    | 1         | 1          |            | Rd := PC + imd<<1                   |
| 0xd8 | call | C    | 0         | 1          |            | Rd := PC+2 ; PC := PC + imd<<1      |
| 0xe8 | breq | C    | 1         | 0          |            | PC := PC + imd<<1 if Rd == 0        |
| 0xf8 | brne | C    | 1         | 0          |            | PC := PC + imd<<1 if Rd != 0        |


