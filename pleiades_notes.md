## Catalogue of command line notes when working on Pleiades:

See available quota on nobackup storage:<br>
```
pfe21% lfs quota -h -u [username] /nobackup/[username]`
```

To see which nobackup filesystem is assigned to me:<br>
```
pfe21% ls -l /nobackup/your_username
```

To check the status of a job running:<br>
```
qstat -u [username]
```

To cancel a job that has been submitted:<br>
```
qdel [JobID] 
```
where `JobID` can be viewed when checking status of the job or sent in email when job was originally executed.

To submit a job:<br>
```
qsub [job_script]
```

Job request:<br>

To calculate how many nodes your job needs:
```
ceil(number of cpus/number of cpus per node)
```
The number of CPUs comes from the total number of processors identified in the SIZE.h file (nPx*nPy) for MITgcm. For the SASSIE ECCO LLC1080 model, 
```
nPx = 2565-1041 # (ocean cells-land cells) this equals 1524 CPUs
nPy =   1
```
With the exch2 package, all processors are in the x-direction. The five SASSIE model tiles are broken up into 40x40 cells for the processors. Four of the model tiles are 17x27 = 459 processors each (1836 total) and the 1 Arctic tile is 27x27 processors (729 total). 1836+729 = 2565. Because a solid fraction of the domain is land, these tiles are subtracted. Mike computed 1041 tiles are "blank" (no ocean). These are listed in data.exch2.


This example requests 55 Broadwell nodes on Pleiades each with 28 CPUs.<br>
```
#PBS -l select=55:ncpus=28:model=bro
```
It is common to use 2 Broadwell processors with 14 CPUs each as one node, resulting in 28 CPUs per node.

For the SASSIE ECCO test run, this job request was used:
```
#PBS -l select=40:ncpus=40:model=sky_ele
```
On Electra with 40 nodes, each using 40 CPUs (total 1600 CPUs)

Requesting Different ncpus in a Multi-Node Job:<br>
The number of CPUs (`ncpus`) used in each node can be different. This can be useful for jobs that require more memory for some processes and less for others. To do this, you can request resources in "chunks" for a job with varying CPUs in each node.<br>

This example requests two resource chunks: (1) one CPU in one Haswell node and (2) eight CPUs in three other Haswell nodes.<br>

```
#PBS -l select=1:ncpus=1:model=has+3:ncpus=8:model=has
```
<br>
Chunks:<br>

`#PBS -l select=1:ncpus=1:model=has` + `3:ncpus=8:model=has`
