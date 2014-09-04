Instruction Format
------------------

| Type | -- 4 -- | 1   | 1   | 1   | 1   | -- 4 -- | -- 4 -- | -- 4 -- | -- 4 -- | -- 4 -- | -- 4 -- |
| ---- | ------- | --- | --- | --- | --- | ------- | ------- | ------- | ------- | ------- | ------- |
| A    | op      | f   | s   | c   | z   | Rd      | Ra      | Rb      | sh      | -       | -       |
| B    | op      | f   | s   | c   | z   | Rd      | Ra      | Rb      |         |         | Rc      |
| C    | op      | f   | s   | c   | z   | Rd      | Ra      | imd     | -       | -       | -       |
| D    | op      | f   | s   | c   | z   | Rd      | imd     | -       | -       | -       | -       |


Instruction List
----------------

| Op  | Name | Type f=0 | Type f=1 | Description          | Operation f=0                       | Operation f=1                    |
| --- | ---- | -------- | -------- | -------------------- | ----------------------------------- | -------------------------------- |
| 0x0 | and  | A        | C        | bitwise and          | Rd := Ra and (Rb << sh)             | Rd := Ra and imd                 |
| 0x1 | andn | A        | C        | bitwise and with not | Rd := Ra and not (Rb << sh)         | Rd := Ra and not imd             |
| 0x2 | or   | A        | C        | bitwise or           | Rd := Ra or (Rb << sh)              | Rd := Ra or imd                  |
| 0x3 | xor  | A        | C        | bitwise xor          | Rd := Ra xor (Rb << sh)             | Rd := Ra xor imd                 |
| 0x4 | add  | A        | C        | integer add          | Rd := Ra + (Rb << sh)               | Rd := Ra + imd                   |
| 0x5 | sub  | A        | C        | integer sub          | Rd := Ra - (Rb << sh)               | Rd := Ra - imd                   |
| 0x6 | mul  | B        |          | multiply             | (Rc,Rd) := Ra * Rb                  |                                  |
| 0x7 | div  | B        |          | divide               | Rd := Ra / Rb ; Rc := Ra % Rb       |                                  |
| 0x8 | ld   | D        | C        | load aligned word    | Rd := mem[PC + imd]                 | Rd := mem[Rd + imd]              |
| 0x9 | st   | D        | C        | store aligned word   | mem[PC + imd] := Rd                 | mem[Rd + imd] := Rd              |
| 0xa | ldb  | D        | C        | load byte            | Rd := (Rd << 8) or mem[PC + imd]    | Rd := (Rd << 8) or mem[Rd + imd] |
| 0xb | stb  | D        | C        | store byte           | mem[PC + imd] := Rd ; Rd := Rd >> 8 | mem[Rd + imd] := Rd ; Rd >> 8    |
| 0xc | lea  | D        | C        | load address         | Rd := PC + imd                      | Rd := Ra + imd                   |
| 0xd | br   | D        | C        | branch               | PC := PC + imd                      | PC := Ra + imd                   |
| 0xe |      |          |          | undefined            |                                     |                                  |
| 0xf |      |          |          | undefined            |                                     |                                  |


Instruction Decoding
--------------------

| Op   | Name | Type | Reg Reads | Reg Writes | Mem Access | Operation                           |
| ---- | ---- | ---- | --------- | ---------- | ---------- | ----------------------------------- |
| 0x00 | and  | A    | 2         | 1          |            | Rd := Ra and (Rb << sh)             |
| 0x10 | andn | A    | 2         | 1          |            | Rd := Ra and not (Rb << sh)         |
| 0x20 | or   | A    | 2         | 1          |            | Rd := Ra or (Rb << sh)              |
| 0x30 | xor  | A    | 2         | 1          |            | Rd := Ra xor (Rb << sh)             |
| 0x40 | add  | A    | 2         | 1          |            | Rd := Ra + (Rb << sh)               |
| 0x50 | sub  | A    | 2         | 1          |            | Rd := Ra - (Rb << sh)               |
| 0x08 | and  | D    | 1         | 1          |            | Rd := Ra and imd                    |
| 0x18 | andn | D    | 1         | 1          |            | Rd := Ra and not imd                |
| 0x28 | or   | D    | 1         | 1          |            | Rd := Ra or imd                     |
| 0x38 | xor  | D    | 1         | 1          |            | Rd := Ra xor imd                    |
| 0x48 | add  | D    | 1         | 1          |            | Rd := Ra + imd                      |
| 0x58 | sub  | D    | 1         | 1          |            | Rd := Ra - imd                      |
| 0x60 | mul  | B    | 2         | 2          |            | (Rc,Rd) := Ra * Rb                  |
| 0x70 | div  | B    | 2         | 2          |            | Rd := Ra / Rb ; Rc := Ra % Rb       |
| 0x80 | ld   | D    | 0         | 1          | r align    | Rd := mem[PC + imd]                 |
| 0x90 | st   | D    | 1         | 0          | w alig     | mem[PC + imd] := Rd                 |
| 0xa0 | ldb  | D    | 1         | 1          | r byte     | Rd := (Rd << 8) or mem[PC + imd]    |
| 0xb0 | stb  | D    | 1         | 1          | w byte     | mem[PC + imd] := Rd ; Rd := Rd >> 8 |
| 0xc0 | lea  | D    | 0         | 1          |            | Rd := PC + imd                      |
| 0xd0 | br   | D    | 0         | 1          |            | PC := PC + imd                      |
| 0x81 | ld   | C    | 1         | 0          | r align    | Rd := mem[Rd + imd]                 |
| 0x91 | st   | C    | 2         | 1          | w align    | mem[Rd + imd] := Rd                 |
| 0xa1 | ldb  | C    | 2         | 1          | r byte     | Rd := (Rd << 8) or mem[Rd + imd]    |
| 0xb1 | stb  | C    | 2         | 1          | w byte     | mem[Rd + imd] := Rd ; Rd >> 8       |
| 0xc1 | lea  | C    | 1         | 1          |            | Rd := Ra + imd                      |
| 0xd1 | br   | C    | 1         | 1          |            | PC := Ra + imd                      |


