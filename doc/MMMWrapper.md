# MMM Wrapper Documentation
> TODO: Update Needed

The included file `wrapper/mmm_wrapper.f90` provides an example of how to call the `set_mmm_switches` and `mmm` subroutines using the included library file `libmmm.a`.  Use the included make file `wrapper/make` to compile the wrapper into the program `mmm.exe`.

Running this compiled program will produce a CSV of output variables, named `output.dat`.  Input options and variables should be passed to the program using the included `input.dat` file.  This input file can be updated as needed to change input option and input variable values.

In addition, the wrapper file is user-editable and can be updated to meet individual needs.  Moreover, external codes written in languages such as Python or MatLab can run MMM via the compiled wrapper using the following process:

1. Write an input file `input.dat` from variables loaded in an external code.
2. Run the program `mmm.exe` from the external code using a terminal command.
3. Import the resulting output variables in `output.dat` back into the external code.

---

[Return](../README.md)
