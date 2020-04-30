
# SMDA

SMDA is a minimalist recursive disassembler library that is optimized for accurate Control Flow Graph (CFG) recovery from memory dumps.
It is based on Capstone (http://www.capstone-engine.org/) and currently supports x86/x64 Intel machine code.
As input, arbitrary memory dumps (ideally with known base address) can be processed.
The output is a collection of functions, basic blocks, and instructions with their respective edges between blocks and functions (in/out).
Optionally, references to the Windows API can be inferred by using the ApiScout method.

## Installation

With version 1.2.0, we have finally simplified things by moving to PyPI!  
So installation now is as easy as:

```
$ pip install smda
```

## Usage

A typical workflow using SMDA could like this:

```
>>> from smda.Disassembler import Disassembler
>>> disassembler = Disassembler()
>>> report = disassembler.disassembleFile("/bin/cat")
>>> print(report)
 0.777s -> (architecture: intel.64bit, base_addr: 0x00000000): 86 functions
>>> for fn in report.getFunctions():
...     print(fn)
...     for ins in fn.getInstructions():
...         print(ins)
...
0x00001720: (->   1,    1->)   3 blocks,    7 instructions.
0x00001720: (      4883ec08) - sub rsp, 8
0x00001724: (488b05bd682000) - mov rax, qword ptr [rip + 0x2068bd]
0x0000172b: (        4885c0) - test rax, rax
0x0000172e: (          7402) - je 0x1732
0x00001730: (          ffd0) - call rax
0x00001732: (      4883c408) - add rsp, 8
0x00001736: (            c3) - ret 
0x00001ad0: (->   1,    4->)   1 blocks,   12 instructions.
[...]
>>> json_report = report.toDict()
``` 

There is also a demo script:

* analyze.py -- example usage: perform disassembly on a file or memory dump and optionally store results in JSON to a given output path.

The code should be fully compatible with Python 2 and 3.
Further explanation on the innerworkings follow in separate publications but will be referenced here.

To take full advantage of SMDA's capabilities, make sure to (optionally) install:
 * lief 
 * pdbparse (currently as fork from https://github.com/VPaulV/pdbparse to support Python3)

## Version History

 * 2020-04-29: v1.2.0 - Restructured config.py into smda/SmdaConfig.py to similfy usage and now available via PyPI! The smda/Disassembler.py now emits a report object (smda.common.SmdaReport) that allows direct (pythonic) interaction with the results - a JSON can still be easily generated by using toDict() on the report.
 * 2020-04-28: v1.1.0 - Several improvements, including: x64 jump table handling, better data flow handling for calls using registers and tailcalls, extended list of common prologues based on much more groundtruth data, extended padding instruction list for gap function discovery, adjusted weights in candidate priority score, filtering code areas based on section tables, using exported symbols as candidates, new function output metadata: confidence score based on instruction mnemonic histogram, PIC hash based on escaped binary instruction sequence
 * 2020-03-10: Various minor fixes and QoL improvements.
 * 2019-08-20: IdaExporter is now handling failed instruction conversion via capstone properly.
 * 2019-08-19: Minor fix for crashes caused by PDB parser.
 * 2019-08-05: v1.0.3 - SMDA can now export reports from IDA Pro (requires capstone to be available for idapython).
 * 2019-06-13: PDB symbols for functions are now resolved if given a PDB file using parameter "-d" (THX to @VPaulV).
 * 2019-05-15: Fixed a bug in PE mapper where buffer would be shortened because of misinterpretation of section sizes.
 * 2019-02-14: v1.0.2 - ELF symbols for functions are now resolved, if present in the file. Also "-m" parameter changed to "-p" to imply parsing instead of just mapping (THX: @VPaulV).
 * 2018-12-12: all gcc jump table styles are now parsed correctly. 
 * 2018-11-26: Better handling of multibyte NOPs, ELF loader now provides base addr.
 * 2018-09-28: We now have functional PE/ELF loaders.
 * 2018-07-09: v1.0.1 - Performance improvements.
 * 2018-07-01: v1.0.0 - Initial Release.


## Credits

Thanks to Steffen Enders for his extensive contributions to this project.
Thanks to Paul Hordiienko for adding symbol parsing support (ELF PDB).

Pull requests welcome! :)

