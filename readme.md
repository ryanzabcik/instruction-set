# Description of the c16

## Goals:
 - Simple
 - Consistent
 - Shared logic between similar instructions
 - Make common operations easy and uncommon operations possible

## Features:
 - 16bit word size
 - 8 16bit registers
 - Load/store architecture
 - Word addressable memory
 - Lack of predicates or condition flags
 - MIPS-like conditions
 - Arithmetic instructions with shifts
 - Arithmetic instructions with Immediates
 - Register relative addressing and branching
 - PC relative addressing and branching


## Registers

R0-R6 - general purpose registers
N - constant -1 register
PC - unaddressable program counter


## Instruction Encoding

    Type A:
    | op |f| rd| ra| rb|sh|
    |xxxx|x|xxx|xxx|xxx|xx|
    
    Type B:
    | op |f| rd| ra| imm5|
    |xxxx|x|xxx|xxx|xxxxx|
    
    Type C:
    | op |f| rd|  imm8  |
    |xxxx|x|xxx|xxxxxxxx|
    
    op - 4 bit opcode encodes 16 general opcodes
    f - format bit indicates format of instruction
    rd - destination register
    ra - register a
    rb - register b
    sh - 2 bit shift encoding
      00 - no shift
      01 - left shift 1 bit
      10 - logical right shift 1 bit
      11 - arithmetic right shift 1 bit
    imm5 - sign extended 5 bit immediate or 4 bit shift with logic/arithmetic bit
    imm8 - sign extended 8 bit immediate
    

## Instruction Set

    or - bitwise or
    xor - bitwise xor
    and - bitwise and
    andn - bitwise and with not
    add - integer addition
    sub - integer subtraction
    slt - set if less than
    sltu - set if less than unsigned
    
    mul - multiply
    div - divide
    shl - shift left
    shr - shift right
    
    ld - load
    st - store
    lea - load effective address
    call - branch and store return address
    breq - branch if zero
    brne - branch if not zero


## Interesting encodings

    or r0,$0,r0     => nop (0x0000)
    brne n,-1       => halt (0xffff)
    
    and n,imm5,rd   => set imm5,rd
    and ra,n,rd     => mov ra,rd
    lea $0,rd       => mov pc,rd
    
    xor ra,n,rd     => not ra,rd
    
    call ra+imm5,n  => br ra+imm5
    call imm8,n     => br imm8
