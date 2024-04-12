# Compiler Flags

## ifort + ifx Compiler Options
```make
Linux and Mac OS X: -diag-disable [n]
Windows:            /Qdiag-disable:[n]
  Disables compiler messages.
  - 10448: ifort depreciation message.
  - 10161: ".o" assumed object file message.

Linux and Mac OS X: -fimf-arch-consistency:true
Windows:            /Qimf-arch-consistency:true
  This option ensures that the math library functions produce consistent
  results across different microarchitectural implementations of the same
  architecture (for example, across different microarchitectural
  implementations of Intel® 64 architecture). Consistency is only guaranteed
  for a single binary. Consistency is not guaranteed across different
  architectures.

Linux and Mac OS X: -no-fma
Windows:            /Qfma-
  This option determines whether the compiler generates fused multiply-add
  (FMA) instructions if such instructions exist on the target processor. When
  the negative form of the [Q]fma option is specified, the compiler must
  generate separate multiply and add instructions with intermediate rounding.
  This option has no effect unless setting CORE-AVX2 or higher is specified
  for option [Q]xHost.

Linux and Mac OS X: -fpconstant
Windows:            /fpconstant
  Tells the compiler that single-precision constants assigned to
  double-precision variables should be evaluated in double precision.

Linux and Mac OS X: -module path
Windows:            /module:path
  Specifies the directory where module files should be placed when created
  and where they should be searched for.

Linux and Mac OS X: -diag-enable all,  -diag-enable=all
Windows:            /Qdiag-enable:all
  Controls the display of diagnostic information during compilation.

Linux and Mac OS X: -init:snan,arrays,  -init=snan,arrays
Windows:            /Qinit:snan,arrays
  Initializes arrays and scalar values to NaN, to help find uninitialized
  variables when debugging.

Linux and Mac OS X: -ftrapuv
Windows:            /Qtrapuv
  For ifort, the option sets any uninitialized local variables that are
  allocated on the stack to a value that is typically interpreted as a very
  large integer or an invalid address. References to these variables are
  then likely to cause runtime errors that can help you detect coding
  errors. This option sets option -g (Linux*) and /Zi or /Z7
  (Windows*), which changes the default optimization level from O2 to -O0
  (Linux) or /Od (Windows). This option sets option [Q]init snan.

Linux and Mac OS X: -stand f18, -stand=f18
Windows:            /stand:f18
  This option tells the compiler to issue compile-time messages for
  nonstandard language elements for Fortran 2018 (f18)

Linux and Mac OS X: -fpe[N], -fpe-all=[N]
Windows:            /fpe:[N], /fpe-all:[N]
  N = 0: Floating-point invalid, divide-by-zero, and overflow exceptions are
  enabled throughout the application when the main program is compiled with
  this value. If any such exceptions occur, execution is aborted. This
  option causes subnormal floating-point results to be set to zero.
  Underflow results will also be set to zero, unless you override this by
  explicitly specifying option -no-ftz or -fp-model precise (Linux*) or
  option /Qftz- or /fp:precise(Windows*).

  N = 3: All floating-point exceptions are disabled. Floating-point
  underflow is gradual, unless you explicitly specify a compiler option
  that enables flush-to-zero, such as [Q]ftz, O3, or O2. This setting
  provides full IEEE support.

Linux and Mac OS X: -gen-interfaces
Windows:            /gen-interfaces
  Tells the compiler to generate an interface block for each routine in a
  source file.  Used for debugging alongside warn interfaces (warn all)

Linux and Mac OS X: -standard-semantics
Windows:            /standard-semantics
  If you specify option standard-semantics, it enables all of the options
  that implement the current Fortran Standard behavior of the compiler,
  which is Fortran 2018 features.

  Option standard-semantics enables option fpscomp logicals and the
  following settings for option assume: byterecl, failed_images,
  fpe_summary, ieee_compares, ieee_fpe_flags (if the fp-model option
  setting is strict or precise), minus0, nan_compares, noold_inquire_recl,
  noold_ldout_format, noold_ldout_zero, noold_maxminloc, noold_unit_star,
  noold_xor, protect_parens, realloc_lhs, recursion, std_intent_in,
  std_minus0_rounding, std_mod_proc_name, and std_value.

Linux and Mac OS X: -fexceptions
Windows:            None
  This option enables C++ exception handling table generation, preventing
  Fortran routines in mixed-language applications from interfering with
  exception handling between C++ routines.

Linux and Mac OS X: -traceback
Windows:            /traceback
  This option tells the compiler to generate extra information in the
  object file to provide source file traceback information when a
  severe error occurs at run time.

Linux and Mac OS X: -ftz
Windows:            /Qftz
  May flush denormal results to zero (number with extremely small
  magnitudes). The default behavior depends on the architecture.

Linux and Mac OS X: -O3
Windows:            /O3
  Specifies the code optimization for applications.

Linux and Mac OS X: -align array[n]byte
Windows:            /align:array[n]byte
  Specifies a starting boundary for arrays.

Linux and Mac OS X: -fp-model precise
Windows:            /fp:precise
  Enables value-safe optimizations on floating-point data and rounds
  intermediate results to source-defined precision.

Linux and Mac OS X: -fpp
Windows:            /fpp
  Enables preprocessing (conditional compilation)

Linux and Mac OS X: -fno-common
Windows:            None
  Tells the compiler to treat common symbols as global definitions. When
  using this option, you can only have a common variable declared in one
  module; otherwise, a link time error will occur for multiple defined
  symbols.

Linux and Mac OS X: -heap-arrays
Windows:            /heap-arrays-
  The compiler puts automatic arrays on the heap.  Use when testing many
  input points or kyrhos values to avoid crashes.

Linux and Mac OS X: -finline-functions
Windows:            None
  It enables the compiler to perform inline function 
  functions defined within the current source file.
```

## gfortran Compiler Options (Cygwin and Linux)
```make
-g: 
  produces symbolic debugging info and may disable optimization (only use for make debug)

-cpp: 
  enables preprocessing (conditional compilation)

-O2, -O3: 
  optimization levels

-fbacktrace:
  Specify that, when a runtime error is encountered or a deadly signal is
  emitted (segmentation fault, illegal instruction, bus error or floating-point
  exception), the Fortran runtime library should output a backtrace of the
  error. This option only has influence for compilation of the Fortran main
  program.

-fdollar-ok: 
  enables feature of older compilers and should be avoided

-fno-common: 
  Specifies that the compiler places uninitialized global variables in the BSS
  section of the object file. This inhibits the merging of tentative
  definitions by the linker so you get a multiple-definition error if the same
  variable is accidentally defined in more than one compilation unit.

-fno-range-check:
  Disables compile errors due to division by 0 and replaces with +-inf. Also
  causes integer overflow errors to wrap around

-ffpe-trap=invalid,zero,overflow
  Floating point exception traps, for debugging

-fno-sign-zero:
  Disallows values of negative zero (-0)

-finline-functions: 
   Consider all functions for inlining, even if they are not declared inline.
   The compiler heuristically decides which functions are worth integrating
   in this way.

-fgcse-after-reload:
   A redundant load elimination pass is performed after reload. The purpose of
   this pass is to clean up redundant spilling.

-ftree-vectorize:
  Perform vectorization on trees. This flag enables -ftree-loop-vectorize
  and -ftree-slp-vectorize if not explicitly specified.
```

---

[Return](../README.md)
