Install NorESM2.2
=============================================

.. warning::
  Before continuing with the installation of NorESM2.2 please be aware that this is a preliminary (in development) version of the model, which has not yet been released or properly tested. Thus, care must be taken when using the results of this model version (pre-release). Also, only a limited number of compsets is presently working.

---------------------

To install NorESM2.2 follow instructions on `NorESM webpage <https://noresm-docs.readthedocs.io/en/noresm2/index.html>`_:

1) First clone the NorESM repository to a folder <noresm-base>: 
  ``git clone https://github.com/NorESMhub/NorESM.git <noresm-base>`` 

2) Then enter the specified directory:
  ``cd <noresm-base>``

3) List all available branches for checkout:
  ``git branch --all``

4) However, instead of checking out a branch/release-tag with model version 2.0.x, check out the branch remotes/origin/noresm2.2, e.g.:
  ``git checkout -b noresm2.2 origin/noresm2.2``

For issues related to model installation (e.g. svn-related) see `NorESM webpage <https://noresm-docs.readthedocs.io/en/noresm2/index.html>`_.


