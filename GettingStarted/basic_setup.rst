.. _basic_setup:

*********************************************
 Basic steps to set up, build and run NorESM
*********************************************

This guide provides basic instructions on how set up and run a standard NorESM case by executing 4 steps:

  - create a new case (the **create_newcase** script)
  - configure case (the **case.setup** script)
  - build case (the **case.build** script)
  - submit case (the **case.submit** script). 
  
It is assumed that you have sucsessfully downloaded the model (see :ref:`download_code`), which means you have a copy of the model on your computer in a folder with a name of your choice. For simplicity we call this folder ``<noresm-base>`` in this guide.


Create a new case
=================

The **create_newcase** script is an executable python script located in:
::

  <noresm-base>/cime/scripts/

The script for creating a new case takes several command line arguments as input to know how to configure your case.
Some of the most important arguments are as follows:

  - ``--case`` defines a casename of your choice and creates a folder by that name. The argument respects absolute and relative paths (e.g. ``--case ~/NorESM/cases/<casename>``). It is good parctice to make a case folder named ``cases`` where your NorESM cases are stored, e.g. ``mkdir NorESM/cases/`` in your home directory

  - ``--res`` defines the resolution of your run. See :ref:`experiments` for more details.

  - ``--compset`` defines what compset you will be using. A list of compsets for fully-coupled configurations can be found in the file *<noresm_base>/cime_config/config_compsets.xml* (see :ref:`amips` for compsets for AMIP-type simulations)

  - ``--mach`` defines the machine you will run the model on. The model NorESM3 has been configured to be run on a set of different machines (see list at :ref:`HPC_platforms`). If you are running the model on a machine not listed you will need to configure the model beyond this newbie guide. 

  - ``--project`` should correspond to the id of the project used in the batch system accounting. 
    
To investigate the full list of arguments, enter the *<noresm_base>/cime/scripts/* folder and run **create_newcase** with the ``--help`` argument: 
::
    
    cd <noresm_base>/cime/scripts/
    ./create_newcase --help

  
To create a new case, enter the scripts directory and run the **create_newcase** scripts: 
::
    
    cd <noresm_base>/cime/scripts/
    ./create_newcase --case <casepath>/<casename> --compset <compset> --res <grid-resolution> --mach <machine> --project <project-ID>

You have now created the case folder *<casepath>/<casename>*! Go to the case folder to start configuring your experiment.


Configure the case
==================
The case folder *<casepath>/<casename>/* is where you configure your case by changing enviroment files (such as the *<casepath>/<casename>/env_run.xml* file; see :ref:`experiment_environment`) or changing the user namelists for the different model components (files named ``user_nl_<component>`` where <component> is a model component such as ``cam``). But for now we stick to the standard out-of-the-box set up and configure the case as follows:
::

  cd <casepath>/<casename>
  ./case.setup
  

Build the case
==============
After your configuration is finished you can start bulding your case by invoking the case.build script from your case folder: 
::

  ./case.build

Which may take a while.


Submit your case
================
When your case has finished building you are ready to submit and run your case. This is done by invoking the case.submit script from your case folder:
::

  ./case.submit

And you are finished!
