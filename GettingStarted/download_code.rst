.. _download_code:

****************************
 Downloading the model code
****************************

The NorESM3 model code is available through a public GitHub repository:
https://github.com/NorESMhub/NorESM

- Most users will probably want to clone the NorESM repository to a local machine using a git command-line client (see below). This gives easy access to both stable releases and development branches of NorESM.
- Users who do not wish to a git command-line client can download archive files (zip or tar.gz) of stable releases from https://github.com/NorESMhub/NorESM/tags


Make a clone of the NorESM repository
=====================================

You can obtain the code using the command-line git client on the appropriate machine as follows::

  git clone https://github.com/NorESMhub/NorESM.git <noresm-base>


where **<noresm-base>** is the name of the directory where the latest version of the released code will be stored. You can replace *<noresm-base>* with the directory name you like.

Enter the *<noresm-base>* folder ::

   cd <noresm-base>


Now you can check which remote servers you have configured:
::

  > git remote -v
  origin	https://github.com/NorESMhub/NorESM.git (fetch)
  origin	https://github.com/NorESMhub/NorESM.git (push)


And check which branch you are using ::

  > git branch

To use another version of the code, you can check out a specific tag or a branch.


Check out a specific NorESM tag or branch
=========================================

The default NorESM branch only includes information about the NorESM repository. In order to obtain a version of the NorESM3 source code, users should check out either a NorESM3 tag or branch. Static tags are usually most appropriate for regular use cases, whereas development branches are used for model development.

List all available tags ::

  > git tag --list

To check out a specific tag, use **git checkout <tag-name>** where *<tag-name>* is a tag for the list, for instance *noresm3_0_beta20* ::

  > git checkout noresm3_0_beta20

List all available branches ::

  > git branch --all

To check out a specific branch, for instance the *noresm3 development branch* ::

  > git checkout -b noresm_develop origin/noresm_develop

You can now inspect which tag or branch you are using by invoking the **git branch** command again. You can also inspect the commits log by invoking the **git log** command (to for instance only see the 3 commits, apply the **-n 3** option).


Download source code for NorESM3 components
===========================================

When checking out a NorESM tag or branch, the user will obtain a framework for the NorESM model, with specific configuration settings and pointers to component source code. The source code for NorESM components are store in separate repositories under the NorESMhub project.

Managing source code for different components is done with the aid of the git submodules framework. References to the tag versions of components are stored in the **.gitmodules** file under **<noresm-base>**.  

To launch the download::

    ./bin/git-fleximod update     [this will take one to a few minutes ...]

The *git-fleximod* tool will read the configuration file **.gitmodules** and will download all the external component models */path/to/<noresm-base>*.

Now you have a complete copy of the NorESM code in the directory *<noresm-base>*.  At this point you can enter the subdirectory *<noresm-base>/cime/scripts/* and start creating a case! (see :ref:`experiments`)

**Please note that if you checkout a new branch or tag, you will need to rerun "git-fleximod update" in order to download the correct version of the model code**


Confirm successful download of all components
---------------------------------------------
To confirm a successful download of all components, you can run *git-fleximod* with the ``status`` to show the status of the externals: ::

  ./bin/git-fleximod status             [shows status of externals]

