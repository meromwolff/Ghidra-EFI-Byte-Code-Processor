# Ghidra-EFI-Byte-Code-Processor

## Background

It was a couple of months ago I started getting more and more interested in BIOS and UEFI technology until I stumbled upon Coreboot's blog post about the Ghidra firmware utilities that were written (https://blogs.coreboot.org/blog/2019/08/22/gsoc-ghidra-firmware-utilities-wrap-up/). At the end of the post, it is mentioned that there is no processor module for the EBC instruction set in Ghidra, and it was at that point that I thought to myself that it's probably not a big deal implementing such a basic architecture for Ghidra. Although it was a little bit harder than what I originally thought (you guessed it: Natural Indexing), I'm happy to finally release this processor module. Writing the processor module was a great excuse for me to read more of the UEFI spec and I do plan on utilizing this knowledge for future projects regarding EBC and UEFI in general.

## Installation

- Rename the root folder to "EBC" and copy it to /.../Ghidra/Processors and then restart Ghidra

## Usage

After you successfully installed the processor module and before loading an EBC compiled PE file to Ghidra, you
would need to check if the file was compiled as a 32-bit file or a 64-bit file. This is a critical piece of information
due to the EBC interpreter implementation of "Natural Indexing". For more information about "Natural Indexing" refer to section
22.4 in the "Unified Extensible Firmware Interface (UEFI) Specification, version 2.8" pdf.

So, to handle the differences between 32 and 64 bit executables, a "@define" macro is present in the .slaspec file. this macro defines
the value of "ARCH" which can be either 32 or 64. By default the value is 64 which means that if you want to use this processor 
module to analyze a 32 bit file, you would need to manually comment out the "@define ARCH '64'" and change it to "32".


## Features

Currently supports:

   - Full disassembly of all instructions after manually selecting the "EBC" processor type.
   - Good decompilation output.
   - Natural indexing disassembly appears in "[offset]" form (like IDA) instead of "(+2, +6)" for easier analysis of the code.

Currently not supported:

   - Implementation for external function calls - Currently even external function calls are implemented with "regular" CALL                 instruction, meaning that they will show in the decompiler as a function call (although they would not be resolved of course).
    
   - BREAK instruction - currently the BREAK instruction is defined as a custom pcodeop (This also means that the "Single Step" Flag         has no actual use).
    
   - Some instructions (for example some of the MOV instructions) sometimes would zero the upper bits of an 8 byte value
      that is operated upon in a 32-bit instruction. Most of these special cases were not implemented in pcode due to the complexity
      they would add to the design and to the decompilation output.


## Future Work

- Modifying Ghidra's PE Loader module so it would automatically identify an EBC PE file
- Optimize out some of the constructors since this processor module can probably be written in less lines than what it is now :)

## A note about emulation

Although the sizes of some instruction operands may not be strictly correct ( 8 bytes instead of 4 or vice versa), I think this code
can be used in order to emulate snippets of EBC compiled code. With that said, this probably would not be very helpful since it would not be possible obviously to emulate the external calls at all although they are a crucial part of any UEFI driver.
That's why if you need to actually emulate EBC code, I would recommend using the really cool "ebc-vm" git project.

## Testing and issues

Since it was pretty hard finding out in the wild some EBC PE files (except 2 files I did find), I used the ebc-fasm extension to compile
my own test programs in order to check the quality of the decompilation output and if the disassembly is working as expected.
If you find any problems with the disassembly output or in the decompiler output, feel free to create an issue with the relevant details here and I'll give it a look.

Hope this could be helpful to someone.

Enjoy!

