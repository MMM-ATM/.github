# Multi-Mode Anomalous Transport Model

The physics-based Multi-Mode Model (MMM) is a multi-species, multi-fluid, multi-mode anomalous transport model that calculates electron and ion thermal transport, electron particle transport, impurity transport, and toroidal and poloidal momentum transport in tokamak discharges. MMM comprises of four components: 
1. The Weiland model for transport driven by ion temperature gradient (ITG), trapped electrons (TE), kinetic ballooning (KB), peeling, and high mode number MHD modes.
2. A new model of electromagnetic electron temperature gradient mode (ETGM) for electron thermal transport.
3. An updated model of microtearing mode (MTM) for transport driven by electron temperature gradients in both the collisionless and collisionality regimes in the presence of perturbations of the magnetic flux surfaces.
4. An updated model for the drift resistive inertial ballooning mode (DBM) for transport driven by gradients, electron inertial effects, and inductive effects.

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
- [ifort Installation](doc/Ifort.md)
- [CDF Input](doc/CDFInput.md)
- [Compiler Flags](doc/CompilerFlags.md)
- [References](doc/References.md)

## Requirements

MMM is intended to be built on either Microsoft Windows or x64 POSIX-compliant operating systems (UNIX, Linux, etc.) with a modern Fortran compiler that supports Fortran 2018 standards.  Windows users are able to use Intel compilers, whereas Linux users may use either Intel or the gfortran compiler.  Window users may also use gfortran via Cygwin (www.cygwin.com), although MMM runs much faster when compiled with an Intel compiler on Windows. Building on other architectures may be possible but has not been tested. Users and developers are welcome to send in their experiences building MMM on different architectures. 

