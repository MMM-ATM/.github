# Test Driver Documentation

> [!WARNING]
> Test driver currently depreciated.  Please use the [wrapper](Wrapper.md) instead.

A Test Driver has been provided in the `test` directory.  The Test Driver has been designed as a means to test and run your MMM installation.  In contrast to the Wrapper, the Test Driver provides maximum feedback on input file error checks, support for some input variable calculations, and the values of input variables sent to MMM.  Because of this, the performance of the Test Driver will not be as good as the performance of the Wrapper, however the difference will be negligible when running MMM using only a single input file.

## Contents

- [Overview](#overview)
- [Input Variable Calculations](#input-variable-calculations)
- [Running Sample Input Files](#running-sample-input-files)

## Overview

The Test Driver can be compiled along with the MMM Library using the provided Makefile.  To build the Test Driver, navigate to the `test` directory and run the command (`make` must be installed on your system):
```bash
make
```

This will create the executable `test/mmm.exe`.  This executable can be run from any folder containing an input file named `input.dat`, which will produce a corresponding output file `output.dat` upon completion.  Several sample input files have been provided in the `input` directory.  A description of how to run the Test Driver using these sample input files is provided in the [Running Sample Input Files](#running-sample-input-files) section below.

The Test Driver supports both default and IMAS input types.  In addition, the Test Driver will calculate any non-essential input variables as needed, such as any of the gradient or derivative variables.  Because of this, both input and output variables will be returned in the created `output.dat` files. Moreover, the Test Driver also performs several error checks on the input file and validation checks on the input variables used to calculate gradients and derivatives.

## Input Variable Calculations

In order to trigger the Test Driver to calculate a gradient or derivative input variable, the first value of that variable must be set to -1000; only this first input point value is needed when using the Test Driver to calculate gradients or derivatives.

The normalized gradient *g<sub>y</sub>* for some variable *y ≠ 0* is calculated as 
```math
g_y = -\frac{R}{y}\frac{dy}{dr},
```
where *R* is the major radius and *r* is the minor radius.  The Test Driver will ensure that either the minimum or minimum absolute value of the variable $y$ is no smaller than 2<sup>-53</sup> ≈ 10<sup>-16</sup> before gradients are taken.  This process is referred to as input variable verification, and is carried out to ensure certain variables are of the correct sign, and that no variables equal zero at any point (to avoid division by zero errors).  For example, none of the density nor temperature variables are allowed to be negative in value, whereas it is permissable for velocity values to be negative.

Additionally, note that the leading negative sign in the above equation is only used when taking normalized gradients of each of the temperature, density, and velocity variables.  Alternatively, the normalized gradients corresponding to the safety factor and magnetic field will use a leading positive sign.  The signs are chosen in this manner to keep the corresponding normalized gradient variables mostly positive in value.

Next, the derivatives of the poloidal magnetic field (*B*<sub>θ</sub>) are calculated by first approximating the poloidal magnetic field from the toroidal magnetic field (*B*<sub>φ</sub>) as
```math
B_\theta = \frac{r}{R} \frac{B_\phi}{q}
```
    
The first (*B*<sub>θ</sub>′) and second (*B*<sub>θ</sub>′′) poloidal magnetic derivatives are taken with respect to the normalized flux surfaces (ρ).  Note that both the first and second derivates will be calculated if either the first value of either of these variables is -1000.

Finally, the optional elongation gradient (*κ*′) is a special case which is calculated as
```math
\kappa^\prime = R_0\frac{d\kappa}{dr},
```
where *R<sub>0</sub>* is the major radius on axis.  Note that this variable is only required when running the Horton ETG model.

Please view the `test/testmmm.f90` file directly for more information on the verificaitons and calculations mentioned in this section.

> TODO: Test that optional variable calculations still works as described.

## Running Sample Input Files

Sample input and output files have been provided in the `input` directory.  These sample input files can be run using either the Test Driver or the Wrapper, however we recommend using the Test Driver as this was the program that was used to generate the provided sample output files.

Note that while sample input files have all of the component models in MMM enabled, the Horton ETG model will not contribute it's electron thermal diffusivity (xteETG) to the total electron thermal diffusivity (xte), since the ETGM model is also enabled.

The full process to running, viewing, and verifying the results of provided sample input and output files is as follows:

1. #### Copying Input Files

    To run an input file, first navigate to the sub-directory you wish to run, then copy the sample input file to a normal input file using the command:

    ```bash
    cp sample_input.dat input.dat
    ```

1. #### Using the Test Driver

    Next, run the Test Driver from the directory of the input file.  Assuming that the Test Driver has already been compiled, use the command:
    ```bash
    ../../test/mmm
    ```

1. #### Plotting Output
    After the output.dat file has been produced, both input and output variables may be plotted using the plotting script:
    ```bash
    ../../plot.sh
    ```
    These variables will be plotted as individual figures within a new `png` sub-directory relative to your current directory.  For example, the total ion thermal diffusivity (xti) produced by MMM may look like the following when plotted:
    
    <p align="center">
        <img src="img/xti.png?raw=true">
    </p>

    This plotting script requires Gnuplot in order to run, which is freely available on both Unix and Cygwin systems.  Additionally, this script also serves as a reference on how to construct your own custom plotting commands of MMM output using Gnuplot.

1. #### Comparing Output

    Finally, your MMM installation can be tested against expected output values by comparing your generated ouput file with the provided sample output file.  To do this, use the command:
    ```bash
    diff output.dat sample_output.dat
    ```

    If your installation is correct, then you should see little to no difference between the two files.


> TODO: Need to verify that these input files are good representations of MMM with respect to each of the provided machines, and that the calculated diffusivity values are calibrated correctly.


---

[Return](../README.md)
