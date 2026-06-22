.. _machine_specific_instructions:

*******************************
 Machine specific instructions
*******************************


Betzy
=====


Olivia
======

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

