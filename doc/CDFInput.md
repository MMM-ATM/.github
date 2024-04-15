<h1 align="center">CDF Input<a name="top"></a></h1>

CDF Input quickly generates input files for MMM using output CDF from TRANSP runs.  CDF Input can be run both locally and on PPPL servers.

## 📚 Contents
- [Installation](#-installation)
- [Example](#%EF%B8%8F-example)
- [Modules](#%EF%B8%8F-modules)
- [Contact](#%EF%B8%8F-contact)


## 💿 Installation


This program requires Python v3.8+ 
- https://www.python.org/downloads/

The following Python packages are also required:
* [numpy](https://numpy.org/)
* [scipy](https://scipy.org/)
* [netCDF4](https://unidata.github.io/netcdf4-python/)

### Installation Guides

<details closed><summary><b> &ensp; PPPL Cluster Installation &ensp; </b></summary><dl><dd>
<br>

The most recent Python version available on PPPL must be loaded.  At the time this was written, the most current version is:
```sh
module load python/3.11.4
```

Since numpy is already installed, only the scipy and netCDF4 packages are needed.  The pip install commands need to be written as:
```sh
pip3 install scipy --user
pip3 install netCDF4 --user
```

</details>

<details closed><summary><b> &ensp; General Installation &ensp; </b></summary><dl><dd>
<br>

Each package can be installed using the Python Package Index via the following terminal commands:

```
pip install numpy
pip install scipy
pip install netCDF4
```

> If the `pip` command does not work, then you likely need to [add Python to your Path](https://datatofish.com/add-python-to-windows-path/) (Windows OS).

</details>

### CDF Files

CDF files can either be placed in the local cdf sub-directory, or they can be accessed directly from the TRANSP archive.  CDF Input will first check the local cdf directory before recursively searching through the TRANSP archive for the specified CDF.  The search directory path can also be modified in the settings module to point to another location as needed.


## ✏️ Example

CDF Input should be run from the command line using the following inputs:
1. `runid`: The runid of the discharge
2. `time`: The input time to use, in seconds

> [!Note]
> The runid is case-sensitive, and the ".CDF" file type should not be included.

For example, to create an input file using 129016W13.CDF at time 0.5s:
```
python3 cdfinput.py 129016W13 0.5
```

Successful use of this program should result in terminal output similar to the following:

```
INFO: Searching for 129016W13...

INFO: CDF 129016W13 loaded
    Path:   /p/transparch/result/NSTX/08/129016W13.CDF
    Points: 40
    Times:  442
    Start:  0.1505s
    Stop:   0.63s

INFO: Input time matched with CDF time
    Input time: 0.5s
    CDF time:   0.4998s
    Index:      323

INFO: wexb loaded using SREXBV2

INFO: Input variables have been verified

INFO: Input file written
    /u/chwilson/cdfinput/input.dat
```

## 🖥️ Modules

This section gives an overview of each of the modules in CDF Input.

### Read CDF

The `readcdf` module is responsible for extracting data from the CDF and saving it in a `Variables` object.  All CDF data pulled by this module reflects the exact values that have been saved in the CDF.  While CDF Input does interpolate and otherwise modify some CDF variables, these operations do not occur until after the Read CDF module has been called.

This module can also be run directly (from the modules directory) to print out a list of all variables and dimensions defined in the CDF.

### Calculations

All variable calculations are handled in the `calculations` module.  CDF Input has been written with the goal of accurately reproducing the same exact input profiles from a CDF as would be passed to MMM by TRANSP during a predictive run.  This has been done so that MMM can reliably reproduce results obtained in TRANSP when run as a stand-alone program. Additionally, when used with a CDF produced from running MMM in TRANSP, CDF Input can also be used to infer the input profile values that were passed to MMM at each point during the run.  Any inaccuracies or changes to variable definitions should first be addressed in TRANSP before CDF Input definitions are updated.

For calculations involving interpolation or differentiation, CDF Input uses the [Akima](https://dl.acm.org/doi/pdf/10.1145/360767.360779) method, which is the same method used by TRANSP.  This method is available as part of the scipy package.  

### Controls

The `controls` module defines each input switch option as a `Control` object grouped together in a single `Controls` object.  These controls are used to set the options that appear at the top of each MMM input file.  Default values for each component model are defined in this module.  Any alterations to these values should be made when initializing the Controls class.

### Settings

The `settings` module handles various settings used by CDF Input, and should be edited as needed.

### Variables

This module defines all variables as `Variable` objects in a single `Variables` object.  All variables are defined as one-dimensional arrays, to avoid issues with mismatching dimensions when making calculations.  Variables with scalar values in the CDF will have their values tiled along the radial dimension to make a one-dimensional array.

Additionally, some CDF variables are interpolated to the XB dimension (zone boundaries), so that all input profiles will be defined on the same dimension.  This is needed as variables in the CDF are often defined on multiple different dimensions.

In the variables module, individual Variable objects can be defined with any of the following properties:
* `absolute_max`: Maximum absolute value of variable
* `absolute_min`: Minimum absolute value of variable (avoid division by zero)
* `cdfvar`: Name of variable to load from CDF
* `desc`: Stores the long_name value from CDF
* `dimensions`: Dimensions of variable (only used for loading vars from CDF)
* `is_absolute`: Take absolute value of variable values
* `is_dimensional`: Whether variable is used to define CDF dimensions
* `is_input`: Whether variable should be written to MMM input file
* `label`: Variable label in LaTeX Format
* `name`: Name of variable
* `physical_min`: Minimum cutoff for values to be physical (used for error checks)
* `units`: Physical units of the variable, in simple format
* `units_override`: Override units in CDF with this value (some CDF units are wrong)
* `values`: Values of variable

If a variable is not found in the CDF, then the value of `absolute_min` will be used in any calculations involving that variable.

Conversions from CDF to MMM units are defined in the `Variables.convert_units` method.  New conversion rules should be added as needed.

### Write

The `write` module is responsible for generating the MMM input file, after all other modules have been called.  Input files will be generated in the same directory from which CDF Input was called.  The input file name is specified in the settings module, and can also be updated dynamically during a CDF Input call, if using this program to generate multiple input files during a single call.

## ✉️ Contact

Please contact [Christopher Wilson](metxchris@gmail.com) in regards to any questions relating to this code.


---

###### [⬆️ Top](#top)&nbsp;&nbsp; [↩️ Return](../README.md)