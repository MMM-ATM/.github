# MMM Library Documentation

> Note: This file is still a work in progress.  Some sections will likely change with the final release of MMM v9.0.0.

## Contents
- [Build Instructions](#build-instructions)
- [Public Definitions](#public-definitions)
- [Input Options](#input-options)
- [Input Variables](#input-variables)
- [Output Variables](#output-variables)
- [Output Variables (Components)](#output-variables-components)
- [MMM Subroutine](#mmm-subroutine)
- [References](#references)

## Public Definitions
The MMM module `modmmm` makes the following public definitions available via the compiled library file `libmmm.a`.  The  statement `Use modmmm` is required to access the public definitions in this section.

### Public Subroutines
 Two public subroutines are specified by the MMM module:
```fortran
Public :: mmm               ! Main subroutine
Public :: set_mmm_switches  ! Specify real and integer switch options (Optional)
```
Each of these subroutine calls will be explained in greater detail below (see [MMM Subroutine](#mmm-subroutine)).    Note that the main subroutine `mmm` can be called without calling `set_mmm_switches` beforehand, which will run MMM using default options (see [Model Options](#model-options)).

### Subroutine Dimensions
Public parameters are used to specify the size of various arrays used by MMM:
```fortran
Integer, Parameter, Public :: &
   MMM_NCH   = 6, &  ! Number of transport channels
   MMM_NMODE = 5, &  ! Number of internal models
   MMM_NOPT  = 20    ! Maximum number of internal switch options per model
```
These parameters should be referenced by external codes using MMM for array initialization, as needed.

### Model Identifiers
Public parameters are also used to reference each model `K<MODEL>` within MMM:
```fortran
Integer, Parameter, Public :: &
   KW20  = 1, &  ! Weiland (W20)
   KDBM  = 2, &  ! Drift-Resistive-Inertial Ballooning Modes (DRIBM)
   KETGM = 3, &  ! Electron Temperature Gradient Modes (ETGM)
   KMTM  = 4, &  ! Microtearing Modes (MTM)
   KETG  = 5     ! Horton ETG (ETG)
```
These model identifiers should be referenced by external codes using MMM when specifying internal switch values, as model integer values are subject to change in future updates.

## Input Options

### Model Weights

Model weights are specified by the optional input array `cmodel` of dimension `(MMM_NMODE)` (see [Subroutine Dimensions](#subroutine-dimensions)), where array indices should be referenced using the [Model Identifiers](#model-identifiers).  Setting the weight of any model to 0 will disable that model.  By default, only the W20, DBM, ETGM, and MTM models are enabled.

> Note: The ETGM model must be disabled in order for Horton ETG to contribute to total electron thermal diffusivity.  If both ETGM and ETG are both enabled, then the electron thermal diffusivity from Horton ETG will still be calculated, but only the electron thermal diffusivity from ETGM will be added to the total electron thermal diffusivity.


### Model Options
Real and Integer options are passed in via the optional input arrays `rswitch` and `iswitch`, respectively, with dimensions `(MMM_NOPT,MMM_NMODE)`  (see [Subroutine Dimensions](#subroutine-dimensions)).  These arrays can be initialized with default values by making the following subroutine call:
```fortran
CALL set_mmm_switches(rswitch=rswitch, iswitch=iswitch) 
```
Afterwards, values can be modified as needed before passing these arrays into MMM via the main subroutine call.  The first dimension here corresponds to the model identifier `K<MODEL>` (see [Model Identifiers](#model-identifiers)),  and the second dimension corresponds to the option index `<INDEX>`.  For further reference on this subroutine call and how to edit the values of model options, please see the included files `wrapper/mmm_wrapper.f90` and `wrapper/input.dat`.

The following sections provide tables of real and integer switch options, along with their default values, for each of the models in MMM:
-  [Model Options (Real)](#model-options-real)
-  [Model Options (Integer)](#model-options-integer)

#### Model Options (Real)
Real options are specified by `rswitch(K<MODEL>, <INDEX>)`.    Diffusivity bounds are given in units of m<sup>2</sup>/s.

> Beta Note: Some of these options will be removed in the final release of MMM v9.


| Model | Index | Default | Description                                        |
| ---- | ------ | ------- | -------------------------------------------------- |
| W20  | 1  |  1      | E×B shear coefficient                                  |
|      | 2  |  1      | Momentum pinch scaling factor                          |
|      | 3  |  0.316  | *k*<sub>y</sub>*ρ*<sub>s</sub>  (ion direction)        |
|      | 4  |  -100   | xti: Lower bound                                       |
|      | 5  |  100    | xti: Upper bound                                       |
|      | 6  |  -100   | xde: Lower bound                                       |
|      | 7  |  100    | xde: Upper bound                                       |
|      | 8  |  -100   | xte: Lower bound                                       |
|      | 9  |  100    | xte: Upper bound                                       |
|      | 10 |  -100   | xdz: Lower bound                                       |
|      | 11 |  100    | xdz: Upper bound                                       |
|      | 12 |  -100   | xvt: Lower bound                                       |
|      | 13 |  100    | xvt: Upper bound                                       |
|      | 14 |  -100   | xvp: Lower bound                                       |
|      | 15 |  100    | xvp: Upper bound                                       |
| | | |
| DBM  | 1  |  1      | E×B shear coefficient                                  |
|      | 2  | 0.05    | *k*<sub>y</sub>*ρ*<sub>s</sub> Scan: Lower bound       |
|      | 3  | 0.30    | *k*<sub>y</sub>*ρ*<sub>s</sub> Scan: Upper bound       |
|      | 4  | 1       | *k*<sub>x</sub> / *k*<sub>y</sub>                      |
|      | 5  |  -25    | xti: Lower bound                                       |
|      | 6  |  25     | xti: Upper bound                                       |
|      | 7  |  -25    | xde: Lower bound                                       |
|      | 8  |  25     | xde: Upper bound                                       |
|      | 9  |  -25    | xte: Lower bound                                       |
|      | 10 |  25     | xte: Upper bound                                       |
|      | 11 |  0.15   | Default diffusivity calibration: Most unstable mode    |
|      | 12 |  0.0075 | Default diffusivity calibration: Sum of modes          |
|      | 13 |  0.45   | Alternate diffusivity calibration: Most unstable mode  |
|      | 14 |  0.0225 | Alternate diffusivity calibration: Sum of modes        |
| | | |
| ETGM | 1  | 1       | E×B shear coefficient                                  |
|      | 2  | 1       | *k*<sub>y</sub>*ρ*<sub>s</sub> Scan: Lower bound       |
|      | 3  | 50      | *k*<sub>y</sub>*ρ*<sub>s</sub> Scan: Upper bound       |
|      | 4  | 0.5     | *k*<sub>x</sub> / *k*<sub>y</sub>                      |
|      | 5  |  -25    | xte: Lower bound                                       |
|      | 6  |  25     | xte: Upper bound                                       |
|      | 7  |  0.05   | Default diffusivity calibration: Most unstable mode    |
|      | 8  |  0.0025 | Default diffusivity calibration: Sum of modes          |
|      | 9  |  1      | Alternate diffusivity calibration: Most unstable mode  |
|      | 10 | 0.05    | Alternate diffusivity calibration: Sum of modes        |
| | | |
| MTM  | 1  | 0.2     | *k*<sub>y</sub> / *k*<sub>x</sub>                      |
|      | 2  | 0.1     | Sum calibration factor                                 |
|      | 3  | 0.005   | *k*<sub>y</sub> Scan: Lower bound                      |
|      | 4  | 10      | kyrhos Scan: Upper bound                               |
|      | 5  |  -25    | xte: Lower bound                                       |
|      | 6  |  25     | xte: Upper bound                                       |
| | | |
| ETG  | 1  |  0.06   | scaling factor for electrostatic limit                 |
|      | 2  |  0.06   | scaling factor for electromagnetic limit               |
|      | 3  |  0      | xte: Lower bound                                       |
|      | 4  |  100    | xte: Upper bound                                       |

#### Model Options (Integer)

Integer options are specified by `iswitch(K<MODEL>, <INDEX>)`. 

> Beta Note: Some of these options will be removed in the final release of MMM v9.

| Model | Index | Default | Description                                        |
| ---- | ------ | ------- | -------------------------------------------------- |
| W20  | 1  |  0     | [_Effective shear definition_] 0: Use elong, 1: Use gxi                        |
| | | |
| DBM  | 1  | 1      | [_Frequency search direction_] 0: All, 1: Electron, -1: Ion                    |
|      | 2  | 20     | Number of *k*<sub>y</sub> modes (20 minimum), 0: Disable *k*<sub>y</sub> scan  |
|      | 3  | 0      | [_Diffusivity Type_] 0: Default, 1: Alternate                                  |
|      | 4  | 2      | Saturation ratio exponent (0 minimum)                                          |
|      | 5  | 0      | [_Mode handling_] 0: Most unstable mode, 1: Sum modes                          |
|      | 6  | 1      | [_kyrhos increments_] 0: Linear, 1: Exponential                                |
| | | |
| ETGM | 1  | 0      | [_Frequency search direction_] 0: All, 1: Electron, -1: Ion                    |
|      | 2  | 50     | Number of *k*<sub>y</sub> modes (50 minimum), 0: Disable *k*<sub>y</sub> scan  |
|      | 3  | 0      | [_Diffusivity Type_] 0: Default, 1: Alternate                                  |
|      | 4  | 2      | Saturation ratio exponent (0 minimum)                                          |
|      | 5  | 0      | [_Mode handling_] 0: Most unstable mode, 1: Sum modes                          |
|      | 6  | 1      | [_kyrhos increments_] 0: Linear, 1: Exponential                                |
| | | |
| MTM  | 1  | 100    | Number of *k*<sub>y</sub> modes (100 minimum), 0: Disable *k*<sub>y</sub> scan |
|      | 2  | 1      | [_xte capping scheme_] 0: Disabled, 1: Enabled                                 |
|      | 3  | 1      | [_kyrhos increments_] 0: Linear, 1: Exponential                                |
| | | |
| ETG  | 1  |  2     | [_Jenko Threshold_] <br> 0: Disabled (use original Horton model) <br> 1: Applied to electrostatic <br> 2: Applied to electromagnetic + electrostatic |

## Input Variables

### Input Arrays
The majority of input variables for MMM are plasma profiles.  All of the one-dimensional arrays listed below are defined on flux surfaces call radial points or zone boundaries, where the transport is to be computed.  The number of radial points is given by the input argument `npoints`.  Since MMM has been written as a local code, the size `npoints = 1` is allowed. 

The following table lists all array input variables, where variable numbers marked with an asterisk (*) are optional inputs:


| # |    Name | Symbol |   Description | Units |
|---------|----------|-------------|-------------|-------------|
| 1 |  z_rmin | *r* | Half-width of the magnetic surface | m |
| 2 |  z_rmaj  | *R* | Major radius to geometric center of the magnetic surface | m |
| 3 |  z_elong  | *κ* | Local elongation | - |
| 4 |  z_ne     | *n*<sub>e | Thermal electron density | 1/m<sup>3</sup> |
| 5 |  z_nh     | *n*<sub>h | Hydrogenic ion density | 1/m<sup>3</sup> |
| 6 |  z_nz     | *n*<sub>z | Impurity ion density | 1/m<sup>3</sup> |
| 7 |  z_nf     | *n*<sub>f | Fast ion density (non-thermal) | 1/m<sup>3</sup> |
| 8 |  z_te     | *T*<sub>e | Thermal electron temperature | keV |
| 9 |  z_ti     | *T*<sub>i |Thermal ion temperature | keV |
| 10 | z_q     | *q* | Safety factor | - |
| 11 | z_btor  | *B*<sub>φ | Toroidal magnetic field | Tesla |
| 12 | z_bu    | *B*<sub>u | Effective magnetic field | Tesla |
| 13 | z_dbp    | *B*<sub>θ</sub>′ | 1<sup>st</sup> derivative of poloidal magnetic field w.r.t. normalized rho | Tesla |
| 14 | z_d2bp    | *B*<sub>θ</sub>′′ | 2<sup>nd</sup> derivative of poloidal magnetic field w.r.t. normalized rho | Tesla |
| 15 | z_zeff    | *Z*<sub>eff | Effective charge | - |
| 16 | z_zz  | *Z*<sub>z| Mean charge of impurities | - |
| 17 | z_az  | *M*<sub>z |Mean atomic mass of impurities | a.m.u. |
| 18 | z_ah  | *M*<sub>h|Mean atomic mass of hydrogenic ions | a.m.u. |
| 19 | z_ai  | *M*<sub>i |Mean atomic mass of thermal ions | a.m.u. |
| 20 | z_wexb | *ω*<sub>E×B | *E*×*B* shearing rate | 1/s |
| 21 | z_gne | *g*<sub>n<sub>e | Normalized electron density gradient | - |
| 22 | z_gni | *g*<sub>n<sub>i | Normalized ion density gradient | - |
| 23 | z_gnh | *g*<sub>n<sub>h | Normalized hydrogenic ion density gradient | - |
| 24 | z_gnz | *g*<sub>n<sub>z | Normalized impurity density gradient | - |
| 25 | z_gte | *g*<sub>T<sub>e | Normalized electron temperature gradient | - |
| 26 | z_gti | *g*<sub>T<sub>i | Normalized ion temperature gradient | - |
| 27 | z_gq | *g*<sub>q | Normalized safety factor gradient | - |
| 28 | z_gbu | *g*<sub>B<sub>u | Normalized effective magnetic field gradient | - |
| 29 | z_vtor | *v*<sub>φ | Toroidal velocity | m/s |
| 30|  z_vpol | *v*<sub>θ | Poloidal velocity | m/s |
| 31|  z_vpar | *v*<sub>∥ | Parallel velocity | m/s |
| 32 | z_gvtor | *g*<sub>v<sub>φ | Normalized toroidal velocity gradient  | - |
| 33 | z_gvpol | *g*<sub>v<sub>θ | Normalized poloidal velocity gradient  | - |
| 34 | z_gvpar | *g*<sub>v<sub>∥ | Normalized parallel velocity gradient  | - |
| 35 | z_gxi | ⟨∇*ρ*⟩ |  Flux surface volume average (normalized *ρ*)  | 1/m |
| 36*| z_gelong | *κ*′ | 1<sup>st</sup> derivative of elongation w.r.t. inverse aspect ratio  | - |
| 37*| z_etanc | *η*<sub><sub>NC |Neoclassical resistivity  | Ωm |

> Note: The Horton ETG model will not run if `z_gelong` is not provided.
### Input Scalars
Additionally, there are two single-point (non-array) input variables:
| # |    Name | Symbol |   Description | Units |
|---------|----------|-------------|-------------|-------------|
| 38 | z_rmaj0 | *R*<sub>0</sub> | Major radius of plasma on axis | m |
| 39 | z_amin | *a* |Minor radius of plasma at edge | m |

## Output Variables

### Output Channels
MMM calculates transport for six channels, defined by `MMM_NCH` (see [Subroutine Dimensions](#subroutine-dimensions)):
| Channel | Description | 
|---|-------------|
| 1 | Ion Thermal   |
| 2 | Electron Particle |
| 3 | Electron Thermal  |
| 4 | Impurity Ion Particle |
| 5 | Toroidal Momentum |
| 6 | Poloidal Momentum |

### Total Diffusivity
Total diffusivities are returned as individual arrays of dimension `(npoints)`, where each variable corresponds to an output channel:
| Name | Symbol | Units | Description
|--------|--------|---------|---------|
| xti    | *χ*<sub>i | m<sup>2</sup>/s  | Ion thermal diffusivity
| xde    | *D*<sub>e | m<sup>2</sup>/s  | Electron particle diffusivity
| xte    | *χ*<sub>e | m<sup>2</sup>/s  | Electron thermal diffusivity
| xdz    | *D*<sub>z | m<sup>2</sup>/s  | Impurity ion particle diffusivity
| xvt    | *χ*<sub>φ | m<sup>2</sup>/s  | Toroidal momentum diffusivity
| xvp    | *χ*<sub>θ | m<sup>2</sup>/s  | Poloidal momentum diffusivity

### Total Flux
The total flux corresponding to each total diffusivity is an optional output variable, and is returned as the array `vflux`, with dimensions `(MMM_NCH, npoints)` :
| Name | Channel | Units | Description
|--------|--------|---------|---------|
| vflux | 1 | keVm/s | Ion thermal flux
|       | 2 | 1/m<sup>2</sup> | Electron particle flux
|       | 3 | keVm/s  | Electron thermal flux
|       | 4 | 1/m<sup>2</sup>  | Impurity ion particle flux
|       | 5 | m<sup>2</sup>/s<sup>2</sup>  | Toroidal momentum flux
|       | 6 | m<sup>2</sup>/s<sup>2</sup>  | Poloidal momentum flux

### Convective Velocity
Convective velocities and momentum pinches are returned as the array `vconv`, with dimensions `(MMM_NCH, npoints)` :
| Name | Channel | Units | Description
|--------|--------|---------|--------|
| vconv | 1 | m/s | Ion thermal convective velocity
|       | 2 | m/s | Electron particle convective velocity
|       | 3 | m/s | Electron thermal convective velocity
|       | 4 | m/s | Impurity ion particle convective velocity
|       | 5 | m/s | Toroidal momentum pinch
|       | 6 | m/s | Poloidal momentum pinch

> Note: Convective velocity is not calculated for the first three output channels, so these values will always be zero.

### Error Tracking
Errors are counted using the variable `nerr`, which can be used to help verify output results.  Such errors denote serious issues with the eigenvalue solver and are not expected.  Please contact [Tariq Rafiq](mailto:tar207@lehigh.edu) immediately if any errors are encountered.

## Output Variables (Components)
Each model in MMM returns its individual diffusivities, real frequencies, and growth rates (when applicable) as optional output variables.  In addition, models may also return additional output variables that can be viewed for diagnostic purposes.  All output variables listed in this section have dimension `(npoints)`, unless otherwise specified.

> Beta Note: Some of these output variables will be removed in the final release of MMM v9.

### W20 Outputs
The Weiland model outputs individual diffusivities corresponding to the first three output channels (see [Output Channels](#output-channels)).  However, since no other models contribute to the diffusivities of the last three output channels, these total diffusivities are also the component diffusivities of the Weiland model.

The growth rate and real frequency of the Weiland model has dimension `(2, npoints)`, as the complex frequency is calculated and returned for both the most unstable ion mode and the most unstable electron mode.

| Name | Units | Description
|--------|--------|---------|
| xtiW20  | m<sup>2</sup>/s  | Ion thermal diffusivity
| xdeW20  | m<sup>2</sup>/s  | Electron particle diffusivity
| xteW20  | m<sup>2</sup>/s  | Electron thermal diffusivity
| gammaW20(1, :) | 1/s  | Growth rate of the most unstable ion mode
| gammaW20(2, :) | 1/s  | Growth rate of the most unstable electron mode
| omegaW20(1, :) | 1/s  | Real frequency of the most unstable ion mode
| omegaW20(2, :) | 1/s  | Real frequency of the most unstable electron mode

> Note: The most unstable ion mode is always found in the ion search direction.  However, the most unstable electron mode can be found in either the ion or electron search direction, depending on which direction results in more transport.

### DBM Outputs

| Name | Units | Description
|--------|--------|---------|
| xtiDBM  | m<sup>2</sup>/s  | Ion thermal diffusivity (Default)
| xti2DBM  | m<sup>2</sup>/s  | Ion thermal diffusivity (Alternate)
| xdeDBM  | m<sup>2</sup>/s  | Electron particle diffusivity (Default)
| xde2DBM  | m<sup>2</sup>/s  | Electron particle diffusivity (Alternate)
| xteDBM  | m<sup>2</sup>/s  | Electron thermal diffusivity (Default)
| xte2DBM  | m<sup>2</sup>/s  | Electron thermal diffusivity (Alternate)
| gammaDBM | 1/s  | Growth rate of the most unstable mode
| omegaDBM | 1/s  | Real frequency of the most unstable mode
| kyrhosDBM | - | *k*<sub>y</sub>*ρ*<sub>s</sub> of the most unstable mode |
| satDBM | - | Saturation ratio of the most unstable mode |
| phi2DBM | - | \|*ϕ*\|<sup>2</sup> of the most unstable mode (normalized scalar potential) |
| Apara2DBM | - | \|*A*<sub>∥</sub>\|<sup>2</sup> of the most unstable mode (normalized vector potential) |
| gaveDBM | - | Average magnetic curvature |

> Note: The most unstable mode will depend on the frequency search direction.

### ETGM Outputs

| Name | Units | Description
|--------|--------|---------|
| xteETGM  | m<sup>2</sup>/s  | Electron thermal diffusivity (Default) |
| xte2ETGM  | m<sup>2</sup>/s  | Electron thermal diffusivity (Alternate) |
| gammaETGM | 1/s  | Growth rate of the most unstable mode |
| omegaETGM | 1/s  | Real frequency of the most unstable mode |
| wdeETGM | 1/s | Magnetic drift frequency of the most unstable mode |
| kyrhosETGM | - | *k*<sub>y</sub>*ρ*<sub>s</sub> of the most unstable mode |
| satETGM | - | Saturation ratio of the most unstable mode |
| phi2ETGM | - | \|*ϕ*\|<sup>2</sup> of the most unstable mode (normalized scalar potential) |
| Apara2ETGM | - | \|*A*<sub>∥</sub>\|<sup>2</sup> of the most unstable mode (normalized vector potential) |
| gaveETGM | - | Average magnetic curvature |
| alphaETGM | - | *α*<sub>MHD</sub> |
| kpara2ETGM | 1/m<sup>2</sup> | ⟨*k*<sub>∥</sub><sup>2</sup>⟩ of the most unstable mode |
| fleETGM | - | ⟨*k*<sub>∥</sub><sup>2</sup>*ρ*<sub>e</sub><sup>2</sup>⟩ of the most unstable mode |

> Note: The most unstable mode will depend on the frequency search direction.


### MTM Outputs

| Name | Units | Description
|--------|--------|---------|
| xteMTM  | m<sup>2</sup>/s  | Electron thermal diffusivity |
| gammaMTM | 1/s  | Growth rate of the most unstable mode |
| omegaMTM | 1/s  | Real frequency of the most unstable mode |
| kyrhosMTM | - | *k*<sub>y</sub>*ρ*<sub>s</sub> of the most unstable mode |
| dbsqMTM | - | \|𝛿*B*/*B*\|<sup>2</sup> profile |

### ETG Outputs

| Name | Units | Description
|--------|--------|---------|
| xteETG  | m<sup>2</sup>/s  | Electron thermal diffusivity |
| gtecETG | -  | Critical normalized electron temperature gradient |


## MMM Subroutine
The MMM subroutine can be called without any initialization beforehand.  However, [Model Options](#model-options) may be initialized and edited via the `set_mmm_switches` subroutine call if needed.  Otherwise, MMM will use default model options if the arrays `rswitch` and `iswitch` aren't specified.

It is strongly recommended to call MMM using keyword arguments, as the number and order of the arguments is subject to change in future updates.  A complete MMM subroutine call with all optional arguments included can be made as follows:
```fortran
CALL mmm( &
    npoints=npoints,                                                 &  ! Input Points
    rswitch=rswitch,       iswitch=iswitch,     cmodel=cmodel,       &  ! Input Switches
    z_amin=amin,           z_rmaj0=rmaj0,                            &  ! Input Variables (Points)
    z_rmin=rmin,           z_rmaj=rmaj,         z_ne=ne,             &  ! Input Variables (Arrays)
    z_nh=nh,               z_nz=nz,             z_nf=nf,             &
    z_zeff=zeff,           z_te=te,             z_ti=ti,             &
    z_q=q,                 z_btor=btor,         z_bu=bu,             &
    z_gbu=gbu,             z_elong=elong,       z_zz=zz,             &
    z_az=az,               z_ah=ah,             z_ai=ai,             &
    z_wexb=wexb,           z_gne=gne,           z_gni=gni,           &
    z_gnh=gnh,             z_gnz=gnz,           z_gte=gte,           &
    z_gti=gti,             z_gq=gq,             z_gvtor=gvtor,       &
    z_vtor=vtor,           z_gvpar=gvpar,       z_vpol=vpol,         &
    z_gvpol=gvpol,         z_vpar=vpar,         z_gelong=gelong,     &
    z_dbp=dbp,             z_d2bp=d2bp,                              &        
    z_gxi=gxi,             z_etanc=etanc,                            &
    xti=xti,               xde=xde,             xte=xte,             &  ! Output Variables
    xdz=xdz,               xvt=xvt,             xvp=xvp,             &
    vconv=vconv,           vflux=vflux,                              &
    xtiW20=xtiW20,         xdeW20=xdeW20,       xteW20=xteW20,       &  ! Output Variables (Components)
    xtiDBM=xtiDBM,         xdeDBM=xdeDBM,       xteDBM=xteDBM,       &
    xti2DBM=xti2DBM,       xde2DBM=xde2DBM,     xte2DBM=xte2DBM,     &
    xteETG=xteETG,         xteMTM=xteMTM,       xteETGM=xteETGM,     &   
    kyrhosMTM=kyrhosMTM,   gammaW20=gammaW20,   omegaW20=omegaW20,   &  
    gammaDBM=gammaDBM,     omegaDBM=omegaDBM,   gammaMTM=gammaMTM,   &
    omegaMTM=omegaMTM,     gammaETGM=gammaETGM, omegaETGM=omegaETGM, &                              
    dbsqMTM=dbsqMTM,       xte2ETGM=xte2ETGM,   wdeETGM=wdeETGM,     &
    kyrhosDBM=kyrhosDBM,   satDBM=satDBM,       phi2DBM=phi2DBM,     &
    kyrhosETGM=kyrhosETGM, gaveDBM=gaveDBM,     gaveETGM=gaveETGM,   &
    kpara2ETGM=kpara2ETGM, fleETGM=fleETGM,     gtecETG=gtecETG,     &
    alphaETGM=alphaETGM,   satETGM=satETGM,     phi2ETGM=phi2ETGM,   &
    Apara2ETGM=Apara2ETGM, Apara2DBM=Apara2DBM,                      &
    nerr=nerr                                                        &  ! Output Errors
)
```

Alternatively, only the following is needed to call the MMM subroutine using minimal arguments:
```fortran
CALL mmm( &
    npoints=npoints,                                                 &  ! Input Points
    z_amin=amin,           z_rmaj0=rmaj0,                            &  ! Input Variables (Points)
    z_rmin=rmin,           z_rmaj=rmaj,         z_ne=ne,             &  ! Input Variables (Arrays)
    z_nh=nh,               z_nz=nz,             z_nf=nf,             &
    z_zeff=zeff,           z_te=te,             z_ti=ti,             &
    z_q=q,                 z_btor=btor,         z_bu=bu,             &
    z_gbu=gbu,             z_elong=elong,       z_zz=zz,             &
    z_az=az,               z_ah=ah,             z_ai=ai,             &
    z_wexb=wexb,           z_gne=gne,           z_gni=gni,           &
    z_gnh=gnh,             z_gnz=gnz,           z_gte=gte,           &
    z_gti=gti,             z_gq=gq,             z_gvtor=gvtor,       &
    z_vtor=vtor,           z_gvpar=gvpar,       z_vpol=vpol,         &
    z_gvpol=gvpol,         z_vpar=vpar,         z_gelong=gelong,     &
    z_dbp=dbp,             z_d2bp=d2bp,                              &        
    z_gxi=gxi,             z_etanc=etanc,                            &
    xti=xti,               xde=xde,             xte=xte,             &  ! Output Variables
    xdz=xdz,               xvt=xvt,             xvp=xvp,             &
    vconv=vconv,                                                     &
    nerr=nerr                                                        &  ! Output Errors
)
```
For further reference on the `set_mmm_switches` and `mmm` subroutine calls, please see the included file `wrapper/mmm_wrapper.f90`.

## References
> TODO: Update Needed

---

[Return](../README.md)
