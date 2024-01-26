.. _noresm2.1_release_notes:

NorESM2.1 release notes
=======================

The NorESM2.1 release is a technical release that includes a few bug fixes along with numerous code improvements from the NorESM2.0 release (NorESM tag release-noresm2.0.6).

This release also introduces a set of regression tests for targeted compset / resolution combinations which is how supported simulations are defined in a technical release. There are 3 levels of support:

1. No support. If there are no regression tests defined for a given compset/resolution pair then the configuration is not technically supported and you will need to add a `--run-unsupported` flag to the `create_newcase` command.

2. Functional support. If there is at least one regression test for a given compset/resolution pair, then the configuration is technically supported and routinely tested and you do not need the `run-unsupported` flag to be added to the `create_newcase` command.

3. Scientific support. If there are tuned scientific simulations that accompany a given compset/resolution configuration, then the compset/resolution pair for that simulation will be scientifically supported.

Because of the simulation changes introduced by the bug fixes, this release has no scientifically supported simulations.

Below, we outline the significant changes implemented in the NorESM2.1 release. For more information on these, please see section :ref:`noresm2.1_test_runs`

Bug fixes:
----------
- (CAM) Fixed a bug in the Hetfrz code that caused answer changes when changing the number of tasks for a simulation. Please note that the calculation was incorrect for any number of tasks. See https://github.com/NorESMhub/CAM/issues/109

- (CAM) Fixed an `ncol/pcols` mismatch in calling the `clcoag` subroutine. This caused some columns to compute incorrect values (which columns are incorrect depends on the number of tasks). See https://github.com/NorESMhub/CAM/issues/24

- (CAM) Removed an artificial limit on the number of ice particles. See https://github.com/NorESMhub/CAM/issues/110 . This change causes significant differences in the climate. See :numref:`noresm2.1_test_runs` for a detailed look at the changes.

   For this release, we provide a method to modify the code to restore NorESM2 behavior. To activate this, go to line 2082 of <NorESM root>/components/cam/src/NorESM/micro_mg2_0.F90 and follow the instructions.

- (CAM) Resolved an error in the calculation of the dry deposition. See https://github.com/NorESMhub/CAM/issues/111

- (CAM) Fixed an `ncol/pcols` mismatch in several calls to `outfld`. This caused some columns to output incorrect values (which columns are incorrect depends on the number of tasks). See https://github.com/NorESMhub/CAM/issues/112

- (BLOM) iHAMOCC bug fixes for sediment alkalinity, sinking of free dust with potential effects on burial rates and burial time smoothing (time smoothing was formerly applied multiple times)

- (BLOM) iHAMOCC fixes to OpenMP code blocks

Other updates:
--------------
CIME / Python
~~~~~~~~~~~~~
- The NorESM case control system (CIME) now requires a minimum version of Python 3.8. Python 2.7 was retired at the beginning of 2020 and all Python versions prior to 3.8 are also now retired (“End of Life” in Python terminology). Please consult with your system administrators if you have trouble accessing a recent version of Python.

CAM
~~~
- There is a new compile-time XML configuration variable that enables `AEROCOM` diagnostic output. Simply execute this command _before_ building the code: `./xmlchange CAM_AEROCOM=TRUE`
- All code and diagnostic output formerly controlled by the `AEROFFL` preprocessor definition is now enabled by default whenever using `OSLO_AERO`
- The `DIRIND` preprocessor definition has been removed and the code previously contained inside the definitions are now enabled whenever using `OSLO_AERO`
- The preprocessor definitions and associated code for `RFMIPIRF`, `SPAERO`, and `COLTST4INTCONS` have been removed. The code previously contained inside these definitions are no longer in NorESM2.1
- Specifying the model grid alias as f19_f19 is identical to specifying the model grid alias as f19_f19_mtn14. This would apply to creating NF compsets.

