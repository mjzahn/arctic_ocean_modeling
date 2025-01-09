# Notes for the LLC1080 model re-run

1. Sign into Pleiades
```
ssh mzahn1@sfe
ssh pfe
```

2. Naviagte to MITgcm directory for the N1_1080 model. In the `namelist` directory, modify the `data.diagnostics` file.

In the `data.diagnostics` file, if I want to save every week, the frequency  = 604800.0 (secs/7 days) for all data variables and frequency  = -604800.0 for the snapshots.
  
4. In the `namelist` directory, modify the `data` file for the pickup file to start at. To determine which pickup to start from, figure out the iter num for the date you want to save. 
For example, if I want "2014-04-24," this corresponds to iter num 5867280. You can use this function to get the iter number from a timestep:

```
def iter_from_timestamp(timestamp_str):
    """
    takes the model timestamp and generates the model iteration number
    """
    
    ## Start time of the model is 5790000 (22.0319 years after 1992-01-01)
    ## there are 120 seconds for each iteration and 86400 seconds per day
    ## take the iteration number, convert to seconds, and calculate number of days since start of model
    
    date = np.datetime64(timestamp_str)
    
    model_start_time = np.datetime64("1992-01-01") # data.cal start time is 1992-01-01
    timestamp = date - model_start_time
    
    iter_num = int(np.array(timestamp, dtype='int')*86400/120)

    return iter_num
```

```
iter_from_timestamp("2014-04-24")
# results in: 5867280
```
Now look for the closest pickup file before this iter num and calculate how many timesteps are needed to get to the date I want.

The closest pickup files for this example is `pickup.0005850000.meta` and `pickup.0005850000.data`. This also includes the sea ice pickups `pickup_seaice.0005850000.meta`

`nTimeSteps` is equal to [(sec/day * number of days)/(sec per timestep)]. For example, if I wanted 3 weeks, nTimeSteps = (86400*21)/120) = 15120.

For this example, we need 144 timesteps to reach timestep 5867280. nTimeSteps = (5867280 - 5850000)/120 = 144.

```
# Under Time stepping parameters
 &PARM03
nIter0 = 7002000,
nTimeSteps = 2880,
```

```
# Under Time stepping parameters
 &PARM03
nIter0 = 5850000,
nTimeSteps = 144,
```

5. Copy pickups to run directory
```
lfe% shiftc /u/[username]/[path] pfe:/nobackup/mzahn1/sassie-ecco/[path]
```   

6. Check to make sure output folders exist in 'diags/'. For example:
```
# create directories for only the diagnostic variables (not snapshots)
mkdir ocean_state_2D_day_mean ocean_state_3D_day_mean seaice_state_day_mean tr_adv_r_day_mean tr_adv_x_3D_day_mean tr_adv_x_2D_day_mean tr_diff_r_day_mean vert_mass_day_mean ocean_vel_day_mean vol_adv_day_mean EXF_day_mean oce_flux_day_mean phi_3D_day_mean seaice_flux_day_mean seaice_vel_day_mean KPP_mix_day_mean KPP_hbl_day_mean 

```

7. Open the job file `job_1080_devel`.
Decide whether this job will be run on the devel or normal queue. The devel queue has a max wall time of 2 hours.
8. 
9. 
