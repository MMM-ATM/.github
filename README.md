<!-- 
NOTE: All MMM documentation was designed to be viewed on Github using Github flavored markdown.
MathJax support for LaTeX parsing is also assumed.
Public MMM documentation is available at: https://github.com/MMM-ATM/.github
-->
<h1 align="center"> Multi-Mode Model<a name="top"></a></h1>

<h4 align="center"><nav>
  <a href="doc/UpdateHistory.md">Update History</a> <sup><sub>🔹</sub></sup>
  <a href="doc/Publications.md">Publications</a>
</nav></h4>

The physics-based Multi-Mode Model (MMM) is a multi-species, multi-fluid, multi-mode anomalous transport model that calculates electron and ion thermal transport, electron particle transport, impurity transport, and toroidal and poloidal momentum transport in tokamak discharges. MMM comprises of four component models:
<dl>
  <dt>W20</dt>
  <dd> 
    The Weiland model for transport driven by ion temperature gradient (ITG), trapped electrons (TE), kinetic ballooning (KB), peeling, and high mode number MHD modes.
  </dd>
  <dt>DBM</dt>
  <dd>
    The drift resistive inertial ballooning mode (DBM) model for transport driven by gradients, electron inertial effects, and inductive effects.
  </dd>
  <dt>ETGM</dt>
  <dd>
    The electromagnetic electron temperature gradient mode (ETGM) model for electron thermal transport.
  </dd>
  <dt>MTM</dt>
  <dd>
    The microtearing mode (MTM) model for transport driven by electron temperature gradients in both the collisionless and collisionality regimes in the presence of perturbations of the magnetic flux surfaces.
  </dd>
</dl>


