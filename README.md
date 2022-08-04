

# Multi-Mode Anomalous Transport Model

This software package contains the multi-mode anomalous transport module (MMM). MMM is a theory-based transport model which has been used to predict temperature, density and toroidal rotation profiles for tokamak plasmas. The model includes an improved Weiland model for the ITG, TEM, and MHD modes, the Horton model for short wavelength ETG model and a new model for the drift resistive inertial ballooning modes (DRIBM). The ETG transport threshold in the Horton model is refined by using threshold obtained from toroidal gyrokinetic ETG turbulence. These components of transport models provide contributions to transport in the different regions of plasma discharge.

> TODO: Add statement about new ETGM.  Check if current text should be updated in regards to recent changes.

## Contents
- [Documentation Links](#documentation-links)
- [Requirements](#requirements)
- [Project Contents](#project-contents)
- [Build Instructions](#build-instructions)
- [Testing Instructions](#testing-instructions)
- [Plotting Instructions](#plotting-instructions)
- [Linking Instructions](#linking-instructions)
- [Manual Compilation](#manual-compilation)
- [Contact](#contact)

## Documentation Links

Additional documentation located in the `/doc` directory:

- [Update History](doc/UpdateHistory.md)
- [MMM Library](doc/MMMLibrary.md)
- [MMM Driver](doc/MMMDriver.md)
- [MMM Wrapper](doc/MMMWrapper.md)
- [Using IMAS](doc/UsingIMAS.md)

## Requirements

MMM is intended to be built on an x64 POSIX-compliant OS (UNIX, Linux, Cygwin, etc.) with a Fortran 90 compiler. The rest of this README will assume that you are running an x64 Linux system with a BASH shell.  Support for the Windows OS is included via Cygwin (www.cygwin.com), although the compiler options may be more limited. Building on other architectures may be possible but has not been tested. Users and developers are welcome to send in their experiences building MMM on different architectures. 

The `make` command must be installed in order to use the included make files.

The testmmm program requires installed PSPLINE library (https://w3.pppl.gov/ntcc/PSPLINE/) and optional IMAS library.

> TODO: Is PSPLINE only required for IMAS?

> TODO: Link to IMAS library?

> TODO: Library Installation instructions needed.

Gnuplot (http://www.gnuplot.info/) is required to use the included plotting script.  This program is freely available on Unix systems and can also be installed within Cygwin.

## Project Contents

This project contains the following sub-directories:


- **/doc**

    Contains MMM documentation in Markdown format.  Markdown is best viewed on Github or through a Markdown viewer.


- **/input**

    Contains sample input files (`sample_input.dat`) that can be used to run MMM.  The expected output corresponding to these input files is also provided (`sample_output.dat`), which can be used to test your MMM installation.

    See [Testing Instructions](#testing-instructions) for more details.

- **/legal**

    Contains the following legal documents that pertain to MMM:
    - [End User License Agreement](/legal/eula.pdf)
    - [Terms Of Use](/legal/tou.pdf)

    Please read these documents carefully.

- **/lib**

    Contains the MMM library file and corresponding .MOD files.  The library file can compiled and used with either the provided `testmmm.f90` or `mmm_wrapper.f90` files, as well as your own Fortran90 code.

    See the separate [MMM Library](doc/MMMLibrary.md) documentation for more details.

- **/test**

    Contains the `testmmm.f90` code that can be used to test and run MMM.

    `testmmm.f90` reads in variable data from `input.dat` files and provides some error and verification checks on the imported data.  In addition, `testmmm.f90` provides support for derivative and gradient calculations, if these values aren't provided in the input file.

    See [Testing Instructions](#testing-instructions) for more details.

- **/wrapper**

    Contains the `mmm_wrapper.f90` sample Fortran90 code that calls the MMM subroutine using `input.dat` files from the `/input` directory.  The wrapper can be used to compile MMM using the provided Make file. Additionally, the wrapper serves as an additional reference in regards to making the MMM subroutine call.

    See the separate [MMM Wrapper](doc/MMMWrapper.md) documentation for more details.


## Build Instructions

Follow this two-step procedure to build the binaries using the MMM library:

1.  Set the environmental variable `MMMFC` to the compiler command on your system. Here are some common examples:

    |  Compiler | MMMFC setting |
    |-------------------------|----------------------------|
    |   GNU Fortran         | gfortran                           |
    |   Intel Fortran          | ifort                                 |
    |   PGI Fortran           | pgfortran                         |
    |   XL Fortran             | xlf                                    |
    ```bash
    $ export MMMFC=pathf95
    ```
    ```makefile
    % setenv MMMFC pathf95
    ```

    > TODO: What is this pathf95 stuff???

    > TODO: The current version of MMM (v9.0.0) has only been tested using gfortran.


2.  To compile the whole package, including the MMM library file in `/lib` and the stand-alone driver program `test/mmm.exe`, simply run:  

    ```bash
    $ make
    ```

    inside the main directory to invoke the master makefile, which will then invoke secondary makefiles to build the module and the driver program. The binary files (\*.o, \*.a, \*.exe) are placed alongside their source codes. To delete all binary files, use:  

    ```bash
    $ make clean
    ```

    You can generate debugging-ready binary files using these commands:  

    ```bash
    $ make clean  
    $ make debug
    ```

    > TODO: Might want to remove the debug option from MMM-Release, since it will only enable debugging on testmmm.f90, and not the MMM library.

It is possible to build the module and the driver program separately, using only the secondary makefiles. In this case, follow these steps:

1.  Set the environmental variable `MMMFC` to the compiler command on your system.
2.  Change the current directory to the subdirectory where you want to build binaries and issue the `make` command.

## Testing Instructions

The driver program `test/mmm.exe` requires only one input file called `input.dat`.  Since the input file is a Fortran namelist file, the variables can be arranged in any order.  

To produce the test cases:

1.  Change the current directory to the directory of one of the test cases, such as `input/case-lmode`.
2.  Copy the sample input file:
    ```bash
    $ cp sample_input.dat input.dat
    ```
3.  Run the driver program:
    ```bash
    $ ../../test/mmm.exe  
    ```

    Note that the driver program is located in a different directory.

After running, `test/mmm.exe` will generate an output file `output.dat`. The output file contains a listing of all possible input arguments used for the MMM subroutine and it is followed by a listing of all output arguments produced by the subroutine. To compare your output with the provided sample output, use this `diff` command:
```bash
$ diff output.dat sample_output.dat
```
which should give little to no output, if the driver program is correctly built.

See the separate documentation [MMM Driver](doc/MMMDriver.md) for more information on `test/mmm.exe`.

## Plotting Instructions
MMM output is given as a simple text spreadsheet, which can be interpreted using plotting tools such as gnuplot. For example, the output of case-lmode case be visualized using gnuplot by these commands run in the `case-lmode` subdirectory:
```bash
$ gnuplot
gnuplot> plot '< tail -n 51 output.dat' u 1:4 w l ti 'fti'  
```

> TODO: Check this command still works.

Note that `case-lmode` has 51 zone boundaries (radial points). This gnuplot command extracts the last 51 lines of the output file and generates an X-Y plot with the minor radius (column 1) on X axis and the ion thermal flux `fti` (column 2) on the Y axis.  

> TODO: Discuss the `plot.sh` script.

> TODO: Include sample use of the plotting script.

> If any errors are encountered using `plot.sh` on a Windows OS, make sure that this file is still using Unix line endings.  This script will completely fail if the line endings get switched to the Windows format.

## Linking Instructions

To use MMM in your own program, the following steps need to be followed:

1.  A `use` statement must reference the MMM module.
2.  A `call` statement of the MMM subroutine must be made.
3.  MMM library files must be linked against other binary object files.

See the separate documentation [MMM Library](doc/MMMLibrary.md) for more information about the MMM module and MMM subroutine.

The MMM library files that must be linked are `libmmm.a`, which is the static-link library, and `modmmm.mod`, which is the Fortran module file.  Both of these files are provided in the `/lib` directory.

Linking of `libmmm.a` against other binary object files should only involve putting `libmmm.a` in the object file list, as long as the file can be located by the compiler. Note that only static linking is supported in this version. Please follow the instructions of your Fortran compiler to set appropriate options.

The compilation of any source file that uses the `modmmm` module requires the compiler to correctly locate the module file `modmmm.mod` Most compiler search `*.mod` files in directories listed after the -I option. Module files are generally incompatible among different compilers. You will not be able to compile the driver program with compiler B if the MMM module is compiled using compiler A.

The provided MMM library files are made available using different compilers and can be found in the tagged releases on Github.  If your specific compiler is not supported, then please [Contact](#contact) us in regards to adding support for your compiler.


## Manual Compilation

If the `make` utility does not work, or if the build is being done on an unsupported platform, then the compilations may need to be done manually. 

To do this, compile the driver program and link the object codes together to produce the executable `test/mmm.exe`:  
```bash
    $ cd test  
    $ f90 -c -I../lib testmmm.f90 -o testmmm.o  
    $ f90 testmmm.o ../lib/libmmm.a -o mmm
```

>TODO: Needs to be updated to include `mmmTestSupport.f90` file

Note that how the module file `modmmm.mod` is located by the `-I` option and how the static-link library `libmmm.a` is linked with other object files. To generate debugging-ready binary files, replace all `-c` with `-g -c`.  

## Contact

If you have any questions, please contact  [Tariq Rafiq](mailto:rafiq@lehigh.edu).
