Regional Nudging
=============================================

.. warning::
  * Again, care must be taken when using the results of this model version (pre-release). 
  * Regional Nudging presented below only works in `NorESM2.2 <https://noresm22-nudging-regional.readthedocs.io/en/latest/Install-NorESM2.2.html>`_ (i.e., not in earlier versions). 
  * Also, only `historical AMIP experiment <https://noresm22-nudging-regional.readthedocs.io/en/latest/AMIP-configuration.html>`_ on Betzy/Sigma2 has been tested.

---------------------

**Implementation**

Regional nudging has been implemented in the latest version of the CESM (CESM2.2, CAM6.3) model, thus the details of the implementation can be found  `here <https://ncar.github.io/CAM/doc/build/html/users_guide/physics-modifications-via-the-namelist.html#nudging>`_. Below we show an example of such implementation in NorESM2.2, which is based on the CESM2.2.

Once you have created a case folder ``~/<noresm-base>/cases/<case-name>`` (e.g., see `here <https://noresm22-nudging-regional.readthedocs.io/en/latest/AMIP-configuration.html>`_), you can specify nudging (global or regional) in ``user_nl_cam`` within that folder. The variables are described `here <https://ncar.github.io/CAM/doc/build/html/users_guide/physics-modifications-via-the-namelist.html#nudging>`_ in more detail, but we provide some information here as well (see also example below):

* ``Nudge_Path``, ``Nudge_File_Template`` specify path & file-name structure of target data (e.g. ERA5 - for more details on available nudging target data below).

* ``Nudge_Force_Opt`` specifies whether we nudge to the next target data (=0) or linearly interpolate between consecutive target data points (=1).

* ``Nudge_TimeScale_Opt`` specifies whether nudging timescale is constant (=0) or gets stronger closer to target values (=1).

* ``Nudge_Times_Per_Day`` specifies the frequency of target data per day (=4 for 6-hourly target data)
    
* ``Model_Times_Per_Day`` specifies the number of model timesteps per day we wish to nudge (for a model with a 30-minute timestep - like NorESM2.2-AMIP - set to 48 if you wish to nudge *every* timestep).

* ``Nudge_Uprof`` (and equivalents for V, T, Q, PS) states what kind of nudging in, e.g., U we want (=0 no nudging, =1 global nudging, =2 regional nudging):

  * For global nudging of U,V specify ``Nudge_Uprof = Nudge_Vprof = 1``, ``Nudge_PSprof = Nudge_Tprof = Nudge_Qprof = 0``.

  * For regional nudging of U,V specify ``Nudge_Uprof = Nudge_Vprof = 2``, ``Nudge_PSprof = Nudge_Tprof = Nudge_Qprof = 0``.
  
* ``Nudge_Ucoef`` (and equivalents for V, T, Q, PS) specifies the strength of nudging for the specific variable, e.g. U (value between 0 and 1).

* ``Nudge_Beg_Year``, ``Nudge_Beg_Month``, ``Nudge_Beg_Day``, ``Nudge_End_Year``, ``Nudge_End_Month``, ``Nudge_End_Day`` specify the start (**Beg**) and end (**End**) date (Year, Month, Day) of nudging. See example below.

* ``Nudge_Hwin_lat0``, ``Nudge_Hwin_latWidth``, ``Nudge_Hwin_latDelta`` specify the size of the regional nudging window in latitudinal direction:
  
  * ``Nudge_Hwin_lat0`` is central latitude of nudging window.
  
  * ``Nudge_Hwin_latWidth`` is the width of the nudging window in latitude direction.
  
  * ``Nudge_Hwin_latDelta`` specifies over how many latitudes we taper-off the data (typically applied to +/- latDelta around the edge of the window).
  
* ``Nudge_Hwin_lon0``, ``Nudge_Hwin_lonWidth``, ``Nudge_Hwin_lonDelta`` specify the size of the regional nudging window in longitudinal direction (equivalent to the latitudinal window above). 
 
