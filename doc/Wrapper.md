# Wrapper Documentation

A Wrapper for the MMM subroutine has been provided in the `wrapper` directory.  The Wrapper has been designed as a means to run MMM using external codes written in languages other than Fortran 90.  In contrast to the Test Driver, the Wrapper provides minimum error checks on input files, no support for input variable calculations, does not return the values of input variables sent to MMM, and does not support IMAS inputs.  This was done to improve the performance of the Wrapper relative to the Test Driver, which will be noticeable when continuously running MMM using many sets of input data.

## Contents

- [Overview](#overview)
- [Non-Fortran Support](#non-fortran-support)

## Overview

The Wrapper uses the same input file format as the Test Driver, so input files can be used in either program as long as the values of all input variables are provided.  The Wrapper is built and run in the same manner as the Test Driver, and also supports the use of the included plotting script.  Please reference the separate [Test Driver](TestDriver.md) documentation for more details on how to build and run the Wrapper using the provided sample input files.

Since the Wrapper was written as a streamlined and simplified version of the Test Driver, the `wrapper/mmm_wrapper.f90` file provides the simplest example of how to call the MMM subroutine from another Fortran 90 code.  Please reference this file to see the minimum setup needed to call the MMM subroutine.

## Non-Fortran Support

External codes written in languages such as Python or MatLab can run MMM via the compiled wrapper using the following process:

1. Write an input file `input.dat` from variables loaded in an external code.
2. Run the program `wrapper/mmm.exe` from the external code using a terminal command.
3. Import the resulting output variables in `output.dat` back into the external code.

The `wrapper/mmm.exe` program must be run from the same directory containing the input file.  The resulting output file is formatted using comma separated values (CSV), and can be imported accordingly.  When looping through this process multiple times within one program call, it is recommended to either delete or move the output file after its contents are imported into your external code.  This will ensure that your external code will not reread the same output file over and over in the event that new output files fail to be created for any reason.

The simplicity of this process allows MMM to be run via external non-Fortran codes without needing to compile the external code along with MMM.  This replaces the need for interfaces such as a MEX function (MatLab) or F2PY (Python), while only sacrificing a minimal amount of performance due to the overhead of reading and writing input and output files.

---

[Return](../README.md)
