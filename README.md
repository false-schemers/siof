# SIOF (Scheme In One File) - A Minimal R7RS Scheme System
                         
SIOF is a portable interpreter for the R7RS Scheme programming language. 
It can be built from a single C source file [siof.c](https://raw.githubusercontent.com/false-schemers/siof/master/siof.c); 
there are no OS- or hardware-specific parts, no compiler-specific tricks, no dependency on platform-specific building tools. There is no distributives or packages: just compile the source file with your favorite C compiler, link it with the standard C runtime libraries and be done with it. For some platforms, statically compiled binaries are available.

## Installation

Here's how you can compile SIOF on a unix box using GCC:

```
gcc -o siof siof.c -lm
```

Please note that some compilers may issue hundreds of warnings; we recommend to add `-Wno-parentheses-equality` for
Clang and `-D_CRT_SECURE_NO_WARNINGS` for Windows headers (unless you want to hear that `fopen`
is no longer a reasonable way to open files). Some compilers link `<math.h>` library automatically, some require explicit 
option like `-lm` above. 

For better performance (up to 3x of the baseline) and smaller executable, you may add optimization flags, e.g.:  

```
gcc -o siof -O3 -DNDEBUG siof.c -lm
```

If you do that, be ready for long compilation times (actual time varies widely from ~1 minute to more than an hour, depending on the compiler and optimization level). 

The resulting interpreter has no dependencies (except C runtime and standard -lm math library) and can be run from any location.
If compiled statically, the resulting executable can be easily moved between systems with the same ABI.


## Scheme Compatibility

SIOF is true to basic Scheme principles -- it features precise garbage collector, supports proper tail recursion, `call/cc`, `dynamic-wind`, multiple return values, and has a hygienic macro system and a library system. It is mostly compatible with R7RS-small standard, but it has the following known limitations and deviations from the standard:

  *  fixnums are 30 bit long, flonums are doubles
  *  no support for bignums/rational/complex numbers
  *  no support for Unicode; strings are 8-bit clean, use system locale
  *  source code literals cannot be circular (R7RS allows this)
  
Some features of the R7RS-Small standard are not yet implemented or implemented in a simplified or non-conforming way:

  *  `read` procedure is always case-sensitive (all ports operate in no-fold-case mode)
  *  `#!fold-case` and `#!no-fold-case` directives are not supported
  *  `include` and `include-ci` forms are not supported
  *  `get-environment-variable` is implemented, but `get-environment-variables` is not
  *  `current-jiffy` and `jiffies-per-second` return inexact integers
  *  `current-second` is defined as C `difftime(time(0), 0)+37`
  *  macroexpander treats `_` as a regular identifier, not match-all pattern
  *  macroexpander does not support `(... escaped)` pattern escapes
  *  macroexpander does not support patterns with internal ellipsis and improper tail variable

Here are some details on SIOF's interactive Read-Eval-Print-Loop (REPL) and evaluation/libraries support:

  *  `read` supports R7RS notation for circular structures, but both `eval` and `load` reject them
  *  all supported R7RS-small forms are available in the built-in `(sharpf base)` library
  *  `-L` command-line option extends library search path; initial path is `./`
  *  `cond-expand` checks against `(features)` and available libraries
  *  `environment` dynamically fetches library definitions from `.sld` files
  *  non-standard `expand` is available (macroexpands the argument)
  *  `eval`, `load`, and `expand` accept optional environment argument
  *  command-line file arguments are dynamically loaded 
  *  both `import` and `define-library` forms can be entered interactively into REPL
  *  `features` procedure returns `(r7rs exact-closed siof siof-1.0.1)`
  
Please note that SIOF's interaction environment exposes bindings for all supported R7RS-small procedures and syntax forms directly, so there is no need to use `import`. In order to use `import` forms and `environment` procedure with R7RS-small libraries, you will need to copy the `scheme` folder with .sld files for the libraries from [here](https://github.com/false-schemers/sharpF/tree/master/int/scheme) to your local system and make sure SIOF can locate it (its library search path contains current directory by default and can be extended with the help of `-L` command line option).


## Origins

SIOF's original code is written in [#F](https://github.com/false-schemers/sharpF), a language for building Scheme-like
systems. Its #F source code can be found there in `examples` directory:

[siof.sf](https://raw.githubusercontent.com/false-schemers/sharpF/master/examples/siof.sf)

SIOF's on-the-fly compiler is derived from Marc Feeley's Scheme Interpreter (see `gambit/bench/src/scheme.scm` in the [Gambit Scheme repository](https://github.com/gambit/gambit)). Hygienic macroexpander is derived from Al Petrofsky's alexpander v1.65 (please see the #F source file for the original copyright notice). Supporting library code comes from #F's [LibL library](https://raw.githubusercontent.com/false-schemers/sharpF/master/lib/libl.sf).


## Family

Please see [S4IOF](https://github.com/false-schemers/s4iof) repository for a smaller system without hygienic macros, and [S5IOF](https://github.com/false-schemers/s5iof) for its R5RS-compatible variant with hygienic macros.