The `make` command must be installed in order to use the included make files.  The plotting program Gnuplot (http://www.gnuplot.info/) is required to use the included plotting script, but isn't needed to run MMM.  Both programs are available on Unix systems and can be installed via Cygwin for Windows users.

## Project Contents

This project contains the following sub-directories:


- :file_folder: **cdfinput**

    Contains the [CDF Input](doc/CDFInput.md) program which can be used to generate MMM input files from TRANSP output CDF.  This program was used to generate all sample input files in the `data` directory.

- :file_folder: **data**

    Contains many input files for testing MMM.  Using the command `make runall` from either the main or `wrapper` directory will run MMM using all input files in this directory. See [Testing Instructions](#testing-instructions) for more details.

- :file_folder: **doc**

    Contains MMM documentation in Markdown format.  Markdown is best viewed on Github or through a Markdown viewer.

- :file_folder: **include**

    Contains the MOD files, which are built when the MMM Library is compiled.  Object files are temporarily moved here, but are cleaned after the MMM executable is built.

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

    Contains the compiled executable `mmm.exe` when MMM is built from either the main or `wrapper` directory.  This also contains the `mmm_wrapper.f90` sample Fortran code which is used as an interface to call the MMM subroutine in the compiled MMM library.  The wrapper was designed to serve as a simple example of how to call the MMM library, and may be modified by users as needed.

    See the separate [MMM Wrapper](doc/Wrapper.md) documentation for more details.


## Build Instructions

Follow this two-step procedure to build the binaries using the MMM library:

1.  Set the environmental variable `MMMFC` to the compiler command on your system.  ifort is used as the default compiler if `MMMFC` is not set.  MMM Make files support the following compilers:

    |  Compiler             | MMMFC     | Installation Guide                |
    |-----------------------|-----------|-----------------------------------|
    |   GNU Fortran         | gfortran  |                                   |
    |   Intel Fortran       | ifort     |  [ifort](doc/Ifort.md)            |
    |   Intel Fortran (new) | ifx       |  [ifort](doc/Ifort.md)            |

    #### Example Commands:
    
    ```bash
    # Cygwin
    export MMMFC=ifort

    # Linux
    setenv MMMFC ifort

    # Windows
    set MMMFC=ifort  # Temporary
    setx MMMFC ifort  # Permanent (after restarting command prompt)
    ```

    Environment variables can also be set within the `make` commands (see below).

2.  The MMM program can be compiled using different build modes, detailed below.  The make commands are the same for all operating systems.

    ```bash
    # Default build (used for TRANSP builds)
    $ make clean all

    # Developer build (with additional output variables)
    $ make clean dev

    # Debugging build (with recommended disabling of MMMIPO)
    $ make clean dbg MMMIPO=0

    # Default build with the ifx compiler using Intel chip optimizations
    $ make clean all MMMFC=ifx MMMINTEL=1

    # Clean all files built in lib, src, include, and wrapper.
    $ make clean
    ```

    The library file and executable can also be built separately using the make files in their associated directories.  See the [Compiler Flags](doc/CompilerFlags.md) documentation for more info on the build flags used in each of the make files.

3. Additional environment variables are available to customize the MMM build.  Set each variable to a value of 0 to disable the associated behavior.

    |  Environment Variable   | Default Value     | Usage               |
    |-------------|----|-----------------------------------|
    |   MMMOMP    | 1  | Enables Open MP parallelization. This option has a great impact on performance, however gfortran users via Cygwin may notice decreased performance when more than 2 threads are used (as specified in the input file).                                    |
    |   MMMIPO    | 0  | Enables interprocedural optimizations. This may will increase the size of the MMM library, and may lead to minor performance improvements. |
    |   MMMINTEL  | 0  | Enables optimizations specific for Intel chips.  This option can lead to noticeable boosts, especially on newer Intel chips.  This option has no effect when used with gfortran. |

> [!WARNING]
> Do not enable the `MMMINTEL` flag on AMD chips, as this will actually cause MMM to run slower.  This is not a bug, as Intel has intentionally made their chip-specific optimizations run slower on AMD chips, due to competitive reasons.


## Testing Instructions

Forty input files from various discharges and machine types have been provided in the `data` directory.  The command `make runall` can be used from either the main or `wrapper` directory to run MMM using all input files in the `data` directory.  Sample output files have also been provided, which were built using the following command on a Windows machine:
```bash
make clean dev MMMIPO=0 MMMINTEL=1 MMMOMP=1 MMMFC=ifort && make runall
```

Use the make command `make diffall` to diff output files created using `make runall` against their associated sample output files.  The `make diffall` command has been set to only show the names of files with different values.  

The `make diffall+` command can be used to show the actual values of each line that differs between an output and associated sample output file.  The order of the output will correspond to the order that differing files were listed when the `make diffall` command was used.

When using either `make diffall` or `make diffall+`, the diff commands should appear in the console window.  An example diff command appears below. If no diff commands appear, then double-check that both `output*.dat` and `sample_output*.dat` files are in the `data` directory.

```bash
cd wrapper; make diffall
make[1]: Entering directory '/home/metxc/mmm/wrapper'
diff ../data/output08505Z06.dat ../data/sample_output08505Z06.dat --brief --ignore-space-change;
```

Output between the ifort and ifx compilers should generally be the same, but minor differences between gfortran and Intel compilers are expected.  The following files are expected to show one-line differences at edge of the plasma (final row of output values) when gfortran is used with the `make runall` command:

```bash
Files ../input/output128474X01.dat and ../input/sample_output128474X01.dat differ
Files ../input/output133964Z02.dat and ../input/sample_output133964Z02.dat differ
Files ../input/output141069A01.dat and ../input/sample_output141069A01.dat differ
Files ../input/output142111A03.dat and ../input/sample_output142111A03.dat differ
```

If no message of the above form is seen when using either diff command, then all output files match their associated sample output files.

Please [Contact](#contact) us if major differences in output values are seen with your MMM build.

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

See the separate documentation [MMM Library](doc/Library.md) for more information about the MMM module and MMM subroutine.  The `wrapper` directory also serves as an example of how to build an executable using the MMM library.  See the make file and `mmm_wrapper.f90` source code for further details.

## Contact

If you have any questions, please contact  [Tariq Rafiq](mailto:rafiq@lehigh.edu).