* ``Nudge_Vwin_Hindex``, ``Nudge_Vwin_Hdelta``, ``Nudge_Vwin_Lindex``, ``Nudge_Vwin_Ldelta`` specify the size of the "regional" nudging window in vertical direction:

  * ``Nudge_Vwin_Hindex`` & ``Nudge_Vwin_Lindex`` specify the window in terms of model indices: High **Hindex** (model surface) and Low **Lindex** (model top) transition levels. For nudging over all model levels set Lindex=0 and Hindex=NLEV+1 (in NorESM2.2-AMIP, Hindex = 33).
  
  * ``Nudge_Vwin_Hdelta`` & ``Nudge_Vwin_Ldelta`` specify over how many levels we taper off the data in terms of number of model level indices. For no tapering-off set the transition lengths to 0.001, otherwise set it to a value larger than 0.
    
* ``Nudge_Hwin_Invert``, ``Nudge_Vwin_Invert`` is a logical specifying whether we nudge within the nudging window (.false.) or outside the nudging window (.true.) - separately for horizontal (**Hwin**) and vertical (**Vwin**) window.
 
----------------

**Target Data**

At the moment there is the following capability for nudging in NorESM2.2:

* Anomaly nudging to `historical AMIP experiment <https://noresm22-nudging-regional.readthedocs.io/en/latest/AMIP-configuration.html>`_ climatology with ERA5 anomalies over the period 1979-2016. Data is available for PS, U, V variables only. Set the following path & file-name in ``user_nl_cam``:

  ``Nudge_Path = '/cluster/projects/nn9039k/CAM_Nudging/met_data/era5_PSUV_f09L32_6hrAnomalySingleRecord/'``
  
  ``Nudge_File_Template= 'era5-ano_PSUV_f09L32_6hr_r0_%y%m%d%s.nc'``
  
* Full nudging to ERA5 data (i.e., climatology + anomalies) over the period 1979-2016. Data is available for PS, U, V variables only. Set the following path & file-name in ``user_nl_cam``:

  ``Nudge_Path = '/cluster/projects/nn9039k/CAM_Nudging/met_data/era5_PSUV_f09L32_6hrFullFieldSingleRecord/'``
  
  ``Nudge_File_Template= 'era5_PSUV_f09L32_6hr_r0_%y%m%d%s.nc'``

* Full nudging to ERA-Interim data (i.e., climatology + anomalies) over the period 2001-2015. Data is available for PS, U, V, T, Q variables. Set the following path & file-name in ``user_nl_cam``:

  ``Nudge_Path = '/cluster/projects/nn9039k/CAM_Nudging/met_data/ERAI_f09L32_vn2_2/'``
  
  ``Nudge_File_Template= '%y-%m-%d-%s.nc'``
  
For now, we have only tested U & V nudging, while PS nudging is also available in ERA5 and T,Q nudging is available in ERA-Interim. We expect to increase the number of variables available for nudging with ERA5 (to, e.g., T, Q) at a later time. 

If you do not have access to the data specified above please contact us (lina.boljka@uib.no).

--------------------

**Example**

Here is an example of the nudging part of the namelist script (``user_nl_cam``) for regional nudging over the North Pacific.
 
