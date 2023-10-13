.. _testing:

..
   _Thanks to the CTSM software engineering team for providing a starting point as well
   as material for this document:

===================================
Overview of CIME-CCS system testing
===================================

System tests are useful for:

* Verifying various requirements for a given model resolution/configuration combination:

  * Model runs to completion successfully

  * Model results does not change from baselines

  * Model restarts without affecting the results (bit-for-bit)

  * Model results are independent of processor count

  * Code is thread safe

  * Compilation with debug flags, e.g., to pick up:

    * Array bounds problems

    * Floating point errors (note that without debug flags, floating
      point exceptions are normally not triggered)

  * And other specialty tests (e.g., init_interp for CTSM)

* Verifying those requirements across a wide range of model
  configurations (e.g., making sure NorESM tests still works when new
  component configurations, physics and resolutions are added)

* Making sure that you haven't introduced a bug that changes answers in
  some configurations, when answer changes are unexpected

  * This is one of the most powerful aspects of the system tests

  * For this to work best, you should try to separate your changes into:

    * Bit-for-bit refactoring

    * Answer-changing modifications that are as small as possible, and
      so can be carefully reviewed

The CIME-CCS test system runs tests that involve:

#. Doing one or more runs of the model with target configurations and
   resolutions and verifying that they run to completion. (If there
   is more than one run in a single test, then there is some change
   in configuration between the different runs.)

#. If there is more than one run in a single test, then comparing these
   runs to ensure they were bit-for-bit identical as expected.

#. If desired, comparing results with existing baselines (to ensure
   results are bit-for-bit the same as before), and/or generating new
   baselines.

#. Providing final test results: An overall PASS/FAIL as well as
   PASS/FAIL status for individual parts of the test.

========================
 Anatomy of a test name
========================

Basic test name specification
=============================

A test name looks like this; bracketed components are optional::

  Testtype[_Testopt].Resolution.Compset.Machine_Compiler[.Testmod]

(There may be more than one Testopt, separated by underscores.)

Notice that this string specifies all required options to
``create_newcase``.

An example is::

  SMS_D_Ld3.f19_f19_mtn14.F2000climo.betzy_intel

Meaning of the elements of a test name
======================================

``Testtype``: code specifying the type of test to run; common test
types are given below.  The ``SMS`` test in the above example is a
basic "smoke" test that ensures that the particular
configuration/resolution combinations can successfully run and
optionally check for any undesired answer changes.

``Testopt``: One or more options modifying some high-level
configuration options. In the above example, we are compiling in debug
mode (``_D``), and running for 3 simulated days (``_Ld3``). Another
common option is the specification of the number of processing
elements that the test should use (``_P128``).

``Resolution``: Any resolution that you would typically specify via
the ``--res`` argument to ``create_newcase``. In the above example, we
are using the ``f19_f19_mtn14`` resolution, which is a 2 degree global
grid for atm/lnd/ice/ocn and that has an ocean mask corresponding to
tnx1v4 that has been mapped to the f19 grid. This grid combination is
normally used for testing CAM in 'stand-alone mode' where the ocean is
a data ocean and ice is run in prescribed mode and all components are
on the finite volume 1.9 degree grid.

``Compset``: Any compset that you would typically specify via the
``--compset`` option to ``create_newcase``. In the above example, we
are using the ``F2000climo`` compset.

``Machine``: The name of the machine you are running on (``betzy`` in
the above example).

``Compiler``: The name of the compiler to use (``intel`` in the above
example).

``Testmod``: A directory containing arbitrary ``user_nl_*`` contents and
``xmlchange`` commands. See below for more details.

=============================
 Test Categories and Options
=============================

.. Test Categories:

Test Categories
===============

The following are the most commonly used test types and their meaning:

``SMS``:
**Basic run functionality test (smoke test)**. Carries out a single run.

``ERS``:
**Exact restart test**.
Compares two runs, ensuring that they give bit-for-bit identical
results. (1) Do a straight-through run, which writes a restart file
just over half-way through. (2) Do a restart run starting from the
restart file written by (1)

