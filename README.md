markdown
# README

**1. Compilation**

To compile WW3 with the scattering parameterization, the only required modification is to define [WFP1](https://github.com/Biao-Zhao/WaveF/blob/main/WW3/model/bin/switch_wfp) in the switch file. This will include the w3swfp1md.ftn module in the build process. No other configuration changes or compiler options are needed.

**2. Preparing the Forcing File**

The scattering parameterization requires certain variables to be provided in NetCDF format. Below are two screenshots of NetCDF file headers showing the required variables and dimensions.
From File 1 (e.g., [wavefarm_WF1_MO_N1D10_DEP20.nc](https://github.com/Biao-Zhao/WaveF/tree/main/cases/wavefarm_diffraction_MO_1T_D10))

| Variables    | Description |
|---------|------|
| **TurH** | Submerged depth of the turbine foundation (in meters). Positive downward from the sea surface. Used for floating structures or wave energy converter devices. |
| **TurD** | Foundation diameter (in meters). Used to compute the λ/D ratio in the parameterization. |
| **TurN** | Number of turbines within a single grid cell. Integer count (e.g., 0, 1, 2…). ⚠️ In the provided example, the units attribute for TurN is incorrectly set as "numbers m-2". This is a mistake. |
| **x, y** | Model grid axes (in meters for Cartesian coordinates). |
| **Time** | Time dimension. |


From File 2 (e.g., [wavefarm_WF2_MO_N1D10_DEP20.nc](https://github.com/Biao-Zhao/WaveF/tree/main/cases/wavefarm_diffraction_MO_1T_D10))
| Variables    | Description |
|---------|------|
| **Type** | 1 = monopile, 2 = floating. This variable is used to decide which parameterization regime to apply. |

To provide a visual overview of the spatial distribution, the figure below shows the layout of wind turbines as represented by the Type variable in the input file wavefarm_WF2.nc.

<div align="center">
    <img src="https://github.com/Biao-Zhao/WaveF/blob/main/cases/turbines_layout.png" alt="Turbine Layout">
</div>
<div align="center" style="font-family: 'Microsoft YaHei'; font-size: 40px; font-weight: bold;">
    <em>Turbine Layout </em>
</div>
<br><br>

**3. Running the Test Case**

Step1: Generate the model grid
Run the ww3_grid, this will generate the model grid and related files
```bash
./ww3_grid
```
Step2: Convert the forcing files
Firstly, prepare the wind files using ww3_prnc executable:
```bash
cp ww3_prnc_wind.inp ww3_prnc.inp
./ww3_prnc
```
This will generate wind.ww3, the binary wind forcing file required by WW3.

Then, the turbine structure input files (wavefarm_WF1_MO_N1D10_DEP20.nc and wavefarm_WF2_MO_N1D10_DEP20.nc) must be converted into binary format readable by WW3. this can be done by running
```bash
cp ww3_prnc_WF1.inp ww3_prnc.inp
./ww3_prnc
cp ww3_prnc_WF2.inp ww3_prnc.inp
./ww3_prnc
```
If the above commands execute successfully, two files, wfp1.ww3 and wfp2.ww3, will be generated.

Step3: Prepare the initial condition
```bash
./ww3_strt
```
Step4: Run the model
```bash
./ww3_shel
```
In the screenshot below, the key parameters in ww3_shel.inp related to the scattering scheme are highlighted in yellow. These parameters should be set to 'T' to activate reading of the associated input data (e.g., wfp1.ww3, wfp2.ww3).
<div align="center">
    <img src="https://github.com/Biao-Zhao/WaveF/blob/main/cases/sceenshot.png" alt=" setup of ww3_shel.inp">
</div>
<div align="center" style="font-family: 'Microsoft YaHei'; font-size: 40px; font-weight: bold;">
    <em> setup of ww3_shel.inp  </em>
</div>
<br><br>

Step5: Post-process the output to NetCDF
```bash
./ww3_ounf
./ww3_ounp
```
After running the model and completing the post-processing step with ww3_outf and ww3_outp, the output is written in NetCDF format. The figure below shows a sample visualization using ncview (a simple graphical tool commonly used to view NetCDF files interactively), confirming that the model executed successfully and produced significant wave height.

<div align="center">
    <img src="https://github.com/Biao-Zhao/WaveF/blob/main/cases/significant%20wave%20height.png" alt="Signifiant Wave Height">
</div>
<div align="center" style="font-family: 'Microsoft YaHei'; font-size: 40px; font-weight: bold;">
    <em>Signifiant Wave Height (m) </em>
</div>
<br><br>

**4. Notes and Tips**
   
The current scattering parameterization has only been tested in WW3 under a Cartesian grid setup. If you intend to apply it to spherical (longitude-latitude) coordinates or unstructured grids, the code will need to be adapted to support these scenarios. This includes handling integration radius when calculating scattered and incident wave energies. Future improvements could focus on making the implementation more flexible and portable across different grid configurations.

**6. Contact**

For more detailed information about the implementation and usage, please refer to following manuscript:

**Zhao, B.**, Sahlée, E., Du, J., Wu, L., A Wave Diffraction Theory-Based Wave Scattering Parameterization for Spectral Wave Models and Idealized Simulations. Submitted to Journal of Advances in Modeling Earth Systems. [https://doi.org/10.22541/essoar.175008916.65969995/v1](https://doi.org/10.22541/essoar.175008916.65969995/v1)

If you have questions, feel free to reach out at bzhao.metocean@gmail.com or bz5265@princeton.edu

