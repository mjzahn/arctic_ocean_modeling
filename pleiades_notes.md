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
