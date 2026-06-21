.. _NorESM2vsNorESM3:

***********************************
 Migrating from NorESM2 to NorESM3
***********************************

This short guide is intended primarily for users who are familiar with NorESM2, and are looking for what is different in NorESM3, with respect to setup and use of the model system.


Compsets and grid configurations
================================




Changes to model infrastructure
===============================

checkout_externals vs. git-fleximod
-----------------------------------

* Discussion page: `NorESM #599 <https://github.com/NorESMhub/NorESM/discussions/599>`_
* See `./bin/git-fleximod --help` for further info and options.
* Developed at NCAR: `<https://github.com/ESMCI/git-fleximod>`_

The tag ``noresm2_5_alpha08`` replaces ``checkout_externals`` with ``git-fleximod`` for checking out NorESM source code. Related to this change, external components are now defined in the ``.gitmodules`` files instead of the ``Externals.cfg`` file.

Updating components managed by git-fleximod
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Updating a NorESM component tag has two goals:

* Commit NorESM (top level) so that all component submodules are recorded in the desired state. That is, running ``git checkout --recurse-submodules <new_tag>`` at the top level should checkout the NorESM tree to the correct state.
* Document the correct tag(s) by updating the ``fxtag`` entries in the top-level .gitmodules file

This procedure should always work:

#. In your NorESM clone, create a new update-BLOM-tag branch and fully check it out (``git checkout -b update-blom-tag NorESM/noresm_develop && ./bin/git-fleximod update --optional``).
   
    * ``git submodule status`` should be 'clean' (the first column is blank for all submodules).

#. In components/blom, checkout the new tag and run ``git submodule update``.
   
    * ``git submodule status`` should be clean.
      
#. At the top level, update the ``fxtag`` value in the ``blom`` submodule section of the .gitmodules file with the new BLOM tag.
#. Run ``git commit -a -m 'Updated BLOM tag to rev.x.y.z.q'``.
#. Push this to your fork and open a PR.

The rpointer (restart) filename format
--------------------------------------

The restart file ``rpointer.<component>`` used to be a file pointing to the latest restart file for each component. In NorESM3 this filename now includes a timestamp, and a new file is created for each set of restart files instead of overwriting the existing file. The reference timestamp is controled by the ``xmlcange`` option ``DRV_RESTART_POINTER``.

Example script setting timestamp (month 12, year 1):

.. code-block:: bash

   ## Copy YEAR-001 restart files to run directory 
   RESTCASE=${RESTCASE:-"n1850.ne16_tn14.noresm3_0_beta03b-run6_yr001.20251029"} 
   RESTDIR=${RESTDIR:-"/cluster/work/users/${USER}/archive/${RESTCASE}/rest"} 
   RESTDATE=${RESTDATE:-"0001-12-01"} 
   cp ${RESTDIR}/${RESTDATE}-00000/* ${RUNDIR} 
 
   # restart settings 
   ./xmlchange RUN_TYPE=branch 
   ./xmlchange RUN_REFCASE=${RESTCASE} 
   ./xmlchange RUN_REFDATE=${RESTDATE}  
   ./xmlchange DRV_RESTART_POINTER="rpointer.cpl.${RESTDATE}-00000"


Archive and compress job scripts
--------------------------------

In NorESM2, the archive and compression scripts were run together in a single ``case.st_archive`` job. For NorESM3 these processes are in two separate jobs, ``case.st_arcive`` and ``case.compress``, hence each simulation job spawns 3 tasks instead of 2 tasks in the HPC queue system. This does not have much practical implications from a user perspective, except that each of these tasks have their own ``task_count``, ``tasks_per_node`` and ``walltime`` parameters.
