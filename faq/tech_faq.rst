.. _tech_faq:

Technical FAQ
=============


How do I check the timing and cost of my experiments?
-------------------------------------------------

You will find a detailed overview of the model cost, throughput and run time for each model component in a subfolder named ``timing`` in the case folder (e.g. in ``~/NorESM/cases/<casename>/timing/``)
The filename is on the form ``cesm_timing.<casename>.JOBID.yymmdd-hhmmss``

My model experiment/simulation crashes and I don't know why!
-------------------------------------------------------------
If you experience crashes while setting up, building, submitting or archiving the experiment, you may find useful information in the log file in the casefolder (e.g. in ``~/NorESM/cases/<casename>/``). The filename is the same as the 15 first letters of the casename with no file extension. If you don't find a solution to your problem please post your question here: https://github.com/NorESMhub/NorESM/discussions


How do I know how many cpu hours I have left?
---------------------------------------------
The terminal command ``cost`` will provide a detailed overview of available and used cpu hours for each project you are assigned as a user.


How do I know how much storage space I am using?
------------------------------------------------
On NIRD and Betzy you can use the command ``dusage`` in the terminal. You can also get detailed information about specific projects by using ``dusage -p <project_name>`` ::

    dusage -p nnxxxk

or ::

    dusage -p nsxxxk


Another useful command to get information about the files is the ``find`` command. Typing ::

    find $dirs  -type f -printf “%u %s hlink=%n %h/%f\n” > filelist.txt
    
in the terminal, the filelist.txt will contain the size and file number usage of every member in the project.
The find command may take 30 minutes or more to run in large project spaces.

To find the total number of files for directory including subdirectories ::

    find . -type f | wc -l

To find the total number of files for all subdirectories separately ::

    find . -maxdepth 1 -type d | while read -r dir; do printf "%s:\t" "$dir"; find "$dir" -type f | wc -l; done


For getting info an all your projects (e.g. ns9252k ns9560k ns2345k), you may write a short bash script ::

    #!/bin/bash
    declare -a grps=(ns9252k ns9560k ns2345k)
    count=${#grps[@]}
    counter=0
    while [ $counter -lt ${count} ]; do
        dusage -p ${grps[$counter]} | awk '{print substr($0, 1, 10) substr($0, 40, 32) }' | grep -v '\-\-'    
        counter=$(( counter+1 ))
    done



Manage externals
----------------
I get a ``Dictionary keys changed`` error when checking out externals in my cloned repository. 

**Error:**

::
  
  ./manage_externals/checkout_externals
  Processing externals description file : Externals.cfg
  Checking status of externals: cam, dictionary keys changed during iteration
  

**Solution:**

Change to an older python version <= 3.7. If you have activated a conda environtment, you can deactivate conda 
(i.e. type ``deactivate conda`` in the terminal) when creating, building and submitting a case. 
The default python versions on BETZY and FRAM is 2.7.5 and will not create such errors.

Creating a case
----------------

I get a ``SyntaxError: invalid syntax`` error when creating a case in my cloned repository. 

**Error:**

::

  ./create_newcase --case ../../../cases/$CASENAME --mach fram --res f19_tn14 --compset NHIST
  Traceback (most recent call last):
    File "./create_newcase", line 9, in <module>
      from CIME.case          import Case
    File "/cluster/projects/nn2345k/$user/<noresm-base>/cime/scripts/Tools/../../scripts/lib/CIME/case/__init__.py", line 1, in <module>
      from CIME.case.case import Case
    File "/cluster/projects/nn2345k/$user/<noresm-base>/cime/scripts/Tools/../../scripts/lib/CIME/case/case.py", line 41, in <module>
      class Case(object):
    File "/cluster/projects/nn2345k/$user/<noresm-base>/cime/scripts/Tools/../../scripts/lib/CIME/case/case.py", line 72, in Case
      from CIME.case.case_submit import check_DA_settings, check_case, submit
    File "/cluster/projects/nn2345k/$user/<noresm-base>/cime/scripts/Tools/../../scripts/lib/CIME/case/case_submit.py", line 33
      print "limit0",resource.getrlimit(resource.RLIMIT_STACK)
            ^
  SyntaxError: invalid syntax

**Solution:**

Change to an older python version. If you are working in an active conda environtment, you can deactivate conda 
(i.e. type ``deactivate conda`` in the terminal) when creating, building and submitting a case. 
The default python versions on BETZY and FRAM is 2.7.5 and will not create such errors.


