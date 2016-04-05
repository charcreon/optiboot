# Introduction #

You can compile Optiboot for platforms that do not have pre-existing .hex files, or you can make modifications to the source code and/or Makefile in order to implement "custom" versions of Optiboot.

This will require some relatively deep knowledge of avr-gcc, Atmel AVR hardware and processor details, Makefiles, and the command line environment.  We HAVE tried to take steps to allow Optiboot to be compiled without installing a full AVR development suite.


# Details #

Optiboot is designed to be compiled with the same version of avr-gcc that is distributed with the Arduino environment: 4.3.2.  This is actually quite an old version of gcc; some effort has been made to allow the compile to procede with new versions of the compiler.  However, since Optiboot MUST compile to a total size of less than 512 bytes, it is very sensitive to changes in the way the compiler does optimizations, or the way it processes optimization options on the compile command.

In all cases, Optiboot compiles directly from the command line using "make", rather than from within any IDE.  It is currently designed to used compilers installed in one of three different places:
  * Local Development Environment - you have WinAVR (on Windows), CrossPack (on Mac), or an avr-gcc package (linux) installed on your system, with appropriate development tools somewhere in your path.
  * You have Arduino installed, and are trying to compile Optiboot from its home directory within the Arduino directory structure (hardware/arduino/bootloaders/optiboot/)  The Arduino app includes a pretty complete set of compiler tools, and these can be used to compile optiboot without installing a separate toolchain.
  * You have downloaded the Arduino Source code, which also includes (binary) copies of avr-gcc toolchains, and a source directory containing the Optiboot source code.