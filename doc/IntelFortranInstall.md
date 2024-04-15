<h1 align="center">Intel Fortran Installation<a name="top"></a></h1>

This guide was originally written in 2022 for the installation of the ifort compiler, but it should largely apply to installation of the new ifx compiler as well.  Users are encouraged to install and use the ifx compiler, as ifort has been depreciated as of 2024.

## Windows Installation
This installation should be completed in the following order to avoid potential errors with PATH variables not correctly being set.

1. Install Microsoft Visual Studio (Community Edition is free).

    - Link: https://visualstudio.microsoft.com/
    - When installing, select the "Desktop development with C++" workload.
     <p align="center"><picture><img src="img/ifort1.png?raw=true"></picture></p>

    Only the checked components from this workload are required:
    - MSVC C++ x64/x86 build tools
    - Windows SDK (corresponding to your Windows version)
     <p align="center"><picture><img src="img/ifort2.png?raw=true"></picture></p>

2. Install the Intel Fortran compiler (stand-alone version)

    - Link: https://www.intel.com/content/www/us/en/developer/tools/oneapi/fortran-compiler.htm
    - Name: Intel Fortran Compiler Classic and Intel Fortran Compiler for Windows
    - During installation, integrate the Fortran compiler with your Microsoft Visual Studio IDE

3. Reboot to update system paths.
   
   This should avoid potential errors in the following step.

4. Run Intel(r) oneAPI Tools
    
    This specialized command prompt environment needs to be run to access the linker installed with the MSVC component during the Visual Studio install.  On Windows 10 it should appear in the start menu, but it might not on Windows 11.
     <p align="center"><picture><img src="img/ifort3.png?raw=true"></picture></p>
     
     The message :: oneAPI environment initialized :: indicates that variables have been correctly set.  Otherwise, you may see some errors about missing PATH variables.  These need to be fixed using the `setx` command (likely followed by a reboot) before the ifort compiler can be used.
    
    In some cases, the shortcut to the Intel oneAPI command prompt may not appear in the Start Menu.  However, this environment can be created manually by opening a normal command prompt window (`cmd.exe`), and then running `setvars.bat`, which should be located in your `C:\Program Files (x86)\Intel\oneAPI` directory; this environment needs to be initialized each time you wish to use the ifort compiler.
    
 
    If needed, a shortcut to Intel oneAPI can be created by first creating a shortcut to the normal command prompt window, `cmd`:
     <p align="center"><picture><img src="img/ifort4.png?raw=true"></picture></p>

    Then,  modify the properties of the shortcut as follows:
       <p align="center"><picture><img src="img/ifort5.png?raw=true"></picture></p>
      - Target: `%ComSpec% /E:ON /K ""C:\Program Files (x86)\Intel\oneAPI\setvars.bat" intel64 vs2022"`
      - Start in: `"C:\Program Files (x86)\Intel\oneAPI"`


5. Set the `MMMFC` MMM Fortran Compiler environment variable as follows:
    ```bash
    setx MMMFC ifort
    ```
    This will not interfere with the same environment variable that may be set in Cygwin's Bash terminal (if installed, for gfortran compilation).


6. Run the `Makefile` from either the `test` or `wrapper` directory to compile with ifort.
    If the `MMMFC` environment variable was correctly set, then the compiler flags should begin with `/`, and not `-`:
    <p align="center"><picture><img src="img/ifort6.png?raw=true"></picture></p>

    
---

###### [⬆️ Top](#top)&nbsp;&nbsp; [↩️ Return](../README.md)
