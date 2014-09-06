# C32 Instruction Set

## How do we do function calls?

The branch instruction stores the old PC in Rd. Any functions that
don't need the assigned register can just leave the return address in
the register and jump to it when done. On top of that we could simply
use the ARM calling convention: store the first 8 arguments in
registers, and if we need more arguments / varargs just pass in a
pointer and length to the actual arguments which may just point to the
stack.

How many register file read/write ports (maximum number of registers
read / written per cycle) For Type A, C and D instructions, at most 2
registers are read, and 1 register is written. For Type B instructions
(mul, div, and xchg), 2 registers are read and 2 registers are
written. These three instructions are all optional so we could drop
them. However, it doesn't make sense for any of these instructions to
write to the same register, so we could leave the results in that case
undefined and perform both writes in a single cycle.

On top of that, the SR can be written with condition codes each
instruction, however it is a readonly register, so this can also be
accomplished in the same cycle as instruction execution.

## How many memory read/write ports?

Just 1 exclusive read/write port. Aside from instruction fetches, only
the ld, ldb, st, stb instructions can access memory for either loading
or storing. We could even drop the ldb/stb instruction for only 32bit
read/writes.

How do we implement arrays / structures / pointers / function
pointers?

 - Array look ups generally compile as such a[b] => *(a + (b <<
   sizeof(b)). This can be encoded into the shift component of each
   instruction and find the offset in a single instruction. An
   additional instruction is then needed for the actual load or store.
   
 - Structures are generally compiled as such a->b => *(a +
   offsetof(b)). This can be encoded at compile time into the
   immediate offset of register based load/store instructions.
   
 - Pointers can simple be stored in registers and accessed with the
   register based load/store instructions.  Function pointers can also
   be stored in registers and called with the register based jump
   instruction.

## How do we implement control structures: if / while / switch?

It seemed the closest I could get to immediate addresses was to and a
16 bit immediate with -1 . I had a C/D type instruction slot
available, so I went ahead and added lea for storing addresses based
of the program counter.

Here is a quick sketch of the basic control structures using a made up
Intel like syntax of instruction ra,rb,rc,rd.

```
         ; if (r0>r1) doa else dob
         subs r0,r1,n    ; test instruction sets condition codes
         ands sr,$~gt,n  ; mask for `greater than` bit setting condition codes for zero/not zero
         brcz labela     ; conditional branch if zero
         doa
labela:  br labelb       ; unconditional branch
         dob
labelb:
  
         ; while (r0) doa
         br labelb       ; jump to while test
labela:  doa
         ands r0,n,n     ; and with register n for test instruction
labelb:  brc labela      ; branch if not zero


         ; switch (r0) { case 0: doa; break; case 1: dob; break; case 2: doc; break; }
labelt   .word labela    ; jump table
         .word labelb    ; stored in nonexecutable memory probably
         .word labelc

         lea labelt,r1   ; stores the immediate address in r1
         add r1,r0<<2,r1 ; add the lookup to the table aligned to 32bits
         br r1           ; unconditional branch to the lookup address
labela:  doa
         br done
labelb:  dob
         br done
labelc:  doc
         br done
done:
```

## How do you implement the stack?

I probably should have thought about this more than I did. The only
method currently available is to select an arbitrary register as the
stack pointer and manually do immediate adds/subs for manages stack
space. However, the immediate offsets to lookups lets variables on the
stack be accessed directly. Generally the stack is allocated as frames
in C programs, so I don't think this is much of an issue, but I might
be wrong.

The alternative would be to either make a single register special with
its own push/pop instructions, or to create load-and-postincrement
instructions and store-and-predecrement instructions that work on
multiple registers.

## How easy is it to detect / resolve inter-instruction dependencies?

I'm not to sure on this one, each register can only have 2 input
registers and 2 output registers which are in pretty consistent
locations on the instructions. Additionally the condition codes are
only a dependency if the status register is used or the condition bit
is set. Relying on knowing if we need the status register for
dependencies might actually be a deal breaker.

## How easy is it to identify operations / operands?

Well, the opcode stores the specific operation. Decoding the operands
requires decoding both the the opcode and the format bit. At least the
condition handling is completely exclusive from the opcode decoding.

## How easy is it to identify branch targets?

I believe this is the main reason for only having only two
instructions for branches. One is a simple offset from the PC, which
can be decoded with out any other information. The other uses a
register as a dependency and also contains an offset.

## Implicit vs. explicit operands / coupling?

To keep the decoding of the instruction set simple, I tried to keep
the encoding of the operands consistent. There are 2 special
registers, the PC and the SR, I believe it is very difficult/complex
an architecture without these. Everything else is general purpose and
roughly orthogonal.
