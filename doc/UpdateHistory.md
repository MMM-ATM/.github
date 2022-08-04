# Update History

<table id="verticalalign">
   <thead>
      <tr>
         <th>Date</th>
         <th>Version</th>
         <th>Description</th>
      </tr>
   </thead>
   <tbody>
      <tr>
         <td valign="top">2022/07/13</td>
         <td valign="top">9.0.0 (beta)</td>
         <td valign="top">
            MMM Module Updates <br>
            - Complete rewrite of codebase <br>
            - Input variable values are now verified <br>
            - Potential division by zero errors removed <br>
            - All non-local code removed <br>
            - Flux calculations corrected for all output channels<br><br>
            Weiland Model Updates <br>
            - Complete rewrite of codebase <br>
            - Now runs over three times as fast <br>
            - Several corrections to Weiland model updates introduced in 8.1.0 <br>
            - Real frequency and growth rate channels reduced from 4 to 2 <br><br>
            ETGM Model Updates <br>
            - Includes dependence on geometry and kyrhos scan <br>
            - Removed collisionality switch; several new switches added <br><br>
            Horton ETG Updates <br>
            - No longer runs by default <br>
            - Fixed bug where Horton ETG was redefining thermal velocity<br><br>
            DRIBM Model Updates <br>
            - Introduction of geometry dependence and kyrhos scan <br>
            - New set of matrix equations<br><br>
            MTM Model Updates <br>
            - Runtime now negligible compared to other models <br>
            - Complex cubic solver runs 30% faster <br>
            - Removal of inertia switch <br>
            - Mean atomic mass of ions introduced <br><br>
            Input Argument Updates<br>
            - New input variables: bu, gbu, dbp, d2bp, gxi, amin <br>
            - Input variable array sizes now explicitly set to npoints <br>
            - Keyword support for option switches removed <br>
            - Many new model input switch options added <br>
            - Renamed option switches, input variables, and several public parameters <br><br>
            General Updates<br>
            - TestMMM input of 2nd kind removed <br>
            - MMM Wrapper and plotting script added <br>
            - License and terms of use updated
         </td>
      </tr>
      <tr>
         <td valign="top">2022/03/29</td>
         <td valign="top">8.2.3</td>
         <td valign="top">
            General Updates<br>
            - Added input option limits on W20 particle diffusivity <br>
            - Several fixes related to IMAS support <br>
         </td>
      </tr>
      <tr>
         <td valign="top">2022/02/14</td>
         <td valign="top">8.2.2</td>
         <td valign="top">
            MTM Model Updates <br>
            - Corrected sum calculation and updated calibration <br>
            - Added inertia switch <br><br>
            ETGM Model Introduced <br>
            - Designed to replace the existing Horton ETG model <br><br>
            General Updates<br>
            - Improved IMAS support <br>
            - General stability improvements <br>
         </td>
      </tr>
      <tr>
         <td valign="top">2021/06/01</td>
         <td valign="top">8.2.1</td>
         <td valign="top">
            General Updates<br>
            - Fixed default settings in TestMMM <br>
            - Added optional kyrhosMTM output variable <br>
         </td>
      </tr>
      <tr>
         <td valign="top">2021/04/13</td>
         <td valign="top">8.2.0</td>
         <td valign="top">
            General Updates<br>
            - Introduced support for IMAS <br>
         </td>
      </tr>
      <tr>
         <td valign="top">2021/04/12</td>
         <td valign="top">8.1.0</td>
         <td valign="top">
            Weiland Model Updates <br>
            - Introduced current gradient term <br>
            - Updated several equations<br><br>
            General Updates<br>
            - Updated file structure and removed old MMM95 files <br>
         </td>
      </tr>
      <tr>
         <td valign="top">2019/09/24</td>
         <td valign="top">7.2.0</td>
         <td valign="top">
            MTM Model Introduced <br>
         </td>
      </tr>
      <tr>
         <td valign="top">2010/02/11</td>
         <td valign="top">7.1.0</td>
         <td valign="top">Release of MMM 7.1.0</td>
      </tr>
   </tbody>
</table>

---

[Return](../README.md)
