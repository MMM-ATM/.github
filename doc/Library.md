<h1 align="center">MMM Library<a name="top"></a></h1>
This documentation covers specifics of the MMM module and library, including public definitions, input options, and input and output variables.  The mmm_wrapper.f90 file in the wrapper directory serves as an excellent reference point for everything mentioned here.

## 📚 Contents
- [Public Definitions](#public-definitions)
- [Input Options](#input-options)
- [Input Variables](#input-variables)
- [Output Variables](#output-variables)
- [Output Variables (Components)](#output-variables-components)
- [MMM Subroutine](#mmm-subroutine)

## Public Definitions
The MMM module `mmm_main` makes the following public definitions available via the compiled library file `libmmm.a`.  The  statement `Use mmm_main` is required to access the public definitions in this section.  The current version of MMM can be referenced using the public character `MMM_VERSION`.

### Public Subroutines
 Two public subroutines are specified by the MMM module:
```fortran
Public :: mmm               ! Main subroutine
Public :: set_mmm_switches  ! Specify real and integer switch options (Optional)
```
Each of these subroutine calls will be explained in greater detail below (see [MMM Subroutine](#mmm-subroutine)).    Note that the main subroutine `mmm` can be called without calling `set_mmm_switches` beforehand, which will run MMM using default options (see [Model Options](#model-options)).

### Module Dimensions
Public parameters are used to specify the size of various arrays used by MMM:
```fortran
Integer, Parameter, Public :: &
   MMM_NCH   = 6, &  ! Number of transport channels
   MMM_NFREQ = 2, &  ! Number of frequency channels
   MMM_NMODE = 5, &  ! Number of internal models + 1
   MMM_NOPT  = 20    ! Maximum number of internal switch options per model
```
These parameters should be referenced by external codes using MMM for array initialization.

### Transport Channel Identifiers
```fortran
Integer, Parameter, Public :: &
   MMM_KTI = 1, &  ! Thermal Ion
   MMM_KTE = 2, &  ! Thermal Electron
   MMM_KNE = 3, &  ! Particle Electron
   MMM_KNZ = 4, &  ! Particle Impurity
   MMM_KVT = 5, &  ! Toroidal Velocity
   MMM_KVP = 6     ! Poloidal Velocity
```

These parameters should be referenced by external codes when reading MMM output variables.

### Frequency Channel Identifiers
```fortran
Integer, Parameter, Public :: &
   MMM_KION = 1, &  ! Ion Direction
   MMM_KELC = 2     ! Electron Direction
```

These parameters should be referenced by external codes when reading W20 output frequencies.

### Model Identifiers
Public parameters are also used to reference each model `K<MODEL>` within MMM:
```fortran
Integer, Parameter, Public :: &
   MMM_KW20  = 1, &  ! Weiland (W20)
   MMM_KDBM  = 2, &  ! Drift-Resistive-Inertial Ballooning Modes (DBM)
   MMM_KETGM = 3, &  ! Electron Temperature Gradient Modes (ETGM)
   MMM_KMTM  = 4, &  ! Microtearing Modes (MTM)
   MMM_KMMM  = 5, &  ! All Models (MMM)
```
These model identifiers should be referenced by external codes using MMM when specifying internal switch values, as model integer values are subject to change in future updates.

## Input Options

### Model Weights

Model weights are specified by the optional input array `cmodel` of dimension `(MMM_NMODE)` (see [Module Dimensions](#module-dimensions)), where array indices should be referenced using the [Model Identifiers](#model-identifiers).  Setting the weight of any model to 0 will disable that model.  All models are enabled by default.

### Model Options
Real and Integer options are passed in via the optional input arrays `rswitch` and `iswitch`, respectively, with dimensions `(MMM_NOPT,MMM_NMODE)`  (see [Module Dimensions](#module-dimensions)).  These arrays can be initialized with default values by making the following subroutine call:
```fortran
CALL set_mmm_switches(rswitch=rswitch, iswitch=iswitch) 
```
Afterwards, values can be modified as needed before passing these arrays into MMM via the main subroutine call.  The first dimension here corresponds to the model identifier `K<MODEL>` (see [Model Identifiers](#model-identifiers)),  and the second dimension corresponds to the option index `<INDEX>`.  For further reference on this subroutine call and how to edit the values of model options, please see the included files `wrapper/mmm_wrapper.f90` and `wrapper/input.dat`.

The following sections provide tables of real and integer switch options, along with their default values, for each of the models in MMM:
-  [Model Options (Real)](#model-options-real)
-  [Model Options (Integer)](#model-options-integer)

#### Model Options (Real)
Real options are specified by `rswitch(K<MODEL>, <INDEX>)`.

| Model | Index | Default | Description                              |
| ---- | ------ | ------- | ---------------------------------------- |
| | | |
| MMM  | 1  | 100     | max \|xti\|  (Diffusivities)                 |
|      | 2  | 100     | max \|xte\|                                  |
|      | 3  | 100     | max \|xne\|                                  |
|      | 4  | 100     | max \|xnz\|                                  |
|      | 5  | 100     | max \|xvt\|                                  |
|      | 6  | 100     | max \|xvp\|                                  |
|      | 7  | 5D4     | max \|vti\|  (Convective Velocities)         |
|      | 8  | 5D4     | max \|vte\|                                  |
|      | 9  | 5D4     | max \|vne\|                                  |
|      | 10 | 5D4     | max \|vnz\|                                  |
|      | 11 | 5D4     | max \|vvt\|                                  |
|      | 12 | 5D4     | max \|vvp\|                                  |
| | | |
| W20  | 1  | 1       | E×B shear coefficient                             |
|      | 2  | 0.5     | Max diffusivity coefficient                       |
|      | 3  | 1       | Max convective velocity coefficient               |
|      | 4  | 0.316   | *k*<sub>y</sub>*ρ*<sub>s</sub>                    |
| | | |
| DBM  | 1  | 1       | E×B shear coefficient                             |
|      | 2  | 0.5     | Max diffusivity coefficient                       |
|      | 3  | 0.05    | *k*<sub>y</sub>*ρ*<sub>s</sub> Scan: Lower bound  |
|      | 4  | 0.30    | *k*<sub>y</sub>*ρ*<sub>s</sub> Scan: Upper bound  |
| | | |
| ETGM | 1  | 1       | E×B shear coefficient                             |
|      | 2  | 0.25    | Max diffusivity coefficient                       |
|      | 3  | 1       | *k*<sub>y</sub>*ρ*<sub>s</sub> Scan: Lower bound  |
|      | 4  | 50      | *k*<sub>y</sub>*ρ*<sub>s</sub> Scan: Upper bound  |
| | | |
| MTM  | 1  | 0.25    | Max diffusivity coefficient                       |
|      | 2  | 0.005   | *k*<sub>y</sub>*ρ*<sub>s</sub> Scan: Lower bound  |
|      | 3  | 10      | *k*<sub>y</sub>*ρ*<sub>s</sub> Scan: Upper bound  |

> [!Note]
> - Max diffusivities are in units of m<sup>2</sup>/s, and max convective velocities are in units of m/s.
> - The max diffusivity of each individual model is determined by multiplying the MMM max diffusivity by the max diffusivity coefficient of that model.
> - Diffusivities and convective velocities should only be limited if the associated solver (e.g. PT Solver) struggles with large values.


#### Model Options (Integer)

Integer options are specified by `iswitch(K<MODEL>, <INDEX>)`. 

| Model | Index | Default | Description                                        |
| ---- | ------ | ------- | -------------------------------------------------- |
| | | |
| MMM  | 1  |  1     | Separate diffusivity and convective velocity  (1: True, 0: False)  |
|      | 2  |  1     | Convert negative diffusivity to convective velocity                |
|      | 3  |  1     | Allow negative diffusivity output                                  |
|      | 4  |  1     | Modify diffusivity using solver gradients                          |
|      | 5  |  1     | Limit large diffusivity due to small gradients                     |
| | | |
| W20  | 1  |  0     | [_Effective shear definition_] 0: Use elong, 1: Use gxi            |
| | | |
| DBM  | 1  | 0      | [_Model_] 0: Default, 1: Alternate                                           |
|      | 2  | 1      | [_Frequency search direction_] 0: Any, 1: Electron, -1: Ion                  |
|      | 3  | 0      | [_Mode handling_] 0: Most unstable mode, 1: Sum modes                        |
|      | 4  | 7      | Number of *k*<sub>y</sub> modes (7 minimum), 0: Disable *k*<sub>y</sub> scan |
| | | |
| ETGM | 1  | 0      | [_Model_] 0: Default, 1: Alternate, 2: Horton                     |
|      | 2  | 1      | [_Jenko threshold (Horton)_] 1: Electrostatic, 2: Electromagnetic |
|      | 3  | 0      | [_Frequency search direction_] 0: Any, 1: Electron, -1: Ion       |
|      | 4  | 0      | [_Mode handling_] 0: Most unstable mode, 1: Sum modes             |
|      | 5  | 7      | Number of *k*<sub>y</sub> modes (7 minimum), 0: Disable *k*<sub>y</sub> scan |
| | | |
| MTM  | 1  | 200    | Number of *k*<sub>y</sub> modes (200 minimum), 0: Disable *k*<sub>y</sub> scan |

> [!Note]
> - Separating diffusive and convective components currently only applies to W20 contributions.
> - Allowing negative diffusivity output has no effect when negative diffusivity is converted to convective velocity.
> - Negative diffusivities will only be modified in the total diffusivity output array `diff`.  The optional component diffusivity outputs are not modified.
> - Modifying diffusivity using solver gradients should not change flux output.  Convective velocity output may change if negative diffusivities are converted to convective velocities.
> - Limiting large diffusivity due to small gradients should only result in negligible changes to effective flux.
> - When summing modes in DBM or ETGM, the *k*<sub>y</sub> mode count should be increased to at least 50.
> - When disabling the *k*<sub>y</sub> scan in DBM, ETGM, or MTM, the lower *k*<sub>y</sub>*ρ*<sub>s</sub> bound is used as the single *k*<sub>y</sub>*ρ*<sub>s</sub> value.


## Input Variables

All input variables can be derived from TRANSP CDF output files.  See the [CDF Input](CDFInput.md) documentation for more details.
### Input Scalars
There are two single-point (non-array) input variables:

| # |    Name | Symbol |   Description | Units |
|---------|----------|-------------|-------------|-------------|
| 1 | z_rmaj0 | $R_0$ | Major radius of plasma on axis | m |
| 2 | z_amin  | $a$ |Minor radius of plasma at edge | m |

### Input Arrays
The majority of input variables for MMM are plasma profiles.  All of the one-dimensional arrays listed below are defined on flux surfaces call radial points or zone boundaries, where the transport is to be computed.  The number of radial points is given by the input argument `npoints`.  Since MMM has been written as a local code, the value `npoints = 1` is allowed. 

| # |    Name | Symbol |   Description | Units |
|---------|----------|-------------|-------------|-------------|
| 3  | z_rmin | $r$ | Half-width of the magnetic surface | m |
| 4  | z_rmaj  | $R$ | Major radius to geometric center of the magnetic surface | m |
| 5  | z_elong  | $\kappa$ | Local elongation | - |
| 6  | z_ne     | $n_{\rm e}$ | Thermal electron density | m<sup>-3</sup> |
| 7  | z_nh     | $n_{\rm h}$ | Thermal hydrogenic ion density | m<sup>-3</sup> |
| 8  | z_nz     | $n_{\rm z}$ | Thermal impurity ion density | m<sup>-3</sup> |
| 9  | z_nf     | $n_{\rm f}$ | Fast ion density (non-thermal) | m<sup>-3</sup> |
| 10 | z_te     | $T_{\rm e}$ | Thermal electron temperature | keV |
| 11 | z_ti     | $T_{\rm i}$ | Thermal ion temperature | keV |
| 12 | z_q     | $q$ | Safety factor | - |
| 13 | z_bu    | $B_{\rm u}$ | Effective magnetic field | T |
| 14 | z_btor  | $B_{\phi}$ | Toroidal magnetic field | T |
| 15 | z_bpol   | $B_{\theta}$ | Poloidal magnetic field | T |
| 16 | z_dbp    | $dB_{\theta} / dr$| 1<sup>st</sup> derivative of poloidal magnetic field | T / m |
| 17 | z_d2bp   | $d^2B_{\theta}/ dr^2$ | 2<sup>nd</sup> derivative of poloidal magnetic field | T / m<sup>2</sup> |
| 18 | z_zeff   | $Z_{\rm eff}$ | Effective charge | - |
| 19 | z_zz  | $Z_{\rm z}$ | Mean charge of impurities | - |
| 20 | z_az  | $M_{\rm z}$ | Mean atomic mass of impurities | u |
| 21 | z_ah  | $M_{\rm h}$ | Mean atomic mass of hydrogenic ions | u |
| 22 | z_wexb | $\omega_{\rm E\times B}$ | *E*×*B* shearing rate | s<sup>-1</sup> |
| 23 | z_gne | $g_{\rm ne}$ | Normalized electron density gradient | - |
| 24 | z_gnh | $g_{\rm nh}$ | Normalized hydrogenic ion density gradient | - |
| 25 | z_gnz | $g_{\rm nz}$ | Normalized impurity density gradient | - |
| 26 | z_gte | $g_{\rm Te}$ | Normalized electron temperature gradient | - |
| 27 | z_gti | $g_{\rm Ti}$ | Normalized ion temperature gradient | - |
| 28 | z_gq | $g_{\rm q}$ | Normalized safety factor gradient | - |
| 29 | z_gbu | $g_{\rm Bu}$ | Normalized effective magnetic field gradient | - |
| 30 | z_vtor | $v_{\phi}$ | Toroidal velocity | m/s |
| 31 | z_vpol | $v_{\theta}$ | Poloidal velocity | m/s |
| 32 | z_vpar | $v_{\parallel}$ | Parallel velocity | m/s |
| 33 | z_gvtor | $g_{\rm v\phi}$ | Normalized toroidal velocity gradient  | - |
| 34 | z_gvpol | $g_{\rm v\theta}$ | Normalized poloidal velocity gradient  | - |
| 35 | z_gvpar | $g_{\rm v\parallel}$ | Normalized parallel velocity gradient  | - |
| 36 | z_gxi | $\nabla\hat{\rho}$ |  Zone boundary gradient  | m<sup>-1</sup> |

### Optional Input Arrays
Input variables that are not required to run MMM.

| # |    Name | Symbol |   Description | Units |
|---------|----------|-------------|-------------|-------------|
| 37 | z_etanc |  $\eta_{\rm nc}$ | Neoclassical resistivity  | Ωm |
| 38 | z_gelong | $\kappa^\prime$ | 1<sup>st</sup> derivative of elongation w.r.t. inverse aspect ratio  | - |
| 39 | z_gti_solver |  $g_{\rm Te,\ solver}$ | Normalized electron temperature gradient  | - |
| 40 | z_gte_solver |  $g_{\rm Ti,\ solver}$ | Normalized ion temperature gradient  | - |
| 41 | z_gne_solver |  $g_{\rm ne,\ solver}$ | Normalized electron density gradient  | - |
| 42 | z_gnz_solver |  $g_{\rm nz,\ solver}$ | Normalized impurity temperature gradient  | - |
| 43 | z_gvt_solver |  $g_{\rm v\phi,\ solver}$ | Normalized toroidal velocity gradient  | - |
| 44 | z_gvp_solver |  $g_{\rm v\theta,\ solver}$ | Normalized poloidal velocity gradient  | - |

> [!NOTE]
> - z_gelong is required to run ETGM when the Horton model is selected.
> - z_etanc is only used in DBM, but is not required to run this model.
> - Solver gradients are used to convert MMM diffusivity to solver diffusivity to keep flux consistent between MMM and the associated solver.


## Output Variables

### Output Channels
MMM calculates transport for six channels, defined by `MMM_NCH` (see [Module Dimensions](#module-dimensions)):

| Channel | Description | Identifier
|---|-------------|-------------|
| 1 | Ion Thermal   | `MMM_KTI` |
| 2 | Electron Thermal | `MMM_KTE` |
| 3 | Electron Particle  | `MMM_KNE` |
| 4 | Impurity Particle | `MMM_KNZ` |
| 5 | Toroidal Momentum | `MMM_KVT` |
| 6 | Poloidal Momentum | `MMM_KVP` |

### Total Diffusivity
Diffusivities are returned as the array `diff` with dimensions `(MMM_NCH, npoints)`:

| Name | Channel |Symbol | Units | Description
|--------|--------|--------|---------|---------|
| flux   | 1  | $\chi_{\rm i}$      | m<sup>2</sup>/s  | Ion thermal diffusivity |
|        | 2  | $\chi_{\rm e}$      | m<sup>2</sup>/s  | Electron thermal diffusivity |
|        | 3  | $\chi_{\rm n}$      | m<sup>2</sup>/s  | Electron particle diffusivity |
|        | 4  | $\chi_{\rm z}$      | m<sup>2</sup>/s  | Impurity particle diffusivity |
|        | 5  | $\chi_{\rm \phi}$   | m<sup>2</sup>/s  | Toroidal momentum diffusivity |
|        | 6  | $\chi_{\rm \theta}$ | m<sup>2</sup>/s  | Poloidal momentum diffusivity |


### Convective Velocity
Convective velocities are returned as the array `vconv` with dimensions `(MMM_NCH, npoints)`:

| Name | Channel | Symbol | Units | Description
|--------|--------|---------|--------|--------|
| vconv | 1 | $V_{\rm i}$      | m/s | Ion thermal convective velocity |
|       | 2 | $V_{\rm e}$      | m/s | Electron thermal convective velocity |
|       | 3 | $V_{\rm n}$      | m/s | Electron particle convective velocity |
|       | 4 | $V_{\rm z}$      | m/s | Impurity particle convective velocity |
|       | 5 | $V_{\rm \phi}$   | m/s | Toroidal momentum pinch |
|       | 6 | $V_{\rm \theta}$ | m/s | Poloidal momentum pinch |

### Effective Flux
Effective flux includes both diffusive and convective contributions.  It is returned as the array `flux` with dimensions `(MMM_NCH, npoints)`:

| Name | Channel | Symbol | Units | Description
|--------|--------|---------|---------|--------|
| flux  | 1 | $\Gamma_{\rm i}$      | keV m/s | Ion thermal flux |
|       | 2 | $\Gamma_{\rm e}$      | keV m/s | Electron thermal flux |
|       | 3 | $\Gamma_{\rm n}$      | m<sup>-2</sup> | Electron particle flux |
|       | 4 | $\Gamma_{\rm z}$      | m<sup>-2</sup> | Impurity ion particle flux |
|       | 5 | $\Gamma_{\rm \phi}$   | m<sup>2</sup>/s<sup>2</sup>  | Toroidal momentum flux |
|       | 6 | $\Gamma_{\rm \theta}$ | m<sup>2</sup>/s<sup>2</sup>  | Poloidal momentum flux |

### Error Tracking
Errors are counted using the variable `nerr`, which can be used to help verify output results.  Such errors denote serious issues with the eigenvalue solver and are not expected.  Please contact [Tariq Rafiq](mailto:tar207@lehigh.edu) immediately if any errors are encountered.


## Output Variables (Components)
Each model in MMM returns its individual diffusivities, real frequencies, and growth rates (when applicable) as optional output variables.  In addition, models may also return additional output variables that can be viewed for diagnostic purposes.  All output variables listed in this section have dimension `(npoints)`, unless otherwise specified.

### W20 Outputs
The Weiland model outputs individual diffusivities corresponding to the first three output channels (see [Output Channels](#output-channels)).  However, since no other models contribute to the diffusivities of the last three output channels, these total diffusivities are also the component diffusivities of the Weiland model.

The growth rate and real frequency of the Weiland model has dimension `(MMM_NFREQ, npoints)`, as the complex frequency is calculated and returned for both the most unstable ion mode and the most unstable electron mode.

| Name | Units | Description
|--------|--------|---------|
| xtiW20  | m<sup>2</sup>/s  | Ion thermal diffusivity |
| xteW20  | m<sup>2</sup>/s  | Electron thermal diffusivity |
| xneW20  | m<sup>2</sup>/s  | Electron particle diffusivity |
| gammaW20(MMM_KION, :) | s<sup>-1</sup>  | Growth rate of the most unstable ion mode |
| gammaW20(MMM_KELC, :) | s<sup>-1</sup>  | Growth rate of the most unstable electron mode |
| omegaW20(MMM_KION, :) | s<sup>-1</sup>  | Real frequency of the most unstable ion mode |
| omegaW20(MMM_KELC, :) | s<sup>-1</sup>  | Real frequency of the most unstable electron mode |
| kyrhosW20(MMM_KION, :) | -    | $k_{\rm y}\rho_{\rm s}$ for ion modes |
| kyrhosW20(MMM_KELC, :) | -    | $k_{\rm y}\rho_{\rm s}$ for electron modes |

### DBM Outputs

| Name | Units | Description
|--------|--------|---------|
| xtiDBM  | m<sup>2</sup>/s  | Ion thermal diffusivity |
| xteDBM  | m<sup>2</sup>/s  | Electron thermal diffusivity |
| xneDBM  | m<sup>2</sup>/s  | Electron particle diffusivity |
| gammaDBM | s<sup>-1</sup>  | Growth rate of the most unstable mode |
| omegaDBM | s<sup>-1</sup>  | Real frequency of the most unstable mode |
| kyrhosDBM | - | $k_{\rm y}\rho_{\rm s}$ of the most unstable mode |

### ETGM Outputs

| Name | Units | Description
|--------|--------|---------|
| xteETGM  | m<sup>2</sup>/s  | Electron thermal diffusivity  |
| gammaETGM | s<sup>-1</sup>  | Growth rate of the most unstable mode |
| omegaETGM | s<sup>-1</sup>  | Real frequency of the most unstable mode |
| kyrhosETGM | - | $k_{\rm y}\rho_{\rm s}$ of the most unstable mode |

### MTM Outputs

| Name | Units | Description
|--------|--------|---------|
| xteMTM  | m<sup>2</sup>/s  | Electron thermal diffusivity |
| gammaMTM | s<sup>-1</sup>  | Growth rate of the most unstable mode |
| omegaMTM | s<sup>-1</sup>  | Real frequency of the most unstable mode |
| kyrhosMTM | - | $k_{\rm y}\rho_{\rm s}$ of the most unstable mode |
| dbsqMTM | - | $\|\delta B/B\|^2$ profile |

## MMM Subroutine
The MMM subroutine can be called without any initialization beforehand.  However, [Model Options](#model-options) may be initialized and edited via the `set_mmm_switches` subroutine call if needed.  Otherwise, MMM will use default model options if the arrays `rswitch` and `iswitch` aren't specified.

It is strongly recommended to call MMM using keyword arguments, as the number and order of the arguments is subject to change in future updates.  A complete MMM subroutine call with all optional arguments included can be seen in the [MMM wrapper](Wrapper.md).

---

###### [⬆️ Top](#top)&nbsp;&nbsp; [↩️ Return](../README.md)
