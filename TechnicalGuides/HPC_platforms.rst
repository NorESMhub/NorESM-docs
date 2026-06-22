.. _HPC_platforms:

************************************
 Running on different HPC platforms
************************************


Betzy @ Sigma2
==============

Input data is stored in /cluster/shared/noresm/inputdata/

Apply for membership in NorESM shared data storage (manager: mben@norceresearch.no) for access to the folder.

The run and archive directories are stored in /cluster/work/users/<user_name>/

NorESM requires at least Python3 for setup and building. A recommended set of modules is provided by
::

  module purge
  module load GCCcore/11.3.0
  module load git/2.36.0-GCCcore-11.3.0-nodocs
  module load Python/3.10.4-GCCcore-11.3.0

Create a new case: ::

    ./create_newcase --case ../../../cases/<casename> --mach betzy --res <resolution> --compset <compset_name> --project <project_name> --user-mods-dir <user_mods_dir> --run-unsupported


Olivia @ Sigma2
===============

Recommended modules on Olivia
-----------------------------
Olivia provides different module stacks with software and libraries provided by NRIS, EESSI and Cray. Software and libraries from these stacks are not interchangeable. It is not recommended that users include module settings in their `~/.bashrc` or `~/.profile` files. Instead, we recommend to save and re-load a module collection list in the following way.

- First time use ::

    module purge
    module load NRIS/CPU
    module load Python/3.12.3-GCCcore-13.3.0
    module save mod_noresm

- Subsequent use ::

    module r mod_noresm

The save name `mod_noresm` is a personal preference for the module collection list. This is a minimal requirement, users may want to add e.g. CDO or ncview as well. All named collection lists are listed by running ::

    module savelist

To see what modules are included in a named collection, run ::

    module describe <list name>