``ERP``:
**Exact restart test with changed processor count on restart .**
This covers the exact restart functionality of the ``ERS`` test, and
also halves the processor count in run (2). In addition, if multiple
threads are used, it also halves the thread count in run (2). Thus, in
addition to ensuring that restarts are bit-for-bit, it also ensures
that answers do not depend on processor count, and optionally that
answers do not depend on threading. This is nice in that a single test
can verify a few of our most important system requirements. However,
when the test fails, it can sometimes be harder to track down the
cause for the problem. (To debug a failed ERP test, you can run the
same configuration in an ERS, PEM and/or PET test.)

``ERI``:
**Tests a combination of hybrid/branch/exact restart test**
The ERI test carries out the following 3 runs:
(1) Do an initial run and writing restarts with short term archiving
on. (2) Do a hybrid run with ref1 restarts and write additional
restarts with short term archiving on.
(3) Do a branch run starting from restarts written in (2).

Other available tests are listed below.

``ERR``:
**Short term archiving test**.
This is an ERS test except that after the initial run the short term
archive tool is run which moves model output out of the run directory
into the short-term archive directory then the restart run is staged
from the short term archive directory.

``REP``:
**Reproducibility test**:
Do two identical runs and verify if they give the same results.

``PET``:
**Modified Threading OpenMP bit-for-bit test**.
(1) Do an initial run where all components are threaded and add a suffix
of `base` to the output files.  (20 Do another initial run with nthrds=1
for all components and add a suffix of `single_thread` to the output
files.  Compare `base` and `single_thread` output files.

``PEM``:
**Modified PE counts MPI bit-for-bit test**.
(10 Do an initial run with default PE layout and add a suffix of `base` to
the output files. (2) Do another initial run with modified PES
(NTASKS_XXX => NTASKS_XXX/2) and add a suffix of `modpes` to the
output fields.  Compare the `base` and `modpes` output files.

``PEA``:
**Single PE test of MPI-SERIAL versus MPI**
(1) Do an initial run on 1 pe with MPI and add a suffix of `base` to
the output files. (2) Do the same run on 1 pe with MPI-SERIAL and add
a suffix of `mpiserial` to the output files.  Compare the `base` and
`mpiserial` output files.

``SEQ``
**Different PE-layout sequencing test**:
(1) Do an initial run test with out-of-box PE-layout and add a `base`
suffix to the output files. (2) Do a second run where all root pes are
at pe-0 and add a `seq` suffix to the output files.  Compare the
`base` and `seq` output files.

``PFS``:
**System performance test**.
Do 20 day run with no restart files created.


Common test options
===================

The following are the most commonly used test options (optional strings
appearing after the test type, separated by ``_``):

``_D``: Compile in debug mode. Exactly what this does depends on the
compiler. Typically, this turns on checks for array bounds and various
floating point traps. The model will run significantly slower with this
option.

``_L``: Specifies the length of the run. The default for most tests is
5 days. Examples are ``_Ln9`` (9 time steps), ``_Ld3`` (3 days),
``_Lm6`` (6 months), and ``_Ly5`` (5 years).

``_P``:
Specifies the processor count of the run. Syntax is ``_PNxM`` where
``N`` is the number of tasks and ``M`` is the number of threads per
task. For example, ``_P32x2`` runs with 32 tasks and 2 threads per
task. Default layouts of many tests all have just 1 thread per task,
but the ability to run with threading (and get bit-for-bit identical
answers) is an important requirement for several NorESM components
(e.g. CAM, CTSM, CICE). Thus, many tests (and particularly ERP tests)
specify processor layouts that use 2 threads per task.

Testmods
========

Few NorESM tests simply run an out-of-the-box compset without any other
modifications. `Testmods` provide a facility to make arbitrary changes
to xml and namelist variables for a particular test. They typically
serve two purposes:

#. Adding more frequent component history output, additional component history streams,
   and/or additional component history variables. The more frequent history output
   is particularly important, since otherwise a short (e.g., 5-day) test
   would not produce any component (e.g. CAM) diagnostic output (since the default
   output frequency is monthly).

#. Making configuration changes specific to this test, such as turning
   on a non-default parameterization option or changing the coupling
   frequency to enable short runs.

Testmods directories are assumed to be in the component
``cime_config/testdefs/testmods_dirs``. Dashes are used in place of
slashes in the path relative to that directory. As an example, for CAM a testmod of
``outfrq9s`` is found in
``$SRCROOT/components/cam/cime_config/testdefs/testmods_dirs/cam/outfrq9s/``.
As another example, for CTSM a testmod of ``default`` is found in
``$SRCROOT/components/cam/cime_config/testdefs/testmods_dirs/clm/default/``.

Testmods directories can contain three types of files:

* ``user_nl_*`` files: The contents of these files are copied into the
  appropriate ``user_nl`` file (e.g., ``user_nl_cam``) in the case
  directory. This allows you to set namelist options.

* ``shell_commands``: This file can contain xmlchange commands that
  change the values of xml variables in the case.

* ``include_user_mods``: Often you want a testmod that is basically the
  same as some other testmod, but with a few extra changes. For example,
  many of CTSM testmods use the `default` testmod as a starting point,
  then add a few things on top of that. ``include_user_mods`` allows you
  to set up these relationships without resorting to unmaintainable copy
  & paste. This file contains the relative path to another testmod
  directory to include; for example, its contents may be::

    ../default

  First, the ``user_nl_*`` and ``shell_commands`` contents from the
  included testmod are applied, then the contents from the current
  testmod are applied. (So changes from the current testmod take
  precedence in case of conflicts.)

  These includes are applied recursively, if you include a directory
  that itself has an ``include_user_mods`` file. Also, in principle, an
  ``include_user_mods`` file can include multiple testmods (one per
  line), but in practice we rarely do that, because it tends to be more
  confusing than helpful.

=========================
 Basic create_test usage
=========================

Running a single test
=====================

Running a single test is as simple as doing the following from
``cime/scripts``::

  ./create_test TESTNAME

For example::

  ./create_test SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default

.. note::
   In contrast to ``create_newcase``, ``create_test`` automatically runs
   ``case.setup``, ``case.build`` and ``case.submit`` for you - so that
   single ``create_test`` command will build and run your case.

Options to create_test
======================

A full list of possible options to ``create_test`` can be viewed by
running ``create_test -h``. Here are some of the most useful options:

* ``-r /path/to/test/root``: By default, the test's case directory is
  placed in the directory given by ``CIME_OUTPUT_ROOT`` (e.g.,
  ``/cluster/work/users/$USER/noresm`` on betzy). This has the benefit that the
  ``bld`` and ``run`` directories are nested under the case
  directory. However, if your scratch space is cluttered, this can make
  it hard to find your test cases later. If you specify a different
  directory with the ``-r`` (or ``--test-root``) option, your test cases
  will appear there, instead. Specifying ``-r .`` will put your test
  cases in the current directory (analogous to the operation of
  ``create_newcase``). This option is particularly useful when running
  large test suites: We often find it useful to put all tests within a
  given test suite within a subdirectory of ``CIME_OUTPUT_ROOT`` - for
  example, ``-r /cluster/work/$USER/noresm/HELPFULLY_NAMED_SUBDIRECTORY``.

* ``--walltime HH:MM``: By default, the maximum queue wallclock time for
  each test is generally the maximum allowed for the machine. Since
  tests are generally short, using this default may result in your jobs
  sitting in the queue longer than is necessary. You can use the
  ``--walltime`` option to specify a shorter queue wallclock time, thus
  allowing your jobs to get through the queue faster. However, note that
  all tests will use the same maximum walltime, so be sure to pick a
  time long enough for the longest test in a test suite. (Note: If you
  are running a full test suite with the xml options documented below,
  walltime limits may already be specified on a per-test basis. However,
  as of the time of this writing, this capability is not yet used for
  the CTSM test suites.)

Parsing test output
===================

As a test runs through its various phases (setup, build, run, etc.),
it updates a file named ``TestStatus`` in the test's case
directory. In the example below, we are comparing the current test
results to another test that was run and resides in the compare
directory ``baseline_dir``. You would expect the subdirectory
``ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default``
to be in ``baseline_dir``. After a test completes, a typical
``TestStatus`` file will look like this::

  PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default CREATE_NEWCASE
  PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default XML
  PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default SETUP
  PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default SHAREDLIB_BUILD time=175
  PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default NLCOMP
  PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default MODEL_BUILD time=96
  PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default SUBMIT
  PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default RUN time=606
  PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default COMPARE_base_rest
  PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default BASELINE baseline_dir
  PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default TPUTCOMP
  PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default MEMLEAK insuffiencient data for memleak test

(This is from a test that had comparisons with baselines, which we have
not described yet.)

The three possible status codes you may see are:

* ``PASS``: This phase finished successfully

* ``FAIL``: This phase finished with an error

* ``PEND``: This phase is currently running, or has not yet started. (If
  a given phase is listed as ``PEND``, subsequent phases may not be
  listed yet in the ``TestStatus`` file.)

If a test completes, you should normally see all ``PASS`` status
values to indicate that the test completed successfully. However,
``FAIL`` values for ``TPUTCOMP`` and ``MEMCOMP`` should be taken with
a grain of salt.  These compare throughput and memory usage with the
baseline and system variability can often cause these to fail even
though there is no fundamental problem.

More detailed test output can be found in the file named
``TestStatus.log`` in the test's case directory. If a test fails, this
is the first place file you should look at.

Finding more details on failed comparisons
==========================================

Many test types perform two runs and then compare the output from the
two, expecting bit-for-bit identical output. For example, an ``ERS``
test compares a straight-through run with a restart run. The comparison
is done by comparing the last set of history files from each run. (If,
for example, there are h0 and h1 history files, then this will compare
both the last h0 file and the last h1 file.)

.. note::

   These comparisons are done via a custom tool named ``cprnc``, which
   compares each field and, if differences are found, computes various
   statistics on these differences. On betzy this tool has been built in
   ``/cluster/shared/noresm/tools/cprnc/cprnc``.

If any one of these comparisons fails, you will see a line like::

  FAIL ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default COMPARE_base_rest

As usual, more details can be found in ``TestStatus.log``, where you
will find output like this::

  2022-09-26 10:10:24: Comparing hists for case 'ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud' dir1='/cluster/work/users/mvertens/noresm/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud/run', suffix1='base',  dir2='/cluster/work/users/mvertens/noresm/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud/run' suffix2='rest'
    comparing model 'datm'
      no hist files found for model datm
    comparing model 'clm'
      /cluster/work/users/mvertens/noresm/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud/run/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud.clm2.h0.0001-01-04-00000.nc.base did NOT match /cluster/work/users/mvertens/noresm/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud/run/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud.clm2.h0.0001-01-04-00000.nc.rest
      cat /cluster/work/users/mvertens/noresm/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud/run/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud.clm2.h0.0001-01-04-00000.nc.base.cprnc.out
      /cluster/work/users/mvertens/noresm/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud/run/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud.clm2.h1.0001-01-04-00000.nc.base did NOT match /cluster/work/users/mvertens/noresm/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud/run/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud.clm2.h1.0001-01-04-00000.nc.rest
      cat /cluster/work/users/mvertens/noresm/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud/run/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud.clm2.h1.0001-01-04-00000.nc.base.cprnc.out
    comparing model 'sice'
      no hist files found for model sice
    comparing model 'socn'
      no hist files found for model socn
    comparing model 'mosart'
      no hist files found for model mosart
    comparing model 'cism'
      no hist files found for model cism
    comparing model 'swav'
      no hist files found for model swav
    comparing model 'cpl'
      /cluster/work/users/mvertens/noresm/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud/run/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud.cpl.hi.0001-01-04-00000.nc.base did NOT match /cluster/work/users/mvertens/noresm/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud/run/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud.cpl.hi.0001-01-04-00000.nc.rest
      cat /cluster/work/users/mvertens/noresm/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud/run/ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud.cpl.hi.0001-01-04-00000.nc.base.cprnc.out
  FAIL

Notice the lines that say ``did NOT match``. Also notice the lines
pointing you to various ``*.cprnc.out`` files. (For convenience,
``*.cprnc.out`` files from failed comparisons are also copied to the
case directory.) These output files from ``cprnc`` contain a lot of
information. Most of what you need, though, can be determined via:

#. Examining the last 10 or so lines::

     $ tail -10 ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud.clm2.h0.0001-01-04-00000.nc.base.cprnc.out

     SUMMARY of cprnc:
      A total number of    487 fields were compared
               of which    340 had non-zero differences
                    and      0 had differences in fill patterns
                    and      0 had different dimension sizes
      A total number of      2 fields could not be analyzed
      A total number of      0 fields on file 1 were not found on file2.
       diff_test: the two files seem to be DIFFERENT

#. Looking for lines referencing RMS errors::

     $ grep RMS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default.20220926_095505_cqrqud.clm2.h0.0001-01-04-00000.nc.base.cprnc.out
      RMS ACTUAL_IMMOB                     3.4138E-11            NORMALIZED  1.1947E-04
      RMS AGNPP                            3.9135E-14            NORMALIZED  1.0836E-08
      RMS AR                               1.4793E-10            NORMALIZED  1.2585E-05
      RMS BAF_PEATF                        6.9713E-23            NORMALIZED  2.4249E-12
      RMS BGNPP                            3.2774E-14            NORMALIZED  9.1966E-09
      RMS BTRAN2                           2.5167E-07            NORMALIZED  2.7111E-07
      RMS BTRANMN                          2.5532E-07            NORMALIZED  6.0307E-07
      RMS CH4PROD                          1.3658E-15            NORMALIZED  7.5109E-08
      RMS CH4_SURF_AERE_SAT                6.6191E-12            NORMALIZED  1.6114E-04
      RMS CH4_SURF_AERE_UNSAT              1.2635E-22            NORMALIZED  5.1519E-13
      ...

Notice that this lists all fields that differ, along with their RMS and
normalized RMS differences.

Running multiple tests at once
==============================

It is often useful to run multiple tests at once
covering different test types, different compsets, different compilers,
etc. This is referred to as a `test suite`.

One way this can be done is by listing each test on the ``create_test``
command-line, as in::

  ./create_test SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default

However, a simpler approach is to create a file listing each of the
tests you want to run. You can then reuse this file to run the test
suite again later. To do this, create a text file containing your test
list, with one test per line::

  SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default
  ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default

Then run ``create_test`` with the ``-f`` (or ``--testfile``) option::

  ./create_test -f TESTFILE

(where ``TESTFILE`` gives the path to the file you just created).

The ``-r`` and ``--walltime`` options described in `Options to
create_test`_ are useful here, too. The ``-r`` option is particularly
helpful for putting all of the tests in the test suite together in their
own directory.

Checking the results of a test suite
====================================

You can check the individual ``TestStatus`` files in each test of your
test suite. However, an easier way to check the results of a test
suite is to run the ``cs.status.TESTID`` command that is put in your
test root (where ``TESTID`` is the unique id that was used for this
test suite or the test ID specified using the ``--test-id`` option to
``./create_test``).

As an example, if you run this ``cs.status.20220926_093725_gq431o`` command, you will see output like the following::

  20220926_093725_gq431o
    ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default (Overall: PASS) details:
      PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default CREATE_NEWCASE
      PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default XML
      PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default SETUP
      PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default SHAREDLIB_BUILD time=175
      PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default NLCOMP
      PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default MODEL_BUILD time=96
      PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default SUBMIT
      PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default RUN time=606
      PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default COMPARE_base_rest
      PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default BASELINE baseline_dir
      PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default TPUTCOMP
      PASS ERS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default MEMLEAK insuffiencient data for memleak test
    SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default (Overall: PASS) details:
      PASS SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default CREATE_NEWCASE
      PASS SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default XML
      PASS SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default SETUP
      PASS SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default SHAREDLIB_BUILD time=16
      PASS SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default NLCOMP
      PASS SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default MODEL_BUILD time=202
      PASS SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default SUBMIT
      PASS SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default RUN time=374
      PASS SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default BASELINE baseline_dir
      PASS SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default TPUTCOMP
      PASS SMS_D_Ld3.f19_f19_mtn14.I1850Clm50BgcCrop.betzy_intel.clm-default MEMLEAK insuffiencient data for memleak test

The output aggregates the results of all of the tests in the test suite, and
also gives an Overall PASS or FAIL result for each test. Since reviewing this
output manually can be tedious, so some options can help you filter the results:

* The ``-f`` / ``--fails-only`` option to ``cs.status`` allows you to see only test
  failures

* The ``--count-performance-fails`` option suppresses line-by-line output for performance
  comparisons that often fail due to machine variability; instead, this just gives a count
  of the number of non-PASS results (FAIL or PEND) at the bottom.

* The ``-c PHASE`` / ``-count-fails PHASE`` option can be used to suppress line-by-line
  output for the given phase (e.g., NLCOMP or BASELINE), instead just giving a count of
  the number of non-PASSes (FAILs or PENDs) for that phase. This is useful when you expect
  failures for some phases – often, phases related to baseline comparisons. This option
  can be specified multiple times.

So a typical use of ``cs.status.TESTID`` will look like this::

  ./cs.status.20220926_093725_gq431o -f --count-performance-fails

or, if you expect NLCOMP and BASELINE failures::

  ./cs.status.20220926_093725_gq431o -f --count-performance-fails -c NLCOMP -c BASELINE

Running a pre-defined test suite
================================

In addition to running your own individual tests or test suites, you can
also use ``create_test`` to run a pre-defined test suite. Moving forwards, NorESM
components will have a policy that a particular test suite must be run before
changes can be merged back to the main branch. These test suites are
defined in the following directories::


  prognostic components:
  $SRCROOT/blom/cime_config/testdefs/testlist_blom.xml
  $SRCROOT/cam/cime_config/testdefs/testlist_cam.xml
  $SRCROOT/cice/cime_config/testdefs/testlist_cice.xml
  $SRCROOT/cism/cime_config/testdefs/testlist_cism.xml
  $SRCROOT/clm/cime_config/testdefs/testlist_clm.xml
  $SRCROOT/rtm/cime_config/testdefs/testlist_rtm.xml
  $SRCROOT/mosart/cime_config/testdefs/testlist_mosart.xml
  $SRCROOT/ww3dev/cime_config/testdefs/testlist_ww3dev.xml

  data components:
  $SRCROOT/cdeps/dice/cime_config/testdefs/testlist_dice.xml
  $SRCROOT/cdeps/cime_config/testdefs/testlist_cdeps.xml
  $SRCROOT/cdeps/docn/cime_config/testdefs/testlist_docn.xml
  $SRCROOT/cdeps/drof/cime_config/testdefs/testlist_drof.xml
  $SRCROOT/cdeps/datm/cime_config/testdefs/testlist_datm.xml
  $SRCROOT/cdeps/dlnd/cime_config/testdefs/testlist_dlnd.xml
  $SRCROOT/cdeps/dwav/cime_config/testdefs/testlist_dwav.xml

  mediator:
  $SRCROOT/cmeps/cime_config/testdefs/testlist_drv.xml

To determine what pre-defined test suites are available and what tests
they contain, you can run ``$SRCROOT/cime/scripts/query_testlists`` (run
``./query_testlists -h`` for usage information). NorESM test categories will
have the suffix ``_noresm`` appending to the name.

Test suites are retrieved in ``create_test`` via three selection
attributes:

* The test category, specified with ``--xml-category`` (e.g.,
  ``--xml-category aux_clm_noresm``; see `Test categories`_ for other options)

* The machine, specified with ``--xml-machine`` (e.g., ``--xml-machine
  betzy``).

* The compiler, specified with ``--xml-compiler`` (e.g.,
  ``--xml-compiler intel``) (it is possible to leave this
  out and run all tests for this category/machine combination in a single test
  submission)

For example, to run the subset of the ``aux_cam_noresm`` test suite that
runs on betzy with the intel compiler, you can run::

  ./create_test --xml-category aux_cam_noresm --xml-machine betzy --xml-compiler intel

The ``-r`` option described in `options to create_test`_ is particularly
useful here for putting all of the tests in the test suite together in
their own directory.

``create_test`` uses multiple threads aggressively to speed up the
process of setting up and building all of the cases in your test
suite. On a shared system,this can turn you into a bad neighbor and
get you in trouble with your system administrator. If possible, you
should submit the ``create_test`` job to a compute node rather than
running it on the login node. Below are some helpful suggestions in
case you can only run large test suites on the login node:

* Run ``create_test`` with the unix ``nohup`` command in case you lose
  your connection.

* Run ``create_test`` with the unix ``nice`` command to give it a lower scheduling
  priority

* Specify a smaller number of parallel jobs via the ``--parallel-jobs`` option to
  ``create_test`` (the default is the number of cores available on a single node of the
  machine)

A typical ``create_test`` command for running a pre-defined test suite might then look like this::

  nohup nice -n 19 ./create_test --xml-category aux_cam_noresm --xml-machine betzy --xml-compiler intel -r /cluster/work/$USER/noresm/HELPFULLY_NAMED_SUBDIRECTORY --parallel-jobs 6

======================
 Baseline comparisons
======================

Overview of baseline comparisons
================================

In addition to verifying that various configurations run to completion
and that given variations are bit-for-bit with each other, baseline
comparisons are also needed in order to determine if your code changes
have resulted in unexpected numerical differences. Baseline
comparisons compare the output from the current version of the code
against the output from a previous version to determine if answers
have changed at all in the new version.

Depending on the changes you have made, you may expect:

1. No answer changes, e.g., if you are doing an answer-preserving code
   refactoring, or adding a new option but not changing anything with
   respect to existing options

2. Answers change only for certain configurations. As an example, if
   you change CTSM-crop code, but don't expect any answer changes for
   runs without the crop model

3. Answers change for most or all configurations, but only in a few
   diagnostic fields that don't feed back to the rest of the system

4. Answers change for most or all configurations

We recommend that when you have large changes to the model science you
should separate them into the following:

* Bit-for-bit modifications that can be tested against baselines - e.g.,
  renaming variables and moving code around, either before or after your
  science changes

* Answer-changing modifications; try to make these as small as possible
  (in terms of lines of code changed) so that they can be more easily
  reviewed for correctness.

You should then run the test suite separately on these two classes of
changes, ensuring that the parts of the change that you expect to be
bit-for-bit truly are bit-for-bit. The effort it takes to do this
separation pays off in the increased confidence that you haven't
introduced bugs.

Baseline comparisons step 1: Determine if you need to generate baselines
========================================================================

First, you need to determine what to use as a baseline. Generally this
is the version of the ``noresm`` branch from which you have branched,
or a previous, well-tested version of your branch.

If you're comparing against a version of the ``noresm`` branch and
have access to the main development machine(s) for the given
component, then baselines may already exist. (e.g., on betzy,
baselines go in ``/cluster/shared/noresm/noresm_baselines`` by
default). Otherwise, you'll need to generate your own baselines.

Baseline comparisons step 2: Generate baselines, if needed
==========================================================

If you need to generate baselines, you can do so by:

* Checking out the baseline code version

* Running ``create_test`` from the baseline code with these options:

  * ``--baseline-root /PATH/TO/BASELINE/ROOT``: Specifies the directory
    in which baselines should be placed. This is optional, but is needed
    if you don't have write access to the default baseline location on
    this machine.

  * ``--generate GENERATE_NAME``: Specifies a name for these
    baselines. Baselines for individual tests are placed under
    ``/PATH/TO/BASELINE/ROOT/GENERATE_NAME``. For example, this could be
    a tag name or an abbreviated git sha-1.

If you're generating baselines for a full test suite (as opposed to just
one or a few tests of your choosing), you may have to run multiple
``create_test`` invocations, possibly on different machines, in order to
generate a full set of baselines. Each component has its own policies
regarding the test suite that should be run for baseline comparisons.

After the test suite finishes, you can check the results as normal. Now,
though, you should see an extra line in the ``TestStatus`` files or the
output from ``cs.status``, labeled ``GENERATE``. A ``PASS`` status for
this phase indicates that files were successfully copied to the baseline
directory. You can confirm this by looking through
``/PATH/TO/BASELINE/ROOT/GENERATE_NAME``: There should be a directory
for each test in the test suite, containing history files, namelist
files, etc.

Baseline comparisons step 3: Compare against baselines
======================================================

Comparison against baselines is done similarly to generation (as
described in `Baseline comparisons step 2: Generate baselines, if
needed`_), but now you should use the ``--compare COMPARE_NAME`` flag to
``create_test``. You should still specify ``--baseline-root
/PATH/TO/BASELINE/ROOT``. You can optionally specify ``--generate
GENERATE_NAME``, but if you do, make sure that ``GENERATE_NAME`` differs
from ``COMPARE_NAME``! (In this case, ``create_test`` will compare
against some previous baselines while also generating new baselines for
later use.)

After the test suite finishes, you can check the results as normal. Now,
though, you should see an extra line in the ``TestStatus`` files or the
output from ``cs.status``, labeled ``BASELINE``. A ``PASS`` status for
this phase indicates that all history file types were bit-for-bit
identical to their counterparts in the given baseline directory. (For
each history file type - e.g., cpl hi, clm h0, clm h1, etc. -
comparisons are just done for the last history file of that type.)

Checking the results of failed baseline comparisons is similar to
checking the results of failed in-test comparisons. See `Finding more
details on failed comparisons`_ for details. However, whereas failed
in-test comparisons are put in a file named ``*.nc.base.cprnc.out``,
failed baseline comparisons are put in a file named ``*.nc.cprnc.out``
(without the ``base``; yes, this is a bit counter-intuitive).

If you expect differences in just a small number of tests or a small
number of diagnostic fields, you can confirm that the differences in the
baseline comparisons are just what you expected. The tool
``cime/tools/cprnc/summarize_cprnc_diffs`` facilitates this; run
``cime/tools/cprnc/summarize_cprnc_diffs -h`` for details.

In addition to the baseline comparisons of history files, comparisons
are also performed for:

* Namelists (``NLCOMP``). For details on a ``NLCOMP`` failure, see
  ``TestStatus.log``

* Model throughput (``TPUTCOMP``). However, note that system variability
  can cause this to fail even when there isn't a real problem.

* Model memory usage (``MEMCOMP``). However, note that system variability
  can cause this to fail even when there isn't a real problem.

* Model memory leak (``MEMLEAK``).


Generating or comparing baselines after the fact
================================================

It sometimes happens that you want to generate or compare baselines from
an already-run test suite. Some reasons this may happen are:

* You forgot to specify ``--generate`` or ``--compare`` when you ran the
  test suite.

* You wanted to wait to see if the test suite was successful before
  generating baselines.

* You ran baseline comparisons against one set of baselines, but now
  want to run comparisons against a different set of baselines.

There are two complementary tools for doing this:

* ``cime/CIME/Tools/bless_test_results``: after-the-fact baseline
  generation

* ``cime/CIME/Tools/compare_test_results``: after-the-fact baseline
  comparison

A typical usage of ``compare_test_results`` for NorESM would look like this::

  ./compare_test_results -b BASELINE_NAME --baseline-root BASELINE_ROOT -r TEST_ROOT -t TEST_ID

where:

* ``-b BASELINE_NAME`` (or ``--baseline-root BASELINE_NAME``)
  corresponds to ``--compare COMPARE_NAME`` for ``create_test``

* ``--baseline-root`` corresponds to the same argument for ``create_test``

* ``-r`` (or ``--test-root``) corresponds to the same argument for
  ``create_test``

* ``-t TEST_ID`` (or ``--test-id TEST_ID``) is either the test-id you
  specified with the ``-t`` (or ``--test-id``) argument to
  ``create_test``, or the auto-generated test-id that was appended to
  each of your tests (a date and time stamp followed by a string of
  random characters)

==============
 General tips
==============

Here are some general tips for running test suites:

* It is very important to **not** change anything in your $SRCROOT
  directory (i.e., your git clone) once you start the test suite,
  until all tests in the test suite finish running.

* On betzy, set the ``PROJECT`` environment variable in your shell startup file, or use
  some other mechanism to specify a default project / account code to cime. This way, you
  won't need to add the ``--project`` argument every time you run ``create_test`` or ``create_newcase``.
