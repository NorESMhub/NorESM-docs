.. _aerosol_output:

Aerosol diagnostics and output
==============================


Outputing NorESM2 aerosol diagnostics
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
To run NorESM2 with more aerosol diagnostics add to ``user_nl_cam``:
:: 

  &phys_ctl_nl 
    history_aerosol = .true. 

Adding this entry to ``user_nl_cam gives`` will result in an additional 577 variables (+ ca. 13 % CPU-time).
Please see an overview of additional output varibales: :ref:`aerosol_output_history_aerosol_variables`

Decomposition of aerosol direct, semidirect and indirect radiative forcing
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For effective radiative forcing estimates, multiple calls to the
radiation code are necessary (see Ghan et al. 2012 for a detailed
explanation).  Previously, in NorESM2.0, additional
radiation-diagnostics for the decomposition of aerosol direct,
semidirect and indirect radiative forcing were activated by defining
the CPP-token ``AEROFFL``.  In NorESM2.1, this CPP-token no longer
exists and this additional output **always** accompanies the standard
CAM diagnostics and also does not need the ``history_aerosol =
.true.`` to be activated.  Please see an overview of the additional
output variables: :ref:`aerosol_output_aeroffl_variables`

Output additional NorESM2 - AEROCOM diagnostics
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
NorESM2 can be set up to output additional aerosol diagnostics for use in AeroCom (https://aerocom.met.no/index.html) or other studies where there is a need for extensive aerosol diagnostics.
In both NorESM2.0 and NorESM21, the CPP token ``AEROCOM`` must be defined in order to activate this output.
However, in NorESM2.1, this variable  is now activated via a new CAM build-time xml variable, ``CAM_AEROCOM``.
By default ``CAM_AEROCOM`` is FALSE. To activate the CPP token ``AEROCOM`` at build time, simply issue the command ::

    ./xmlchange CAM_AEROCOM=TRUE

before calling ``./case.build``.
If ``AEROCOM`` is activated, an addition 149 variables are output (+ ca. 13% CPU-time). Please see an overview of the additional output variables: 
:ref:`aerosol_output_aerocom_variables`


References
^^^^^^^^^^^^ 

Ghan, S.J., X. Liu, R.C. Easter, R. Zaveri, P.J. Rasch, J. Yoon, and B. Eaton, 2012: Toward a Minimal Representation of Aerosols in Climate Models: Comparative Decomposition of Aerosol Direct, Semidirect, and Indirect Radiative Forcing. J. Climate, 25, 6461–6476, https://doi.org/10.1175/JCLI-D-11-00650.1
