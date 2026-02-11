# Norwegian Earth System Model Documentation

[![License: GNU LGPL 3.0](https://img.shields.io/badge/license-LGPL--3.0-blue.svg)](https://www.gnu.org/licenses/lgpl-3.0.en.html)
[![NordicESMhub chat](https://img.shields.io/badge/zulip-join_chat-brightgreen.svg)](https://nordicesmhub.zulipchat.com/)

The NorESM documentation on Read the Docs is built from the branches in this repository. The branches correspond to different versions of the NorESM source code.

| version     | build status | note |
| ----------- | ------------ | ---- |
| NorESM1     | [![NorESM1 Documentation Status](https://readthedocs.org/projects/noresm-docs/badge/?version=noresm1)](https://noresm-docs.readthedocs.io/en/noresm1/?badge=noresm1)         | Model version used for CMIP5 and IPCC AR5 |
| NorESM2     | [![NorESM2 Documentation Status](https://readthedocs.org/projects/noresm-docs/badge/?version=noresm2)](https://noresm-docs.readthedocs.io/en/noresm2/?badge=noresm2)         | Model version used for CMIP6 and IPCC AR6 |
| NorESM2.1   | [![NorESM2.1 Documentation Status](https://readthedocs.org/projects/noresm-docs/badge/?version=noresm2.1)](https://noresm-docs.readthedocs.io/en/noresm2.1/?badge=noresm2.1)     | bugfix and technical release version      |
| NorESM2.2   | [![NorESM2.2 Documentation Status](https://readthedocs.org/projects/noresm-docs/badge/?version=noresm2.2)](https://noresm-docs.readthedocs.io/en/noresm2.2/?badge=noresm2.2)     | unvalidated development version           |
| NorESM2.3   | [![NorESM2.3 Documentation Status](https://readthedocs.org/projects/noresm-docs/badge/?version=noresm2.3)](https://noresm-docs.readthedocs.io/en/noresm2.3/?badge=noresm2.3)     | unvalidated development version : NorESM2.1 + FORCeS + KeyCLIM  |
| NorESM2.5   | [![NorESM2.5 Documentation Status](https://readthedocs.org/projects/noresm-docs/badge/?version=noresm2.5)](https://noresm-docs.readthedocs.io/en/noresm2.5/?badge=noresm2.5)     | development version : pre-NorESM3         |
| NorESM3.0   | [![NorESM3.0 Documentation Status](https://readthedocs.org/projects/noresm-docs/badge/?version=noresm3.0)](https://noresm-docs.readthedocs.io/en/noresm3.0/?badge=noresm3.0)     | development version : NorESM3             |


## How to contribute to the NorESM documentation

- step 1: Fork this repository as shown in the figure below.

<img src="img/fork_NorESM-docs.png" alt="Fork NorESM documentation repository">

- step 2: Go online to [NorESM documentation](https://noresm-docs.readthedocs.io/en/main/) and whenever you would like to update the documentation, click on "Edit on GitHub".

<img src="img/edit_on_github.png" alt="Edit documentation online">

- step 3: Then click on the "pen" (see image below) and write your text ([reStructuredText](http://docutils.sourceforge.net/docs/user/rst/quickref.html)) 


<img src="img/edit_in_your_fork.png" alt="Edit the file in your fork">

- step 4: Save your changes in your forked repository and create a pull request.


<img src="img/propose_changes.png" alt="Propose your changes">


If you do not like to update the documentation online and prefer to use your favorite editor locally on your machine/laptop, you can skip step-3 and 4 and then clone your forked repository to edit the files locally. Once pushed to your forked github repository, you can create a pull request and propose your changes.

Example on how to see your changes locally with sphinx:

> Prepare your PC/MAC
> 
> ```shell
> $ conda install sphinx
> $ conda install -c conda-forge sphinxcontrib-bibtex
> $ conda install -c conda-forge sphinx_rtd_theme
> ```
> Copy NorESMdoc repository to local
> 
> ```shell
> $ git clone git@github.com:NorESMhub/NorESM-docs.git
> ```
>
> go to NorESM-docs dir
>
> ```shell
> $ sphinx-build . _build
> ```
>
> open _build/index.html in browser

More info
See https://coderefinery.github.io/documentation/sphinx/

