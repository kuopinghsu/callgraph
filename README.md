[![Codacy Badge](https://api.codacy.com/project/badge/Grade/8852412d15834d758ec5bd08f90db132)](https://www.codacy.com/manual/kuopinghsu/callgraph-gen?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=kuopinghsu/callgraph-gen&amp;utm_campaign=Badge_Grade)

# Callgraph Generator

Generating the call graph from elf binary file

## Binary Released

*   Windows application <A Href="https://github.com/kuopinghsu/callgraph-gen/blob/master/release/graphgen.win64.tar.bz2">64-bits</A>/<A Href="https://github.com/kuopinghsu/callgraph-gen/blob/master/release/graphgen.win32.tar.bz2">32-bits</A>
*   macOS Catalina application <A Href="https://github.com/kuopinghsu/callgraph-gen/blob/master/release/graphgen.macos.tar.bz2">here</A>

## Getting Started

It requires following library to build the code.

*   uthash: hash library <https://troydhanson.github.io/uthash/userguide.html>
*   PCRE: Perl Compatible Regular Expressions <https://www.pcre.org>
*   libxml2: <http://xmlsoft.org>

Install the PCRE library and libxml2 directory. Or insatall libpcre2-dev and libxml2-dev package on Ubuntu.

```text
$ sudo apt install libpcre2-dev libxml2-dev
```

Checkout the repository and initialize all submodules

```text
$ git clone https://github.com/kuopinghsu/callgraph-gen.git
$ cd callgraph-gen
$ git submodule update --init --recursive
```

build

```text
$ make
```

build on Visual Studio 2019

```text
% mkdir {somewhere} && cd {somewhere}
% git clone https://github.com/kiyolee/libxml2-win-build.git
% git clone https://github.com/kiyolee/pcre2-win-build.git
% git clone --recursive https://github.com/kuopinghsu/callgraph-gen.git
```

Modify the file ./libxml2-win-build/include/libxml/xmlversion.h, keep
the following and remove all other LIBXML_xxxx_ENABLED settings.

```text
LIBXML_THREAD_ENABLED
LIBXML_READER_ENABLED
LIBXML_SAX1_ENABLED
```

Build libxml2-static and libpcre2-8-static, and then build callgraph.

## Features

*   Generate call graph
*   Generate call tree
*   Recursive detection
*   Calculate the stack usage

## Usage
```text
Call graph generator. May 15 2020 build, written by Kuoping Hsu, MIT license
https://github.com/kuopinghsu/callgraph-gen

Usage:
    graphgen [-v] [-a target] [-x file] [-r function_name] [-m n]
             [-g | -t] [-c | -d] [-r name] [-i list] [-h]
             asm_file [vcg_file]

    --verbose, -v           verbose output
    --target name, -a name  specify the target (see support target
                            below)
    --xml file, -x file     read config file
    --root func, -r func    specify the root function
    --max n, -m n           max depth (default 256)
    --graph, -g             generate call graph (default)
    --tree, -t              generate call tree
    --nostack, -k           do not gather statck size
    --vcg, -c               generate vcg graph (default)
    --dot, -d               generate dot graph
    --ignore list, -i list  ignore list
    --help, -h              help

Support target:
    riscv
    arm
    openrisc
    xtensa
    xtensa_call0

Example:

    $ graphgen --max 10 --tree --ignore abort,exit infile.s

      maximun tree depth is 10, generate a call tree, ignore functions
      abort(), and exit().

    $ graphgen --xml mycore.xml --tree --root init infile.s

      Use a user-defined processor to generate a call tree from
      init() function.

```

This is an example to show the call tree of RISC-V's dhrystone diag. Using binutils to generate the assembly file

```text
$ riscv64-unknown-elf-objdump -d dhrystone.riscv > dhrystone.s
```

Generate the Call-Graph of VCG file

```text
$ graphgen --target riscv --graph dhrystone.s dhrystone.vcg
```

This is an example of call graph of RISC-V's dhrystone.<br>
<img src="https://github.com/kuopinghsu/callgraph/blob/master/images/dhrystone-callgraph.svg" alt="Dhrystone Call Graph" width=640>

Generate the Call-Tree of VCG file

```text
$ graphgen --target riscv --tree dhrystone.s dhrystone.vcg
```

This is an example of a call tree of RISC-V's dhrystone, and the red arc represents the call path used by the largest stack<br>
<img src="https://github.com/kuopinghsu/callgraph/blob/master/images/dhrystone-calltree.svg" alt="Dhrystone Call Tree" width=640>

(Note: This is a static analysis and is inaccurate if an indirect function call or recursion is detected.)

Recomment use <A Href="https://pp.ipd.kit.edu/firm/yComp.html">yComp</A> to browse the VCG file.<br>

<img src="https://github.com/kuopinghsu/callgraph/blob/master/images/yComp.png" alt="yComp">

## Limitations

*   Generating the call stack for ARM (experimental)

## License
MIT license
