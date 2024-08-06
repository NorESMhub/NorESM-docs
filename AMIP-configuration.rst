AMIP configuration
=============================================

.. warning::
  * Again, care must be taken when using the results of this model version (pre-release). 
  * Only a limited number of compsets is presently working. Here, we show how to run a historical AMIP experiment on Betzy/Sigma2.

---------------------

To create an experiment ``case`` follow instructions on `NorESM webpage <https://noresm-docs.readthedocs.io/en/noresm2/configurations/configurations.html>`_ or, more specifically, for AMIP experiments follow instructions `here <https://noresm-docs.readthedocs.io/en/noresm2/configurations/amips.html>`_. Below we show an example for only one specific compset, which has historical prescribed sea-surface-temperatures (SSTs) and historical forcing, typically 1979-2014 (or longer).

1) First enter the ``scripts`` folder of the  `NorESM2.2 <https://noresm22-nudging-regional.readthedocs.io/en/latest/Install-NorESM2.2.html>`_ directory: 
  ``cd ~/<noresm-base>/cime/scripts/`` 

2) Then run command to create a new case, e.g., <case-name>:
  ``./create_newcase --case ~/<noresm-base>/cases/<case-name> --mach betzy --res f09_f09_mg17 --compset NFHISTfsstfrc2 --project <project-name>``

  Here, we specify machine as "betzy", a historical AMIP compset with frc2 forcing (generally recommended for NorESM), and the model will run with project resources <project-name>, typically of form "nn1234k" or similar. Note that different AMIP compsets can be found here: 

  ``vi <noresm-base>/components/cam/cime_config/config_compsets.xml``. 

3) Enter the folder 
  ``cd ~/<noresm-base>/cases/<case-name>/``

  and specify the following in user namelist ``user_nl_cam`` (if file does not exist create it)

.. admonition:: user_nl_cam

  &chem_surfvals_nl

    ch4vmr         = -1.0D0

    co2vmr         = -1.0D0    

    f11vmr         = -1.0D0

    f12vmr         = -1.0D0

    flbc_file      = '/cluster/shared/noresm/inputdata/atm/waccm/lb/LBC_1750-2015_CMIP6_GlobAnnAvg_c180926.nc'

    flbc_list      = 'CO2','CH4','N2O','CFC11eq','CFC12'

    flbc_type      = 'SERIAL'

    n2ovmr         = -1.0D0

    scenario_ghg   = 'CHEM_LBC_FILE'

  /

Note that the ``flbc_file`` path above is only relevant for "betzy" machine and can be set to another file if relevant. These variables (especially those set to "-1.0D0" should be specified to avoid an error where model is trying to run with both constant GHG/CFC gases and with time-evolving gases.

4) Change other variables in user_nl_cam or other components' namelists, set environment variables in files starting with "env_", e.g., "env_run.xml" and setup, build and submit the model as shown on `NorESM webpage <https://noresm-docs.readthedocs.io/en/noresm2/configurations/amips.html>`_.

