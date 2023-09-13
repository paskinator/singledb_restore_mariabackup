# singledb_restore_mariabackup

#### This script takes the process I came up with and automates it so you just provide the full backup location, Database name and the data directory location
#### This is for Schemas/databases that are only using InnoDB, trying to use this with MyISAM for example will break the process
[Single DB restore from full backup](https://mariadb.com/kb/en/individual-database-restores-with-mariabackup-from-full-backup/)

### Variables to know about

```bash
backup_dir="$1"
dbname="$2"
nodatafile="$3"
```

### How to run:

**bash singledb.bash path/to/full/backup database_name location/to/nodata/sql/file**

#### For example:
```bash
bash singledb.bash /media/backups/2023-09-12/fullbackup/  /home/jeffery/restore/nodata.sql
```

#### Things to know
Once ran this script will read the grabresults.sql file in the same directory you run this so make sure both files are in the directory. The script will output a file called grabresults2.sql which has the database name in a SQL variable. The Script will then connect to MariaDB using the options at the top of the script where you can change the password and add other options.

```bash
declare -a mariadboptions=(
		"-u user"
		"-ppassword"
		)
```
MariaDB will then output four files that it will run in the correct order.

### Cronjob for no-data SQL file
To make this proess work we need the table structure, we need to run the following command, add username (-u backupuser) and password(-ppasswordforaccount) parameters if needed

```bash
mariadb-dump --no-data fire > nodata.sql
```
Crontab config for 2am, run as many times as you need if data structure changes alot in your server, as this will not take long to run you make want to do this inline with your Mariabackup schedule
```bash
0 2 * * * /bin/bash /path/to/mariabackup.bash
```
