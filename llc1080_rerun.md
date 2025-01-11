# Notes for the LLC1080 model re-run

1. Sign into Pleiades
```
ssh mzahn1@sfe
ssh pfe
```

2. Naviagte to MITgcm directory for the N1_1080 model. In the `namelist` directory, modify the `data.diagnostics` file.

In the `data.diagnostics` file, if I want to save every week, the frequency  = 604800.0 (secs/7 days) for all data variables and frequency  = -604800.0 for the snapshots.
  
4. In the `namelist` directory, modify the `data` file for the pickup file to start at, and copy required pickup files to the `run` directory. To determine which pickup to start from, figure out the iter num for the date you want to save. 
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

The closest pickup files for this example is `pickup.0005850000.meta` and `pickup.0005850000.data`. This also includes the sea ice pickups `pickup_seaice.0005850000.meta`. Copy these pickup files to the run directory.

`nTimeSteps` is equal to [(sec/day * number of days)/(sec per timestep)]. For example, if I wanted 3 weeks, nTimeSteps = (86400*21)/120) = 15120.

For this example, the pickup 5850000 corresponds to "2014-03-31," so we need 25 days to reach timestep "2014-04-24." nTimeSteps = (86400*25)/120) = 18000.

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
nTimeSteps = 18000,
```

To test the setup and see how long it takes to run, just save 2 days: 
```
# Under Time stepping parameters
 &PARM03
nIter0 = 5850000,
nTimeSteps = 1440,
```

Copy pickups to run directory
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

It's a good idea to do a test with just a few days to make sure you know how long it will take. To run on devel, the job script `job_1080_devel` file should show:

```
#!/bin/csh
###PBS -l select=1:model=bro+32:ncpus=24:model=bro
#PBS -l select=40:ncpus=40:model=sky_ele
###PBS -l select=55:ncpus=28:model=bro
#PBS -l walltime=2:00:00
#PBS -q devel
###PBS -q normal
#PBS -j oe
#PBS -m abe
#PBS -W group_list=s2546
#PBS -M marie.j.zahn@jpl.nasa.gov

module purge
module load comp-intel mpi-hpe hdf4/4.2.12 hdf5/1.8.18_mpt netcdf/4.4.1.1_mpt
#module load comp-intel mpi-sgi hdf4 hdf5/1.8.18_mpt netcdf/4.4.1.1_mpt
module list

umask 027
cd $PBS_O_WORKDIR
limit stacksize unlimited
#./modpickup
mpiexec -np 1524 ./mitgcmuv
```

Once you see how long it takes to run a few days, you can estimate the wall time for the full job.

To run the full model the job script will be:

```
#!/bin/csh
###PBS -l select=1:model=bro+32:ncpus=24:model=bro
#PBS -l select=40:ncpus=40:model=sky_ele
###PBS -l select=55:ncpus=28:model=bro
#PBS -l walltime=8:00:00
###PBS -q devel
#PBS -q normal
#PBS -j oe
#PBS -m abe
#PBS -W group_list=s2546
#PBS -M marie.j.zahn@jpl.nasa.gov

module purge
module load comp-intel mpi-hpe hdf4/4.2.12 hdf5/1.8.18_mpt netcdf/4.4.1.1_mpt
#module load comp-intel mpi-sgi hdf4 hdf5/1.8.18_mpt netcdf/4.4.1.1_mpt
module list

umask 027
cd $PBS_O_WORKDIR
limit stacksize unlimited
#./modpickup
mpiexec -np 1524 ./mitgcmuv
```

9. Now it is time to run the model. To submit a job you will run:

```
# qsub script_name
qsub job_1080_devel
```

To check the 
```
# qstat -u user
qstat -u mzahn1
```

To cancel a job
```
# qdel job_id
qdel 21372294.pbspl1
```

Once the job is finished, you can open the job file (e.g., `cat job_1080_devel.o21372294`) and it should read "NORMAL END" a bunch of times.
