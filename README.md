gcc for Unikraft
=============================

This is a port of gcc for Unikraft as an external library.

For the time being we only port libbacktrace and libffi libraries as
they are needed for the libgo port and libgcov library because it is needed
for the PGO (Profile-Guided Optimization) mechanism. You will need newlib
for all three libraries to build. In addition, 

For libffi you will need the pthread\_embedded external library too.

For libbacktrace you will need the following external libraries:
+ compiler-rt 
+ libunwind
+ libcxx
+ libcxxabi

Note that because of a documented bug in libunwind `unw_getcontext`
leads to a page fault, and in turn `backtrace_full`will also lead to
one as it uses `_Unwind_Backtrace` which calls `unw_getcontext`.

For libgcov you will need the following libraries that should be added to the
`Makefile` in this order:

* `pthreads`, e.g `pthread-embedded`
* `compiler-rt`
* `libcxx`
* `libcxxabi`
* `libunwind`
* `libc`, e.g. `newlib`

In order to use the `PGO` mechanism, the `fprofile-generate` and `fprofile-use`
flags should be added to the `Makefile.uk` of the application running in Unikraft.

For example, if you want to use the `PGO` mechanism for the `helloworld`
application you have to add the following lines in the `Makefile.uk`:

`APPHELLOWORLD_CFLAGS -$(CONFIG_OPTIMIZE_PGO_GENERATE)          += -fprofile-generate`

`APPHELLOWORLD_CFLAGS -$(CONFIG_OPTIMIZE_PGO_USE)               += -fprofile-use`

Several header files should be generated to successfully compile the source files
from the libgcov library. These headers have already been generated and can be found
in the `libgcov/include` directory. If you want to generate again the files, you need
to download the `GCC` sources and run the following commands:

* in the `libgcc` directory:
`make auto-target.h gthr-default.h libgcc_tm.h`
* in the `gcc` directory:
`make tconfig.h tm.h auto-host.h gcov-iov.h`

Please refer to the `README.md` as well as the documentation in the `doc/`
subdirectory of the main unikraft repository for further information.