BLOM
~~~~
- Two updated release v1.4.0 and v1.5.0 Release notes are summarized at https://github.com/NorESMhub/BLOM/discussions/319 and https://github.com/NorESMhub/BLOM/discussions/313
- v1.5.0 contained updates for hybrid coordinates
- v1.4.0 contained major iHAMOCC changes
  + Major iHAMOCC code re-organization

    + most pre-processor flags ("ifdefs") replaced by logical flags that are read in via namelist
    + all subroutines placed in modules
    + new module mo_param_bgc collects all model parameters and routines for initialization of model parameters

  + Major iHAMOCC code style changes (unified indentation, lower-case keywords)
  + Added regression testing functionality for BLOM, when run as part of NorESM
  + New mechanisms to create namelist files when run as part of NorESM (consistent with other NorESM components), through the file namelist_definition_blom.xml
  + New mesoscale eddy diffusivity options
  + C-isotope code for sediment now technically supported
  + Added support for simulating Ocean Alkalinity Enhancement

CISM
~~~~
- The first steps to enable coupled climate-ice sheet runs (KeyCLIM) are now in place for CISM for compsets N1850frc2G, NHISTfrc2G, NSSP585frc2G, NSSP585frc2extG. These cannot be currently run out of the box since they require CISM restart files that are not distributed with this release. However, this capability will be in place in NorESM2.3 in the spring.

Tested  configurations (compset / resolution combinations)
----------------------------------------------------------
The combinations of compset and resolution below have been run on Betzy. That is why simulations using these combinations can be created without using the `--run-unsupported` option to `create_newcase` (see introduction above).

Longer tests
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
- Compset: N1850, Grid: f19_tn14, Betzy, years 1600-1699 (branched off from N1850_f19_tn14_11062019, i.e., the point where the standard 500-year long piControl simulation also started).   These 100-year long simulations will give an indication of the TOA imbalance and of the drift in surface temperature and ocean heat content.
- Compset: NF1850norbc, Grid: f19_f19, Betzy, 30 years.  This simulation in combination with the NF1850norbc_aer2014 allows us to estimate the ERF.
- Compset: NF1850norbc_aer2014, Grid: f19_f19, Betzy, 30 years.

Short (regression) tests
~~~~~~~~~~~~~~~~~~~~~~~~
- Compset: N1850frc2 Grid: f09_tn14; Enabled by short exact restart and short run tests.
- Compset: NHISTfrc2 Grid: f09_tn14; Enabled by short exact restart and short run tests.
- Compset: N1850 Grid: f19_tn14; Enabled by short exact restart and short run tests.
- Compset: NHIST Grid: f19_tn14; Enabled by short exact restart and short run tests.
- Compset: N1850esm Grid: f19_tn14; Enabled by short exact restart and short run tests.
- Compset: F2000climo Grid: f19_f19_mtn14; Enabled by a short run and by exact restart tests with a processor count change.
- Compset: QPC6 Grid: f19_f19_mtn14; Enabled by a short run and by exact restart tests with a processor count change.
- Compset: FHS94 Grid: f19_f19_mtn14; Enabled by a short run and by exact restart tests with a processor count change.
- Compset: NF1850norbc Grid: f19_f19_mtn14; Enabled by a short run and by exact restart tests with a processor count change.
- Compset: NF1850norbc Grid: f19_f19_mtn14; Enabled by a short run.
- Compset: NF1850frc2norbc Grid: f09_f09_mtn14; Enabled by a short run and by exact restart tests with a processor count change.
- Compset: NF1850norbc_aer2014 Grid: f19_f19_mtn14; Enabled by a short run and by exact restart tests with a processor count change and with and without AEROCOM diagnostic output.
- Compset: NF1850frc2norbc_aer2014 Grid: f09_f09_mtn14; Enabled by a short run and by exact restart tests with a processor count change.
- Compset: NFHISTnorpddmsbc Grid: f09_f09_mtn14; Enabled by a short run and by exact restart tests with a processor count change.
