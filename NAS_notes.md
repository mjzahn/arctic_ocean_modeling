## Command line notes for working on NAS

**Transferring files within the enclave:**<br>
```
pfe21% shiftc /u/username/file1 lfe:data/dir2
```

**To download files from nobackup folder to local machine:**
```
your_local_system% scp mzahn1@pfe.nas.nasa.gov:/home1/mzahn1/nobackup/[filename] .
```
If that doesn't work, you can try:
```
your_local_system% scp -oProxyCommand='ssh sfe6.nas.nasa.gov 
                 ssh-proxy %h' pfe21.nas.nasa.gov:/pfe20:/nobackup/mzahn1/[filename] .
```

**To compress a folder into a tar.gz file**<br>
```
tar -czvf output_filename.tar.gz /path/to/folder
```
- c: Creates a new archive.
- z: Compresses the archive using gzip.
- v: Verbose mode, shows the progress in the terminal.
- f: Specifies the name of the output file.
