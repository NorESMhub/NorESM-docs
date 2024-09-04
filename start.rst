.. _start:


Introduction
=============

NorESM2.5
---------
The Norwegian Earth System Model version 2 (NorESM2) is an coupled Earth System Model developed by the NorESM Climate modeling Consortium (NCC). NorESM2 is based on the second version of the Community Earth System Model, CESM2 (https://www.cesm.ucar.edu/models/cesm2), developed and operated at the National Center for Atmospheric Research (NCAR), Boulder, US. 

The NorESM specific development is led by the Norwegian Meteorological Institute and NORCE Norwegian Research Centre AS. Other partners involved are the University of Oslo (UiO), CICERO, Nansen Environmental and Remote Sensing Center (NERSC) and the University of Bergen (UiB). 

NorESM2.5 includes major structural and component updates relative to NorESM2, and is intended as an intermediate version leading up to NorESM3, that will be the designated CMIP7 version of NorESM.

NorESM2 specific additions to CESM2 includes (but is not limited to):
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

- Atmosphere model : CAM6-Nor replaces standard CAM

  - Atmospheric chemistry/aerosol/cloud module: OsloAero6  (Kirkevåg et al. GMD, 2018)
  - Atmospheric dynamics/physics: Improved conservation of energy and angular momentum (Toniazzo et al. GMD, 2020)
  - Parameterization of turbulent air-sea fluxes (see :ref:`amips` for more details)
  
- Sea-ice model:

  - Wind drift of snow
- Ocean model : Isopycnic coordinate model BLOM 
- Ocean biogeochemical model : iHAMOCC

  - HAMburg Ocean Carbon Cycle model (HAMOCC) adopted for use with the isopycnic ocean model BLOM and further developed (Tjiputra et al. GMD, 2020)

For a short description of the model components, please see :ref:`model-description`

Ocean model component in NorESM2:
+++++++++++++++++++++++++++++++++
BLOM/iHAMOCC replaces MICOM/HAMOCC as the combined physical and biogeochemical ocean model component in NorESM2. BLOM/iHAMOCC is publically available soure code licensed under a LGPLv3 license, but is otherwise a direct descendant of the MICOM/HAMOCC model component. New applications of NorESM2 will only use BLOM/iHAMOCC, but older data sets may still refer to MICOM/HAMOCC.
