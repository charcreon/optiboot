# Good Question! #

Optiboot occasionally generates questions on various forums (Arduino, AVRFreaks, etc), and I thought I'd collect some of the answers here, without spending a lot of time trying to re-organize them as actual "documentation."

  1. Can I put Optiboot on my Arduino MEGA2560 ?
> > No.  Optiboot uses "Version 1" of the STK500 communications protocol, which only allows up to 64kwords of flash to be programmed.
  1. Why does Optiboot have its own copy of boot.h ?
> > The copy of boot.h used by Optiboot has been modified to use "out" instructions instead of "sts."  The out instruction is shorter, but only works if the target registers in withing the first 64 IO addresses.
  1. What is a "Virtual Boot Partition"
> > VIRTUAL\_BOOT\_PARTITION is a hack for implementing a bootloader on parts that don't have "start at the bootloader address after reset" support.  So the start vector always has to be at 0, where it will be overwritten by the application's vectors.  Except the bootloader "cleverly substitutes" its own start address for the start of the application during "upload", causing the bootloader to always run first.
  1. Why is there an extra `BAUD_RATE * 4L` there? Why is it using 4L and 8L rather than just 4 and 8?
> > It's doing rounding.  int(calculated\_divisor + 0.5), except since it's all integers we use algebra and multiply the 0.5 by something big enough to make it an integer.  (Hmm.  This is probably silly; I should just let it use compile-time floating point.  like `__delay_ms()`)
  1. What is the difference between `"__boot_page_fill_short" and "__boot_page_write_short"`?
> > Flash is written a full page at a time, rather than a byte at a time.  To write a page of flash, you fill a RAM buffer in the flash controller peripheral (boot\_page\_fill), and then give a "boot\_page\_write" to actually write that buffer to the flash memory.  All together, there are three copies (buff, which is filled from the serial comm, the flash page buffer filled by page\_fill, and the flash itself.)
  1. What's with the "`#define rstVect (*(uint16_t*)(RAMSTART+SPM_PAGESIZE*2+4))`" ?
> > These are "manually defined" variables.  Normally you'd say `"char buff[SPM_PAGESIZE]; int rstvector, wdtvector;"`  But C would want to include code to initialize those variables, which would be "too much" extra code for optiboot to fit in 512 bytes.  So instead we define buff as being where we know the first byte of RAM is and rstvetcor as the first byte after buff, and so on.
  1. hat is RAMPZ? what is being done in the "newAddress += newAddress" line?
> > RAMPZ is the "extra byte" of flash addressing for parts with more than 64k words.
> > The addition (multiply by 2) converts from (16bit) word addresses that Atmel likes to use in various place, to byte addresses used by the hardware and avr-libc in most places.
  1. What are "sections" and the ".version" section in particular, and what is optiboot doing with them?
> > The "section" attribute is equivalent to the .section gnu assembler directive.    http://tigcc.ticalc.org/doc/gnuasm.html#SEC119 (one source; generic.)  ".version" is an arbitrary name.<br>
<blockquote>These are essentially commands that transfer information from the source code to the linker, which will then put together the various bits of information in various places in memory, based on the "linker map" (file provided by the infrastructure) and commands given to it explicitly ("-Wl,--section-start=.version=0x3ffe" in the Makefile.)  The newer code does this in C with attribute instead of using inline assembler, but it does the same thing.<br>
</blockquote><ol><li>How does Optiboot put the code and version number at the correct absolute flash addresses?<br>
<blockquote>The output from the compiler is in relocatable "sections" that are then relocated and assigned to appropriate addresses by the linker.  The addresses used are normally determined by a "linker map" file that is part of the per-chip definitions included with the compiler, but in the case of Optiboot, they are specified explicitly in the link command using the <code> -Wl,--section-start=.text=0x3e00</code> type commands.<br>
</blockquote></li><li>Where did 0x3e00 come from?  I don't see that in the data sheet!<br>
<blockquote>There are various places where the Atmel documentation uses WORD addresses for flash memory, while most of the gnu tools use BYTE addresses.  0x3e00 comes from the "boot size configuration" in the boot loader support chapter of the datasheet (where it says "0x1F00")