## 📚 Contents
- [Documentation](#-documentation)
- [Requirements](#-requirements)
- [Project](#-project)
- [Building](#%EF%B8%8F-building)
- [Testing](#-testing)
- [Plotting](#-plotting)
- [Linking](#%EF%B8%8F-linking)
- [Contact](#%EF%B8%8F-contact)


## 📑 Documentation

Additional documentation located in the doc directory:

- [CDF Input](doc/CDFInput.md)
- [Compiler Flags](doc/CompilerFlags.md)
- [Intel Fortran Installation](doc/IntelFortranInstall.md)
- [MMM Library](doc/Library.md)
- [MMM Wrapper](doc/Wrapper.md)


## ⚡ Requirements

MMM is intended to be built on Windows, Linux, and OSX operating systems using a modern Fortran compiler that targets the Fortran 2018 standard.  A list of verified compilers is provided below; MMM may not run correctly on older compilers.

<details closed><summary><b> &ensp; Windows &ensp; </b></summary><dl><dd>
  <br>

  | Name | Version |
  |------|---------|
  | Intel Fortran (ifx) | 2024.1.0 |
  | Intel Fortran (ifort) | 2021.12.0 |
  | GNU Fortran | 11.3.0 |

</dl></dd></details>

<details closed><summary><b> &ensp; Linux &ensp; </b></summary><dl><dd>
  <br>

  | Name | Version |
  |------|---------|
  | Intel Fortran (ifort) | 2021.5.0,&nbsp; 2019.0.3 |
  | GNU Fortran | 11.3.0,&nbsp; 9.3.0 |

</dl></dd></details>

Window users may use gfortran via Cygwin (www.cygwin.com), although MMM runs much faster when compiled with an Intel compiler on Windows.  See the [Intel Fortran installation guide](doc/IntelFortranInstall.md) for more details.

MMM has a few additional dependencies, all of which are freely available either on Linux systems or through Cygwin for Windows users:
- The make utility (https://www.gnu.org/software/make/) is needed to build MMM using the included Makefiles.
- A shell utility (https://www.gnu.org/software/bash/) is required to use the included plotting script.
- Gnuplot (http://www.gnuplot.info/) is required to use the included plotting script.


## 📁 Project

<dl>
  <dt>cdfinput</dt>
  <dd>Contains the <a href="doc/CDFInput.md">CDF Input</a> program, which can be used to generate MMM input files from TRANSP output CDF.  This program was used to generate all of the input files in the data directory.</dd>

  <dt>data</dt>
  <dd>Contains 40 sample input and output files for testing MMM, which were built from various TRANSP discharges and machine types.  See <a href="#-testing">testing</a> for more details.</dd>

  <dt>include</dt>
  <dd>Contains the compiled .mod files.  Object files are also created here, but are cleaned after the MMM executable is built.</dd>

  <dt>lib</dt>
  <dd>Contains the compiled MMM library.  The <a href="doc/Library.md">library</a> documentation covers all of the input and output variables used by MMM, as well as the input options.</dd>

  <dt>src</dt>
  <dd>Contains the source coded needed to compile the MMM library, along with the associated Makefile.</dd>

  <dt>wrapper</dt>
  <dd>Contains the compiled executable <i>mmm.exe</i> and the <i>mmm_wrapper.f90</i> sample Fortran code which is used as an interface to call the MMM subroutine.  See the <a href="doc/Wrapper.md">wrapper</a> documentation for more details.</dd>
</dl>


## 🛠️ Building

MMM can be built from either the main or wrapper directories using the included Makefiles.  Issue Makefile commands using your system console from the directory of the Makefile.

### Environment Variables

Set the environmental variable `MMMFC` to the compiler command on your system.  ifort is used as the default compiler if `MMMFC` is not set.

| MMMFC      |Compiler            | 
|------------|---------------------|
|  ifx       | Intel Fortran (new) |
|  ifort     | Intel Fortran (depreciated) |
|  gfortran  | GNU Fortran         |

<details closed><summary><b> &ensp; Example Commands &ensp; </b></summary><dl><dd>
  <br>

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

</dl></dd></details>

Additional environment variables have also been created to fine-tune MMM optimizations.  Setting these variables to 0 will disable their associated behavior.

|  Environment Variable   | Default Value     | Usage               |
|-------------|----|-----------------------------------|
|   MMMOMP    | 1  | Enables Open MP parallelization. This option has a great impact on performance, however gfortran users via Cygwin may notice decreased performance when more than 2 threads are used (as specified in the input file).                                    |
|   MMMIPO    | 0  | Enables interprocedural optimizations. This will increase the size of the MMM library, and may lead to minor performance improvements. |
|   MMMINTEL  | 0  | Enables optimizations specific for Intel chips.  This option can lead to noticeable boosts, especially on newer Intel chips.  This option has no effect when used with gfortran. |

> [!WARNING]
> Do not enable the `MMMINTEL` flag on AMD chips, as this will actually cause MMM to run slower.  This is not a bug, as Intel has intentionally made their chip-specific optimizations run slower on AMD chips, due to competitive reasons.

### Build Commands

The MMM program can be compiled using different build modes, detailed below.  The make commands are the same for all operating systems.

<details closed><summary><b> &ensp; Default Build &ensp; </b></summary><dl><dd>
  <br>

  ```make
  make clean all
  ```

  The default build is used when MMM is compiled in TRANSP.

</dd></dl></details>


<details closed><summary><b> &ensp; Developer Build &ensp; </b></summary><dl><dd>
  <br>

  ```make
  make clean dev
  ```

  The developer build modifies the MMM interface and includes additional output variables.  Developer output variables are saved separately to `dev.dat` files.

</dd></dl></details>

<details closed><summary><b> &ensp; Debug Build &ensp; </b></summary><dl><dd>
  <br>

  ```make
  make clean dbg MMMIPO=0
  ```

  The debug build enables debugging different debugging flags on gfortran and Intel compilers to help with testing.  Additionally, debugging mode will make MMM write out all input and output variables to individual text files each time MMM is called.  This can allow users to see all of the calls being made to MMM when MMM is used as a custom library in TRANSP Tshare builds.  We've also disabled the build flag for interprocedural optimizations here with the `MMMIPO=0` command.

</dd></dl></details>

<details closed><summary><b> &ensp; Sample Output Build &ensp; </b></summary><dl><dd>
  <br>

  ```sh
  make clean dev MMMIPO=0 MMMINTEL=1 MMMOMP=1 MMMFC=ifort
  ```

  This build command was used on a Windows machine to produce all of the sample output files located in the data directory.  Interprocedural optimizations were disabled as this optimization can potentially have a small impact on results, as these output files are used for testing MMM builds.  Otherwise, using the `MMMIPO` flag should be fine.

</dd></dl></details>

<details closed><summary><b> &ensp; Clean Files &ensp; </b></summary><dl><dd>
  <br>

  ```make
  make clean
  ```

  This will clean out build directories of all build files, including the executable and all debugging output.  

</dd></dl></details>

The Makefile [build flags](doc/CompilerFlags.md) have also been documented for developer reference.

## 🧪 Testing

The MMM build can be tested using the 40 sample input and output files provided in the data directory.  The following list of make commands can be run from either the main or wrapper directories.

### Makefile Commands

<details closed><summary><b> &ensp; <code>make runall</code> &ensp; </b></summary><dl><dd>
  <br>

  The `make runall` command can be used from either the main or wrapper directories to run MMM using all input files in the data directory.  Input files are detecting by matching the string format `input*.dat`, so that additional input files can be added to the data directory without having to update the Makefile command.

</dd></dl></details>

<details closed><summary><b> &ensp; <code>make cpall</code> &ensp; </b></summary><dl><dd>
  <br>

  The `make cpall` command is used to copy all output files matching the string `output*.dat` to sample output files, `sample_output*.dat`.  Using this command will overwrite all of the sample output files provided with the build, but doing so will allow you to test different build configurations on your machine.

</dd></dl></details>

<details closed><summary><b> &ensp; <code>make diffall</code> &ensp; </b></summary><dl><dd>
  <br>

  The `make diffall` command will run a diff of all output files in the data directory against their associated sample output files.  This command has been set to only show the file names of mismatching files. The following example shows one of the 40 diff calls that should appear in the console when using this command:

  ```bash
  cd wrapper; make diffall
  make[1]: Entering directory '/home/metxc/mmm/wrapper'
  diff ../data/output08505Z06.dat ../data/sample_output08505Z06.dat --brief --ignore-space-change;
  ```

</dd></dl></details>


<details closed><summary><b> &ensp; <code>make diffall+</code> &ensp; </b></summary><dl><dd>
  <br>

  The `make diffall+` command can be used to show the actual values of each line that differs between an output and associated sample output file.  Unfortunately the names of the files with differing lines aren't shown, but the order of files shown will match the order given when the `make diffall` command is used.  

</dd></dl></details>

### Testing Results

We expect output between the ifort and ifx compilers to generally be the same, but minor differences between gfortran and Intel compilers are common.  Typically these differences occur in regions where the output flux is extremely small.  The following files are expected to show one-line differences at edge of the plasma (final row of output values) when gfortran is used with the `make runall` command:

```bash
Files ../input/output128474X01.dat and ../input/sample_output128474X01.dat differ
Files ../input/output133964Z02.dat and ../input/sample_output133964Z02.dat differ
Files ../input/output141069A01.dat and ../input/sample_output141069A01.dat differ
Files ../input/output142111A03.dat and ../input/sample_output142111A03.dat differ
```

If no message of the above form is seen when using either diff command, then all output files match their associated sample output files.

Please [contact](#%EF%B8%8F-contact) us if major differences in output values are seen with your MMM build.

## 🎨 Plotting
MMM output is given as a simple text spreadsheet, which can be interpreted using plotting tools such as gnuplot. The provided plot.sh script can be run from any directory containing an output file output.dat to produce plots of all input and output variables in that file.  From the wrapper directory, run the plot.sh script using the terminal command

```bash
# Linux, Cygwin
../plot.sh

# Windows
sh ..\plot.sh  
```

This will plot all output variables within a new png sub-directory relative to your current directory.  If the png directory already exists, then any existing plots will be overwritten.  Individual plots can be saved either by moving them to a new directory, or by renaming the files so that they don't coincide with a variable name.  Example plots of the ETGM and MTM thermal electron diffusivity produced by the plotting script are given below:

<p align="center">
  <picture><img src="doc/img/xteETGM.png?raw=true"></picture>&nbsp; <picture><img src="doc/img/xteMTM.png?raw=true"></picture>
</p>

The plotting script also serves as an example on how to plot MMM variables in CSV format.

> [!WARNING]
> This script must have line endings set to Unix, even when used on Windows.  If the line endings are inadvertently switched to the Windows standard, then the following error will show when using this script:
> ```"$'\r': command not found```


## ⛓️ Linking

To use MMM in your own program, the following steps need to be followed:

1.  A `use` statement must reference the MMM module.
2.  A `call` statement of the MMM subroutine must be made.
3.  MMM library files must be linked against other binary object files.

See the separate documentation [MMM Library](doc/Library.md) for more information about the MMM module and MMM subroutine.  The wrapper directory also serves as an example of how to build an executable using the MMM library.

## ✉️ Contact

For MMM related questions, please contact [Tariq Rafiq](mailto:rafiq@lehigh.edu).  
For technical questions, please contact [Christopher Wilson](mailto:metxchris@gmail.edu).

---

###### [⬆️ Top](#top)
