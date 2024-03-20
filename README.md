# Multi-Mode Anomalous Transport Model

This software package contains the multi-mode anomalous transport module (MMM). MMM is a theory-based transport model which has been used to predict temperature, density and toroidal rotation profiles for tokamak plasmas. The model includes an improved Weiland model for the ITG, TEM, and MHD modes, the Horton model for short wavelength ETG model and a new model for the drift resistive inertial ballooning modes (DRIBM). The ETG transport threshold in the Horton model is refined by using threshold obtained from toroidal gyrokinetic ETG turbulence. These components of transport models provide contributions to transport in the different regions of plasma discharge.


## Contents
- [Documentation Links](#documentation-links)
- [Requirements](#requirements)
- [Project Contents](#project-contents)
- [Build Instructions](#build-instructions)
- [Testing Instructions](#testing-instructions)
- [Plotting Instructions](#plotting-instructions)
- [Linking Instructions](#linking-instructions)
- [Contact](#contact)

## Documentation Links

Additional documentation located in the `doc` directory:

- [Update History](doc/UpdateHistory.md)
- [MMM Library](doc/Library.md)
- [Test Driver](doc/TestDriver.md)
- [Wrapper](doc/Wrapper.md)
- [IMAS Interface](doc/UsingIMAS.md)
- [Ifort Installation](doc/Ifort.md)
- [CDF Input](doc/CDFInput.md)
- [References](doc/References.md)

## Requirements

MMM is intended to be built on an x64 POSIX-compliant OS (UNIX, Linux, Cygwin, etc.) with a Fortran 90 compiler. The rest of this README will assume that you are running an x64 Linux system with a BASH shell.  Support for the Windows OS is included via Cygwin (www.cygwin.com), although the compiler options may be more limited. Building on other architectures may be possible but has not been tested. Users and developers are welcome to send in their experiences building MMM on different architectures. 

The `make` command must be installed in order to use the included make files.

Gnuplot (http://www.gnuplot.info/) is required to use the included plotting script.  This program is freely available on Unix systems and can also be installed within Cygwin.

## Project Contents

This project contains the following sub-directories:


- :file_folder: **doc**

    Contains MMM documentation in Markdown format.  Markdown is best viewed on Github or through a Markdown viewer.

- :file_folder: **include**

    Contains the MOD files, which are built when the MMM Library is compiled.

- :file_folder: **cdfinput**

    Contains the [CDF Input](doc/CDFInput.md) script and a sample input file (`sample/sample_input.dat`) for MMM.  The expected corresponding output is also provided (`sample/sample_output.dat`), which can be used to test your MMM installation.

    See [Testing Instructions](#testing-instructions) for more details.

- :file_folder: **legal**

    Contains the following legal documents that pertain to MMM:
    - [End User License Agreement](/legal/eula.pdf)
    - [Terms Of Use](/legal/tou.pdf)

    Please read these documents carefully before using MMM.

- :file_folder: **lib**

    Contains the compiled MMM library file.  The library file can be used with the provided `mmm_wrapper.f90` script, as well as your own Fortran90 code.

    See the separate [MMM Library](doc/Library.md) documentation for more details.

<!-- - :file_folder: **test**

    Contains the `testmmm.f90` code that can be used to test and run MMM.

    `testmmm.f90` reads in variable data from `input.dat` files and provides some error and verification checks on the imported data.  In addition, `testmmm.f90` provides support for derivative and gradient calculations, if these values aren't provided in the input file.

    See [Testing Instructions](#testing-instructions) for more details.
 -->
- :file_folder: **wrapper**

    Contains the `mmm_wrapper.f90` sample Fortran90 code that calls the MMM subroutine using `input.dat` files from the `input` directory.  The wrapper can be used to compile MMM using the provided Make file. Additionally, the wrapper serves as an additional reference in regards to making the MMM subroutine call.

    The wrapper is a simplified and streamlined version of testmmm, and was designed to be modifiable by users.

    See the separate [MMM Wrapper](doc/Wrapper.md) documentation for more details.


## Build Instructions

Follow this two-step procedure to build the binaries using the MMM library:

1.  Set the environmental variable `MMMFC` to the compiler command on your system.  gfortran is used as the default compiler if `MMMFC` is not set.  MMM Makefiles support the following compilers:

    |  Compiler       | MMMFC     | Installation Guide                |
    |-----------------|-----------|-----------------------------------|
    |   GNU Fortran   | gfortran  |                                   |
    |   Intel Fortran | ifort     |  [ifort](doc/Ifort.md)            |

    #### Example Commands:
    
    Bash (Cygwin):
    ```bash
    export MMMFC=gfortran
    ```
     Linux:
    ```bash
    setenv MMMFC gfortran
    ```
    Windows:
    ```bash
    setx MMMFC gfortran
    ```

2.  To compile the whole package, including the MMM library file in `lib` and the stand-alone driver program `test/mmm.exe`, simply run:  

    ```bash
    $ make clean all
    ```

    inside the main directory to invoke the master makefile, which will then invoke secondary makefiles to build the module and the driver program. The binary files (\*.o, \*.a, \*.exe) are placed alongside their source codes.

    To delete all binary and debugging files, use:  

    ```bash
    $ make clean
    ```

    For users with access to the MMM library file, the following build options are available:

    1.  
        ```bash
        $ make clean all
        ```
        This is the default build option, which is intended to be used in TRANSP.

    2. 
        ```bash
        $ make clean dev
        ```
        This is the development build option, which is intended to be used for stand-alone development.  Development mode adds additional variables to the MMM interface, and also prints more feedback during MMM runs.  This option cannot be used in TRANSP, due to the interface changes it contains.

    3. 
        ```bash
        $ make clean debug
        ```
        This is the debug build option, which can be used both in stand-alone and in TRANSP.  When used in TRANSP as a custom library file, input and output profiles will be printed to individual files each time MMM is called.  This provides a means to compare raw MMM values with values that appear in the CDF output.

It is possible to build the module and the driver program separately, using only the secondary makefiles. In this case, follow these steps:

1.  Set the environmental variable `MMMFC` to the compiler command on your system.
2.  Change the current directory to the subdirectory where you want to build binaries and issue the `make` command.

## Testing Instructions

The wrapper `wrapper/mmm.exe` requires only one input file called `input.dat`.  Since the input file is a Fortran namelist file, the variables can be arranged in any order.  

To produce the test case:

1.  Change the current directory to the `input/sample`.
2.  Copy the sample input file:
    ```bash
    $ cp sample_input.dat input.dat
    ```
3.  Run the compiled driver program:
    ```bash
    $ ../../test/mmm
    ```

4.  Take a diff between the new output file and the provided sample output file:
    ```bash
    $ diff output.dat sample_output.dat
    ```

Ideally, the diff should reveal few to no differences in values.

See the [Test Driver](doc/TestDriver.md) documentation for more information on `test/mmm.exe`.

## Plotting Instructions
MMM output is given as a simple text spreadsheet, which can be interpreted using plotting tools such as gnuplot. The provided `plot.sh` script can be run from any directory containing an output file `output.dat` to produce plots of all input and output variables in that file.  Please refer to the `plot.sh` script directly for more information on plotting with gnuplot.

> [!WARNING]
> If any errors are encountered using `plot.sh` on a Windows OS, make sure that this file is still using Unix line endings.  This script will completely fail if the line endings get switched to the Windows format.

An example usage of the plotting script can be found in the [Test Driver](doc/TestDriver.md) documentation.

## Linking Instructions

To use MMM in your own program, the following steps need to be followed:

1.  A `use` statement must reference the MMM module.
2.  A `call` statement of the MMM subroutine must be made.
3.  MMM library files must be linked against other binary object files.

See the separate documentation [MMM Library](doc/Library.md) for more information about the MMM module and MMM subroutine.

The MMM library files that must be linked are `libmmm.a`, which is the static-link library, and `modmmm.mod`, which is the Fortran module file.  Both of these files are provided in the `lib` directory.

Linking of `libmmm.a` against other binary object files should only involve putting `libmmm.a` in the object file list, as long as the file can be located by the compiler. Note that only static linking is supported in this version. Please follow the instructions of your Fortran compiler to set appropriate options.

The compilation of any source file that uses the `modmmm` module requires the compiler to correctly locate the module file `modmmm.mod` Most compiler search `*.mod` files in directories listed after the -I option. Module files are generally incompatible among different compilers. You will not be able to compile the driver program with compiler B if the MMM module is compiled using compiler A.

The provided MMM library files are made available using different compilers and can be found in the tagged releases on Github.  If your specific compiler is not supported, then please contact us in regards to adding support for your compiler.

## Contact

If you have any questions, please contact  [Tariq Rafiq](mailto:rafiq@lehigh.edu).
