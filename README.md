# e56k
A Spiritual successor of the 6502 ISA.

Inspiration:
https://blog.randallhyde.com/index.php/2021/02/05/the-65000-yet-another-6502-dream-machine/

https://youtu.be/2fNBkUCjhcE

In 1985 I read a book on digital logic design and started designing my own 6502-like CPU with NAND gates. It only ever happened on paper, but it was fun, and I learned a lot. I remember being fascinated with Grey code and Karnaugh maps. With modern tools, there is a chance that I could build it in software, and possibly hardware.

I really like most of what Randall Hyde did, more so than any other 6502 extension that I have seen.

I like the new instructions added to WD65C02, but am less impressed by the WD65816. 65000 is a better "extension" but it also is not hampered by a backward compatibility requirement.

Randall Hyde said, "Maybe I’ll call it the 65020 (a play on the 68000 and 68020)." Bad call. 68020 ISA was a compatible superset of the 68000 ISA, not a complete redesign. (OTOH, Motorola *did* do a clean break from 6800 to 68000.) I'll go with a similar naming idea, but make a clean break from 6502. This ISA family will be called 56000. The first concrete implementation will be 56010A, a simple chip with 2 kiB data cache 2 kiB instruction cache, 16-bit pointers ("A"=16 "B"=24 "C"=32 "D"=64), and a very simple pipeline. Time period is 1978-80 so it should perform roughly on par with Intel's 8086, and should not be blown away by Motorola's 68000. Later models (560x0) can implement more sophisticated microarchitectures the way Hyde's x86 learning processors did, while keeping the same programming model (and in most cases, the same code). I can imagine a 56020B inside millions of laser printers.

CISC vs RISC: Not really a thing, IMO. ARM won because it was inexpensive and consumed very little power, not because it had a better ISA. Modern ARM cores resemble the belittled x86 cores more than they resemble the original ARMv1.  Adding Java and JavaScript? Disgusting. Fixed-length instructions may be easy to implement today, but future-proof they are not. A good instruction cache completely eliminates the problem of variable length instructions.

(aside: the longest instruction would be (opcode 2, reg 1, const 9)=12 bytes long, but this rare instruction would never occur in a tight loop.)

Like Hyde, I am thinking more of embedded than desktop computers. However, I see no reason not to include provisions for protected mode and virtualization extensions. On the other hand, Virtual Memory, the idea of *transparently* pretending that disk is RAM, is a bad idea, and I will *not* support it. Ever.

The same argument applies to memory mapped I/O. Using RAM as a video frame buffer is fine, but I think Intel was right to explicitly separate I/O space from data space, at the expense of adding a single pin to the package. I/O is almost always slower than memory and should not be treated the same. Separate I/O instructions allow you to live inside 64k a lot longer.

"better than ATmel" turned out to be easier than I expected. The only advantages AVR has over 6502 are the bit-twiddling instructions, Harvard architecture, and a lot of registers. Rockwell should have been more agressive in marketing the 6502 derivatives they designed.

12-bit bytes would be cool, but there is too much inertia behind 8 bits.
