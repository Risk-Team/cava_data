<h1 align="center">
  <br>
  <h5 align="center">cavapy: CORDEX-CORE Climate Data Access Simplified</h3>
  <br>
<div align="center">
   <img src="https://img.shields.io/pypi/dm/cavapy" alt="Downloads" style="display: inline-block;">
   <img src="https://img.shields.io/github/release-date-pre/Risk-team/cavapy" alt="release date" style="display: inline-block;">
</div>
</h1

---

## Introduction

`cavapy` is a Python library designed to streamline the retrieval of CORDEX-CORE climate models hosted on THREDDS servers at the University of Cantabria. Using the Open-source Project for a Network Data Access Protocol (**OPeNDAP**), users can directly access and subset datasets without the need to download large NetCDF files. This capability is part of the Climate and Agriculture Risk Visualization and Assessment (CAVA) [project](https://risk-team.github.io/CAVAanalytics/articles/CAVA.html), which focuses on providing high-resolution climate data for scientific, environmental, and agricultural applications.

With `cavapy`, users can efficiently integrate CORDEX-CORE data into their workflows, making it an ideal resource for hydrological and crop modeling, among other climate-sensitive analyses. Additionally, `cavapy` enables bias correction, potentially enhancing the precision and usability of the data for a wide range of applications.



## Data Source

The climate data provided by `cavapy` is hosted on the THREDDS data server of the University of Cantabria as part of the CAVA project. CAVA is a collaborative effort by FAO, the University of Cantabria, the University of Cape Town, and Predictia, aimed at democratising accessibility and usability of climate information.

### Key Datasets:
- **CORDEX-CORE Simulations**: Dynamically downscaled high-resolution (25 km) climate models, used in the IPCC AR5 report, featuring simulations from:
  - 3 Global Climate Models (GCMs)
  - 2 Regional Climate Models (RCMs)
  - Two Representative Concentration Pathways (RCPs: RCP4.5 and RCP8.5)
- **Reanalyses and Observational Datasets**:
  - ERA5
  - W5E5 v2

These datasets provide robust inputs for climate and environmental modeling, supporting scientific and policy-driven decision-making.

---

## Available Variables

`cavapy` grants access to critical climate variables, enabling integration into diverse modeling frameworks. The variables currently available include:

- **Daily Maximum Temperature (tasmax)**: °C  
- **Daily Minimum Temperature (tasmin)**: °C  
- **Daily Precipitation (pr)**: mm  
- **Daily Relative Humidity (hurs)**: %  
- **Daily Wind Speed (sfcWind)**: 2 m level, m/s  
- **Daily Solar Radiation (rsds)**: W/m²  

---

## Installation
cavapy can be installed with pip. Ensure that you are not using a python version > 3. 

```
conda create -n test python=3.11
conda activate test
pip install cavapy
```
## Process

The get_climate_data function performs automatically:
- Data retrieval in parallel
- Unit conversion
- Convert into a Gregorian calendar (CORDEX-CORE models do not have a full 365 days calendar) through linear interpolation
- Bias correction using the empirical quantile mapping (optional)

## Example usage

Depending on the interest, downloading climate data can be done in a few different ways. Note that GCM stands for General Circulation Model while RCP stands for Regional Climate Model. As the climate data comes from the CORDEX-CORE initiative, users can choose between 3 different GCMs downscaled with two RCMs. In total, there are six simulations for any given domain (except for CAS-22 where only three are available).
Since bias-correction requires both the historical run of the CORDEX model and the observational dataset (in this case ERA5), even when the historical argument is set to False, the historical run will be used for learning the bias correction factor.

It takes about 10 minutes to run each of the tasks below. For bigger areas/country, the computational time increases. For example, for Zambia it takes about 30 minutes.

### Bias-corrected climate projections
**By default all available climate variables are used. You can specify a subset with the variable argument**

```
import cavapy
Togo_climate_data = cavapy.get_climate_data(country="Togo", cordex_domain="AFR-22", rcp="rcp26", gcm="MPI", rcm="REMO", years_up_to=2030, obs=False, bias_correction=True, historical=False)
```
### Non bias-corrected climate projections

```
import cavapy
Togo_climate_data = cavapy.get_climate_data(country="Togo", cordex_domain="AFR-22", rcp="rcp26", gcm="MPI", rcm="REMO", years_up_to=2030, obs=False, bias_correction=False, historical=False)
```
### Bias-corrected climate projections plus the historical run

This is useful when assessing changes in crop yield from the historical period. In this case, we provide the bias-corrected historical run of the climate models plus the bias-corrected projections. 

```
import cavapy
Togo_climate_data = cavapy.get_climate_data(country="Togo", cordex_domain="AFR-22", rcp="rcp26", gcm="MPI", rcm="REMO", years_up_to=2030, obs=False, bias_correction=True, historical=True)
```
### Observations only (ERA5)

```
import cavapy
Togo_climate_data = cavapy.get_climate_data(country="Togo", cordex_domain="AFR-22", rcp="rcp26", gcm="MPI", rcm="REMO", years_up_to=2030, obs=True, bias_correction=True, historical=True, years_obs=range(1980,2019))
```