.. admonition:: user_nl_cam

  &nudging_nl
  
    Nudge_Model = .true. ! (set to .false. for no nudging)
    
    Nudge_Path = '/cluster/projects/nn9039k/CAM_Nudging/met_data/era5_PSUV_f09L32_6hrAnomalySingleRecord/'
    
    Nudge_File_Template= 'era5-ano_PSUV_f09L32_6hr_r0_%y%m%d%s.nc'
    
    Nudge_Force_Opt = 1 ! (=0 for target at next future time ; =1 for linear interpolation between 2 target datapoints)
    
    Nudge_TimeScale_Opt = 0 ! (=0 for weak nudging [constant] & =1 for strong nudging [near target points])
    
    Nudge_Times_Per_Day= 4 ! (for 6-hourly target data for nudging - e.g. ERA5)
    
    Model_Times_Per_Day= 48 ! (for a model with a 30-minute timestep - like NorESM2.2-AMIP)
    
    Nudge_Uprof = 2 ! (=0 for nudging off ; =1 for global nudging ; =2 for regional nudging)
    
    Nudge_Ucoef =1.00 ! (= anything between 0 and 1)
    
    Nudge_Vprof = 2
    
    Nudge_Vcoef =1.00
    
    Nudge_Tprof =0
    
    Nudge_Tcoef =1.00
    
    Nudge_Qprof =0
    
    Nudge_Qcoef =1.00
    
    Nudge_PSprof =0
    
    Nudge_PScoef =0.00
    
    Nudge_Beg_Year = 1979
    
    Nudge_Beg_Month= 1
    
    Nudge_Beg_Day = 1
    
    Nudge_End_Year = 2013
    
    Nudge_End_Month= 12
    
    Nudge_End_Day = 31
    
    Nudge_Hwin_lat0 = 45. 
    
    Nudge_Hwin_latWidth= 45. ! (set to 999. for full longitudinal circle)
    
    Nudge_Hwin_latDelta= 5.0 
    
    Nudge_Hwin_lon0 = 180. 
    
    Nudge_Hwin_lonWidth= 100. ! (set to 999. for full latitudinal circle)
    
    Nudge_Hwin_lonDelta= 5. 
    
    Nudge_Hwin_Invert =.false. ! (set to .true. for inverted nudging window)
    
    Nudge_Vwin_Hindex = 33. 
    
    Nudge_Vwin_Hdelta = 0.001 ! (const vertical window ; for non-const. set to a larger value, i.e., number of levels over which it tapers off)
    
    Nudge_Vwin_Lindex = 13. ! (=0.  full vertical extent ; =13.  troposphere only & taper off in lower stratosphere [lev 13 ~150 hPa; lev 15 ~200 hPa; lev 11 ~100 hPa] ; =32.  surface layer only)
    
    Nudge_Vwin_Ldelta = 2. ! (=2. taper-off over +/- 2 levels ; =0.001 const. vertical window)
    
    Nudge_Vwin_Invert =.false. ! (set to .true. for inverted nudging window)
    
  /

----------------

**Visualisation of the Nudging Window**

To visualise the nudging window used (e.g., prior to implementing it in the model) do the following:

1)  Navigate to Cam Tools folder with Nudging Window scripts within `NorESM2.2 <https://noresm22-nudging-regional.readthedocs.io/en/latest/Install-NorESM2.2.html>`_ dierctory <noresm-base>:

  ``cd ~/<noresm-base>/components/cam/tools/nudging/Lookat_NudgeWindow/``

2) Copy the ``user_nl_cam`` script from ``~/<noresm-base>/cases/<case-name>`` directory to the ``Lookat_NudgeWindow`` folder and read the ``README`` file there. Make sure that you are in an environment where ``ncl`` is installed before running the following commands:

  ``>> WRAPIT Read_Namelist.f90 Read_Namelist.stub``
  
  ``>> ncl Lookat_NudgeWindow.ncl``
  
  This should show figures with a X11 display, but if you set, e.g., ``wks = gsn_open_wks("png","Wcoef")`` in ``Lookat_NudgeWindow.ncl`` it will save the file as a pdf instead.

The above example of ``user_nl_cam`` yields a nudging window in the horizontal and in the vertical as shown below.

.. image:: Wcoef_new.png
  :width: 800

.. image:: Wcoef_new2.png
  :width: 350

----------------

**Topography data**

Also, topography data from a reanalysis can be specified in ``user_nl_cam``, although be aware that ERA5 topography may be very different from model topography and thus care must be taken!

.. admonition:: user_nl_cam

  &cam_initfiles_nl
  
    use_topo_file=.true.
    
    bnd_topo = '/cluster/shared/noresm/inputdata/noresm-only/inputForNudging/ERA_f09f09_32L_days/ERA_bnd_topo_noresm2_20191023.nc'
    
  /

At the moment only the ERA-Interim topography data is available (as specified in the example above), i.e., we have not performed any tests with ERA5 topography.

