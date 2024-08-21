## Catalogue of command line notes when working on Pleiades:

See available quota on nobackup storage:<br>
`pfe21% lfs quota -h -u [username] /nobackup/[username]`

To see which nobackup filesystem is assigned to me:<br>
`pfe21% ls -l /nobackup/your_username`

To check the status of a job running:<br>
`qstat -u [username]`

To cancel a job that has been submitted:<br>
`qdel [JobID]` where `JobID` can be viewed when checking status of the job or sent in email when job was originally executed.

To submit a job:<br>
`qsub [job_script]`

Job request:<br>
This example requests 55 Broadwell nodes on Pleiades each with 28 CPUs.<br>
`#PBS -l select=55:ncpus=28:model=bro`

Requesting Different ncpus in a Multi-Node Job:<br>
The number of CPUs (`ncpus`) used in each node can be different. This can be useful for jobs that require more memory for some processes and less for others. To do this, you can request resources in "chunks" for a job with varying CPUs in each node.<br>

This example requests two resource chunks: (1) one CPU in one Haswell node and (2) eight CPUs in three other Haswell nodes.<br>

`#PBS -l select=1:ncpus=1:model=has+3:ncpus=8:model=has`<br>
Chunks:<br>
`#PBS -l select=1:ncpus=1:model=has` + `3:ncpus=8:model=has`
