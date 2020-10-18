# C/C++ Terminologys

## cpp

c plus plus

## .h

.header

## -I

For each of the headers used in your source \(via `#include` directives\), the compiler searches the so-called _include-paths_ for these headers. The include-paths are specified via `-I`_`dir`_ option. Since the header's filename is known \(e.g., `iostream.h`, `stdio.h`\), the compiler only needs the directories.

## -L

The linker searches the so-called _library-paths_ for libraries needed to link the program into an executable. The library-path is specified via `-L`_`dir`_ option \(uppercase `'L'` followed by the directory path\). 

In addition, you also have to specify the library name. In Unixes, the library `lib`_`xxx`_`.a` is specified via `-l`_`xxx`_ option \(lowercase letter `'l'`, without the prefix "`lib`" and `".a`" extension\). In Windows, provide the full name such as `-lxxx.lib`. The linker needs to know both the directories as well as the library names. Hence, two options need to be specified.

## malloc

memory allocation

> A subroutine in the C programming language's standard library for performing dynamic **memory allocation**.

## calloc

contiguous allocation

> The malloc\(\) function allocates a single block of memory. Whereas, calloc\(\) allocates multiple blocks of memory and initializes them to zero.

