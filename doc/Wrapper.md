<h1 align="center">MMM Wrapper<a name="top"></a></h1>

The wrapper provides an example of an interface between input data and the MMM Subroutine within the MMM library.  The associated Makefile provides an example of how to link a Fortran program with the compiled MMM library.

<!--In contrast to the Test Driver, the Wrapper provides minimum error checks on input files, no support for input variable calculations, does not return the values of input variables sent to MMM, and does not support IMAS inputs.  This was done to improve the performance of the Wrapper relative to the Test Driver, which will be noticeable when continuously running MMM using many sets of input data.-->

## 📚 Contents
- [Overview](#overview)
- [Non-Fortran Support](#non-fortran-support)

## Overview

The wrapper has been written with the goal of speed and simplicity in mind, so minimal error checks are made on input variables, and no calculations of any input variables are made.  Since the wrapper is just attempting to provide an interface to the MMM subroutine, all variable calculations are assumed to be in order when the wrapper is called.

## Non-Fortran Support

External codes written in languages such as Python or MatLab can run MMM via the compiled wrapper using the following process:

1. Write an input file input.dat from variables loaded in an external code.
2. Run the program wrapper/mmm.exe from the external code using a terminal command.
3. Import the resulting output variables in output.dat back into the external code.

The wrapper/mmm.exe program must be run from the same directory containing the input file.  The resulting output file is formatted using comma separated values (CSV), and can be imported accordingly.  When looping through this process multiple times within one program call, it is recommended to either delete or move the output file after its contents are imported into your external code.  This will ensure that your external code will not reread the same output file over and over in the event that new output files fail to be created for any reason.

The simplicity of this process allows MMM to be run via external non-Fortran codes without needing to compile the external code along with MMM.  This replaces the need for interfaces such as a MEX function (MatLab) or F2PY (Python), while only sacrificing a minimal amount of performance due to the overhead of reading and writing input and output files.

---

###### [⬆️ Top](#top)&nbsp;&nbsp; [↩️ Return](../README.md)
