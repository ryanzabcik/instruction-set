# CS350C Instruction Set Architecture

To do the assignment 1, you must specify what commit hash of the Arch
project that you will be writing your assignment against. If you
choose to accept
[Chris's original proposal](https://github.com/CS350C-Fall-2014/instruction-set/tree/c32/)
unchanged, that is sufficient to finish the assignment.
 
However, there were many valid points brought up in discussion on
Thursday that you should consider. To organize this discussion so that
you can have a final proposal which is a reasonable substrate for
future projects.

[Piazza Link](https://piazza.com/class/hzbug9l443uam)

## Background reading

Relatively conservative ISAs which are probably good source material in no particular order.

 - [6502](http://www.6502.org/tutorials/6502opcodes.html)
 - [8080/z80](http://nemesis.lonestar.org/computers/tandy/software/apps/m4/qd/opcodes.html) ← a really classic 16 bit ISA
 - [PDP11](https://mathlab.utsc.utoronto.ca/courses/cscb58s14/pdp11.pdf)
 - [CDC6000](http://www.60bits.net/msu/mycomp/cdc6000/65inst.htm)
 - [MIPS](http://www.mrc.uidaho.edu/mrc/people/jff/digital/MIPSir.html)
 - [ARM](http://users.ece.utexas.edu/~valvano/Volume1/QuickReferenceCard.pdf)
 - [PowerPC](https://www-01.ibm.com/chips/techlib/techlib.nsf/techdocs/852569B20050FF778525699600719DF2/$file/6xx_pem.pdf)
 - [Amtel AVR](http://www.atmel.com/images/doc0856.pdf) ← a classic in order 8 bit ISA
 - [SuperH](http://koti.kapsi.fi/~antime/sega/files/h12p0.pdf)
 - [Motorola 6800](http://bitsavers.trailing-edge.com/pdf/motorola/6800/Motorola_M6800_Programming_Reference_Manual_M68PRM%28D%29_Nov76.pdf)
 - [8086](http://mai.kvk.uni-obuda.hu/documents/tantargy/8086-instruction-set-overview.pdf)
 - [80386](http://css.csail.mit.edu/6.858/2014/readings/i386.pdf) ← the granddaddy of x86_64
 - [IBM360](http://www.cse.iitd.ernet.in/~sak/courses/cdp/cards/IBM360.pdf)

## Guidelines

The master branch will _not_ track any single proposal. If you have a
suggestion for an ISA, please either fork one of the existing
proposals onto a new branch or create a new branch based off of master
replacing the README file with whatever you may find appropriate as an
introduction and explicitly addressing Dr. Gheith's ISA usability
questions:

 - what is the word size?
 - what is the instruction layout?
 - do you have any special registers?
 - how do we do function calls?
 - how many register file read/write ports (maximum number of registers read / written per cycle)?
 - how many memory read/write ports?
 - how do we implement arrays / structures / pointers / function pointers?
 - how do we implement control structures: if / while / switch?
 - how do you implement the stack?
 - how easy is it to detect / resolve inter-instruction dependencies
 - how easy is it to identify operations / operands
 - how easy is it to identify branch targets
 - implicit vs. explicit operands / coupling

The following questions are also legitimate.

 - how do you implement integer overflow/underflow and "exception" detection?
 - how do you implement virtual memory?
 - do you have privileged instructions? memory regions? how is privilege tracked?

Note that the [Batbridge](https://github.com/arrdem/batbridge)
specification does not address these questions. As you will need to
implement this instruction set yourselves in what approximates
hardware I advise against investing much energy in these questions as
they are not critical to the instruction scheduling problems which
will dominate this course.

As a courtesy to other authors, please send pull requests to proposals
that are not your own rather than directly contributing.

Dr. Gheith or I will update and version the `master` branch when a
proposal is decided on. Until then please don't touch it.
