# Update History

- [v9.1.0](#v910)
- [v9.0.10](#v9010), [v9.0.9](#v909), [v9.0.8](#v908), [v9.0.7](#v907), [v9.0.6](#v906), [v9.0.5](#v905), [v9.0.4](#v904), [v9.0.3](#v903), [v9.0.2](#v902), [v9.0.1](#v901)
- [v8.2.3](#v823), [v8.2.2](#v822), [v8.2.1](#v821), [v8.2.0](#v820), [v8.1.0](#v810)
- [v7.2.0](#v720), [v7.1.0](#v710)

## v9.1.0
   
###### 2024/03/22  ([Top](#update-history))

This is a major update that increases the precision of all models and makes
v9.1.0 twice as fast as the previous version, v9.0.10.  Numerous improvements
have been made to the W20 model, and minor improvements have been made to
other models.  The source code has also been reworked to keep each internal
model separated and modular, and to allow for easier integration of future
models.

**New Source Files:**
- `mmm_support` Required by all internal modules
- `mmm_main` Replaces modmmm
- `mmm_w20` Replaces w20mod
- `mmm_dbm` Contains DBM module
- `mmm_etgm` Contains ETGM and ETG modules
- `mmm_mtm` Contains MTM module
- `mmm_debug` Special debug features that are not included in TRANSP builds

**Output Channel Changes:**
- The thermal electron and electron particle channels were swapped
- New public channel identifiers created to reference channels
> [!Warning]
> Output channels should no longer be referenced using hard-coded indices

**Public Parameter Changes:**
- Parameters now specified in `mmm_support` (not designed to be used by external programs)
- Copies of public parameters for external programs declared in `mmm_main` (see below)
- Parameter names have been updated to reduce chances of naming conflicts in external codes

```fortran
! MMM Dimensions
Integer, Parameter, Public :: &
   MMM_NCH   = 6, &  ! Number of transport channels
   MMM_NFREQ = 2, &  ! Number of frequency channels
   MMM_NMODE = 5, &  ! Number of internal models + 1
   MMM_NOPT  = 20    ! Maximum number of internal switch options per model

! Transport channel identifiers
Integer, Parameter, Public :: &
   MMM_KTI = 1, &  ! Thermal Ion
   MMM_KTE = 2, &  ! Thermal Electron
   MMM_KNE = 3, &  ! Particle Electron
   MMM_KNZ = 4, &  ! Particle Impurity
   MMM_KVT = 5, &  ! Toroidal Velocity
   MMM_KVP = 6     ! Poloidal Velocity

! MMM frequency channel identifiers
Integer, Parameter, Public :: &
   MMM_KION = 1, &  ! Ion Direction
   MMM_KELC = 2     ! Electron Direction

! Identifiers for internal models
Integer, Parameter, Public :: &
   MMM_KW20  = 1, &   ! Weiland 20
   MMM_KDBM  = 2, &   ! Drift-Resistive-Inertial Ballooning Modes
   MMM_KETGM = 3, &   ! Electron Temperature Gradient Modes
   MMM_KMTM  = 4, &   ! Microtearing Modes
   MMM_KMMM  = 5      ! All Models
```
See `mmm_wrapper.f90` for implementation examples.

**Interface Changes:**
- Solver gradients added: `z_gti_solver, z_gte_solver, z_gne_solver, z_gnz_solver, z_gvtor_solver, z_gvpol_solver`
- Fast ion variables added (Optional): `z_gnf, z_tfpa, z_tfpp, z_gtfpa, z_gtfpp`
- Total diffusivity variables removed: `xti, xte, xde, xdz, xvt, xvp`
- Total diffusivity array added (dim 6): `diff`
- Effective flux array renamed from vflux to `flux`
- Component outputs renamed: xdeW20 to `xneW20`, xdeDBM to `xneDBM`

**Input File:**
- Input options reworked and previous input files are obsolete
- A simplified namelist `mmm_options.nml` has been provided for use on TRANSP

**New Input Options:**
- Split convective velocity and diffusivity for all W20 channels
- Convert negative diffusivity to convective velocity
- Allow negative diffusivity output
- Modify diffusivity using solver gradients
- Limit large diffusivity values due to small input gradients
- Specify max convective velocity output values

**All Models:**
- Solver gradients were added to modify MMM output diffusivity so that flux calculations in associated solvers match MMM flux values
- Updated calculation for ion-ion collision rate $\nu_{ii}$
- Fast ion variables not implemented yet, and have been added in preparation for future models
- Flux output is now effective flux, which includes convective components.
- Effective flux calculations do not depend on whether convective velocities are used, or solver gradients are used
- Fixed edge-case error in the eigensystem solver algorithm

**W20 Updates:**
- Both electron and ion directions use same kyrhos value now
- Convergence loop upgraded, which increases precision and reduces noise of results
- W20 source checked line-by-line with original Weiland code, resulting in various fixes being made
- Removed input variables `rmaj0` and `Csound0`
- New optional output variables added for improved diagnostics
- All references to thermal ion density `ni` replaced with hydrogenic ion density `nh`
- Fixed sign issue in calculation involving Bpol derivatives
- Bpol derivatives `dbp, d2bp` should now be differentiated with respect to rmin, instead of $\hat{\rho}$
- Major optimizations implemented, which doubled the speed of W20 relative to v9.0.10

**DBM Updates:**
- DBM model rolled back to previous v9.0.1 implementation
- New kyrhos scanning strategy that increases kyrhos precision by ~500 times and doubles the speed of DBM relative to v9.0.10

**ETGM Updates:**
- New kyrhos scanning strategy that increases kyrhos precision by ~500 times and doubles the speed of ETGM relative to v9.0.10

**MTM Updates:**
- Minimum kyrhos count increased to 200 for increased precision of MTM results

**Test Driver:**
- The wrapper should now be used for all MMM testing
- Wrapper output now split between standard and developer output variables (two separate files)
- New real `rMMM` and integer `iMMM` arrays added to specified options for all models
- Custom input file names can be specified in the command line call (see `mmm_wrapper.f90` for details)
- Test driver depreciated until it is updated to work with recent changes

## v9.0.10
   
###### 2022/12/23  ([Top](#update-history))

**Interface Changes:**
- Input variable `z_bpol` added
- Input variable `z_gni` removed
- Output variable `xteETG` removed
- Public parameter `KETG` removed

**Misc Changes:**
- Removal of unneeded R/R0 factor from term in W20
- Diffusivity factor in W20 now calculated from physical constants
- Minimum gradient limits used in W20 now applied to all component models
- Ion density and its gradient derived from quasi-neutrality
- References to ion mass replaced with hydrogenic ion mass
- Internal calibration for MTM corrected

**CDF Input:**
- Calculations for bpol and zeff added
- Additional logging and error checks added
- Option to use experimental profiles removed

> [!NOTE]
> Sample inputs in the input directory have not yet been updated to reflect these interface changes, since these files will likely be removed in a future update.


## v9.0.9

###### 2022/12/14  ([Top](#update-history))

**CDF Input:**
* New program written in python that generates input files for MMM using output CDF from TRANSP.

**ETGM + Horton ETG Merger:**
* Input options for Horton ETG have been merged with ETGM in the input file.
* Horton ETG diffusivity is now given in the xteETGM output variable, when Horton is enabled.
* All other Horton related inputs and outputs have been depreciated, however no interface changes have been made.

**Misc:**
* The w20 correlation loop is now a public subroutine, and the initial wexb value was updated to use the input wexb, instead of being set to zero.  This difference is negligible from a numerical standpoint, but it should more accurately represent the original intention of the correlation loop.
* Convective velocity components for the impurity and toroidal channels are now summed in the Weiland model, instead of in modmmm.  The impurity convective velocity is now explicitly set to zero, and the toroidal convective velocity is unchanged.
* The diffusivity outputs for ETGM and DBM now reflect the selected model (Default or Alternate).  Previously, alternate diffusivity output could only be viewed in developer mode, and was not available in TRANSP.
* The xte capping scheme option for MTM was removed, and the capping scheme is now default behavior for MTM.
* The order of some input switch options has changed.
* Reverted minimum MTM kyrhos scan count back to 100.
* Fixed an indexing error in R8TOMSQZ when using gfortran in debug mode; results were not affected.

## v9.0.8
###### 2022/12/05  ([Top](#update-history))

**Model Updates:**
- Updated system equations for DBM model
- Fixed error in the alternate particle diffusivity definition in DBM
- Increased minimum kyrhos scan count for MTM from 100 to 200
- Depreciated gni input variable

**Debugging Updates:**
- Debug build option updated to write input and output profiles per MMM call
- Debug mode designed to be used when running TRANSP with a custom MMM library
- Updated hidden option to overwrite component output diffusivities with input variable values

**Input Switch Simplification:**
- Moved several options to internal parameters
- Calibration should now be handled by modifying the cmodel values, at the top of the input file
- Maximum absolute diffusivity bounds are now specified, instead of specifying both max and min
- A separate integer switch controls minimum diffusivity values for all channels (either 0, or negative of the max value)


## v9.0.7
###### 2022/11/10  ([Top](#update-history))

This update only changes the MMM subroutine interface, relative to the previous version.

There are now two distinct MMM interfaces, depending on what build options are used:
- The main interface `(make)`.  Only includes output variables intended for public release
- The developer interface `(make dev)`.  Includes additional output variables not intended for public release

## v9.0.6
###### 2022/11/09  ([Top](#update-history))

**Interface Changes:**
- Updated the MMM interface, depending on which build option is used
- The default interface was reverted to the MMM 9.0.1 interface for compatibility with the current tshare version
- Developer mode (`make dev`) includes additional output variables in the interface, which aren't designed to be accessible in TRANSP
> Developer mode has not been added to the master makefile yet

**Directory Changes:**
- Updated file structure to be consistent with PPPL conventions
- Renamed `src` directory to `lib`
- Added new `include` directory.  Mod files are now built directly in the `include` directory

**Input Variable Changes:**
- Deprecated input variable: ai (now derived in MMM)
- Taking absolute value on input variables: btor, bu, q


## v9.0.5
###### 2022/10/31  ([Top](#update-history))

Note: This is a minor update to help with diagnostic feedback

**Misc Changes:**
- Added developer mode preprocessor directive (make dev)
- Added documentation for building with ifort on Windows
- Added version variable to modmmm

## v9.0.4
###### 2022/09/26  ([Top](#update-history))

**W20 Convergence Loop Overhaul:**
- The algorithm used for mode convergence was greatly simplified, making it easier to read and understand.
- Stricter criteria is now used for mode convergence, which should improve the accuracy of W20 results.
- The metric used to match iterated solutions with previous solutions during the convergence process was updated to reduce variance in converged solutions (the previous metric lead to noisier solutions due to sometimes choosing bad matches).

**New Optional Output Variables:**
> Optional output variables are just for diagnostic purposes and __should not__ be added to TRANSP.
- gaveW20i
- gaveW20e
- kyrhosW20
- kyrhosW20e
- kparaW20i
- kparaW20e
- kparaDBM
- kparaETGM

**Depreciated Output Variables:**
> Depreciated variables remain in the MMM subroutine interface, but only return values of zero.  These will be fully removed the next time the MMM interface is updated.
- alphaETGM
- kpara2ETGM
- fleETGM
- wdeETGM


**Fixes:**
- gmaW20e now only shows contribution from the electron direction.  This is purely a change to the output variable and not the growth rate used in W20.
- The definition of $k_\parallel$ was updated in the DBM model, which affects the results of $\chi_n^*$.
- Effective shear definitions updated so that they satisfy $|\hat{s}| \geq |s|$; this only impacts the $\nabla\rho$ definition of $\hat{s}$.


## v9.0.3
###### 2022/09/12  ([Top](#update-history))

**W20 Changes:**
- Now calculating variables betae and vei, instead of receiving them as input arguments

**R8TOMSQZ Optimization:**
- codebase rewritten and optimized
- normalization to prevent overflow removed (not needed when using R8)
- replaced some slower computations with faster equivalents
- fewer calculations made when eigenvectors aren't needed
- updated three occurrences of single precision 'cmplx' to 'dcmplx'
- most GOTO statements removed


## v9.0.2
###### 2022/07/12  ([Top](#update-history))

**Model Changes:**
   - Removed $R/R_0$ factor applied to gradients in the W20 model
   - Applied a limit to temperature ratios $T_e/T_i$ and $T_i/T_e$

**Misc Changes:**
   - Added new set of input files in new input folder
   - Removed old input files from test folder
   - Updated documentation for the Test Driver and Wrapper


## v9.0.1
###### 2022/07/02  ([Top](#update-history))

**MMM Module Updates** 
  - Complete rewrite of codebase 
  - Input variable values are now verified 
  - Potential division by zero errors removed 
  - All non-local code removed 
  - Flux calculations corrected for all output channels

  **Weiland Model Updates** 
  - Complete rewrite of codebase 
  - Now runs over three times as fast 
  - Several corrections to Weiland model updates introduced in 8.1.0 
  - Real frequency and growth rate channels reduced from 4 to 2 

  **ETGM Model Updates** 
  - Includes dependence on geometry and kyrhos scan 
  - Removed collisionality switch; several new switches added 

  **Horton ETG Updates** 
  - No longer runs by default 
  - Fixed bug where Horton ETG was redefining thermal velocity

  **DRIBM Model Updates** 
  - Introduction of geometry dependence and kyrhos scan 
  - New set of matrix equations

  **MTM Model Updates** 
  - Runtime now negligible compared to other models 
  - Complex cubic solver runs 30% faster 
  - Removal of inertia switch 
  - Mean atomic mass of ions introduced 

  **Input Argument Updates**
  - New input variables: bu, gbu, dbp, d2bp, gxi, amin 
  - Input variable array sizes now explicitly set to npoints 
  - Keyword support for option switches removed 
  - Many new model input switch options added 
  - Renamed option switches, input variables, and several public parameters 
  
  **General Updates**
  - TestMMM input of 2nd kind removed 
  - MMM Wrapper and plotting script added 
  - License and terms of use updated

## v8.2.3
###### 2022/03/29  ([Top](#update-history))
            
**General Updates**
- Added input option limits on W20 particle diffusivity <br>
- Several fixes related to IMAS support <br>

## v8.2.2
###### 2022/02/14  ([Top](#update-history))

**MTM Model Updates**
- Corrected sum calculation and updated calibration
- Added inertia switch

**ETGM Model Introduced**
- Designed to replace the existing Horton ETG model

**General Updates**
- Improved IMAS support
- General stability improvements

## v8.2.1
###### 2021/06/01  ([Top](#update-history))

**General Updates**
- Fixed default settings in TestMMM
- Added optional kyrhosMTM output variable

## v8.2.0
###### 2021/04/13  ([Top](#update-history))

**General Updates**
- Introduced support for IMAS


## v8.1.0
###### 2021/04/12  ([Top](#update-history))

**Weiland Model Updates**
- Introduced current gradient term
- Updated several equations

**General Updates**
- Updated file structure and removed old MMM95 files

## v7.2.0
###### 2019/09/24  ([Top](#update-history))

MTM Model Introduced 

## v7.1.0
###### 2010/02/11  ([Top](#update-history))

Release of MMM 7.1.0


---

[Return](../README.md)
