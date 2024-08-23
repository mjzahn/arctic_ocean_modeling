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

**To see what nobackup filesystem (Lustre Filesystem) you were assigned to:** <br>
```
pfe21% ls -l /nobackup/your_username
```

**To compress a folder into a tar.gz file**<br>
```
tar -czvf output_filename.tar.gz /path/to/folder
```
- c: Creates a new archive.
- z: Compresses the archive using gzip.
- v: Verbose mode, shows the progress in the terminal.
- f: Specifies the name of the output file.

**Use shell script to take subdirectories of ~/diags/*, compress each diagnostic, and save to output dir
```
sh compress_model_granules.sh ~/nobackup/path_to/run/diags ~/nobackup/path_to/output_gz
```

**To see your nobackup storage quota:** <br>
```
nbquota
```

**Transferring files within the enclave:** <br>
The Lustre (/nobackup) filesystems are mounted on the LFEs. To transfer files between your /nobackup filesystem and your Lou home filesystem, use the local file transfer commands shiftc, cp, or mcp. Two examples:
```
# transfer from one Lou directory to another
pfe21% shiftc /u/username/file1 lfe:data/dir2
# transfer from Lou to pfe
pfe21% shiftc /u/username/file1 pfe:/nobackup/username/data/dir2
```

**To transfer files from PFE nobackup scratch directory to s3 bucket**<br>
First set up credentials to access private s3 buckets:
```
# if you don't have AWS installed
# Install the AWS ‘command line interface’ CLI package on your local machine
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /

# then load AWS module
module load scicon/aws_cli_tools

# to check to see that it loaded
aws cli
```
```
# to access private s3 buckets you will need to update your credentials in the aws credentials file
# to view contents of credentials file
cd ~/.aws
cat credentials

# to set up credentials with keys
aws configure set aws_access_key_id [access_key_id] --profile [profile_name]
aws configure set aws_secret_access_key [aws_secret_access_key] --profile [profile_name]
aws configure set output json --profile [profile_name]
aws configure set region us-west-2 --profile [profile_name] # specify correct region for the bucket
aws s3 ls s3://bucket_name --profile [profile_name]
```

To set up credentials through JPL, follow JPL instructions to download script to generate credentials [HERE](https://cloudwiki.jpl.nasa.gov/display/cloudcomputing/Installing+and+Running+the+Access+Key+Generation+Script+on+macOS)
```
# then run the following command to generate credentials
# (you will need to provide your JPL username, password, and PIN + PASSCODE):
aws-login.darwin.amd64 -r us-west-2 -pub -l

# you can also set up a shell script that renews credentials:
(sassie) [mzahn1@pfe20]/nobackup/mzahn1/scripts% cat renew_aws_credentials.sh 
#!/usr/bin/zsh
conda activate sassie
cd /home1/mzahn1 
python /nobackup/mzahn1/acg/Access-Key-Generation-master/aws-login.py -l --pub -r us-west-2
```
*This will all need to be set up on PFE front end when transferring files between nobackup filesystems and the cloud*

To transfer files from nobackup to s3 bucket:
```
aws s3 sync /nobackup/username/dir1 s3://bucket_name/dir/ --profile [profile_name]
```

