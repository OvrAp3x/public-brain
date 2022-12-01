---
dg-publish: true
tags:
- public
- proxmox
- backup
- linux
---
# About

This page contains an excerpt for the usage of the proxmox-backup-client CLI utility.  
This reference is kept here for the sake of quick access and completeness.  
The source reference is: [https://pbs.proxmox.com/docs/backup-client.html](https://pbs.proxmox.com/docs/backup-client.html "https://pbs.proxmox.com/docs/backup-client.html")

# Our details for quick reference

A list of datastores can be found here [Sources](https://neonomics.atlassian.net/wiki/spaces/IT/pages/2161278998) in the “**Target with datastore”** column  
Credentials are stored in the password file. (Admin user for proxmox backup server)

# Backup Client Usage

The command line client is called **proxmox-backup-client**.

## Repository Locations

The client uses the following notation to specify a datastore repository on the backup server.

> [[username@]server[:port]:]datastore

The default value for `username` is `root@pam`. If no server is specified, the default is the local host (`localhost`).

You can specify a port if your backup server is only reachable on a different port (e.g. with NAT and port forwarding).

Note that if the server is an IPv6 address, you have to write it with square brackets (e.g. [fe80::01]).

You can pass the repository with the `--repository` command line option, or by setting the `PBS_REPOSITORY` environment variable.

Here some examples of valid repositories and the real values

Example

User

Host:Port

Datastore

mydatastore

`root@pam`

localhost:8007

mydatastore

myhostname:mydatastore

`root@pam`

myhostname:8007

mydatastore

user@pbs@myhostname:mydatastore

`user@pbs`

myhostname:8007

mydatastore

192.168.55.55:1234:mydatastore

`root@pam`

192.168.55.55:1234

mydatastore

[ff80::51]:mydatastore

`root@pam`

[ff80::51]:8007

mydatastore

[ff80::51]:1234:mydatastore

`root@pam`

[ff80::51]:1234

mydatastore

## Environment Variables

`PBS_REPOSITORY`The default backup repository.`PBS_PASSWORD`When set, this value is used for the password required for the backup server.`PBS_ENCRYPTION_PASSWORD`When set, this value is used to access the secret encryption key (if protected by password).`PBS_FINGERPRINT` When set, this value is used to verify the servercertificate (only used if the system CA certificates cannot validate the certificate).

## Output Format

Most commands support the `--output-format` parameter. It accepts the following values:

`text`:

Text format (default). Structured data is rendered as a table.

`json`:

JSON (single line).

`json-pretty`:

JSON (multiple lines, nicely formatted).

Please use the following environment variables to modify output behavior:

`PROXMOX_OUTPUT_FORMAT`Defines the default output format.`PROXMOX_OUTPUT_NO_BORDER`If set (to any value), do not render table borders.`PROXMOX_OUTPUT_NO_HEADER`If set (to any value), do not render table headers.

Note

The `text` format is designed to be human readable, and not meant to be parsed by automation tools. Please use the `json` format if you need to process the output.

## Creating Backups

This section explains how to create a backup from within the machine. This can be a physical host, a virtual machine, or a container. Such backups may contain file and image archives. There are no restrictions in this case.

Note

If you want to backup virtual machines or containers on Proxmox VE, see [Proxmox VE Integration](https://pbs.proxmox.com/docs/pve-integration.html#pve-integration "https://pbs.proxmox.com/docs/pve-integration.html#pve-integration").

For the following example you need to have a backup server set up, working credentials and need to know the repository name. In the following examples we use `backup-server:store1`.

`1# proxmox-backup-client backup root.pxar:/ --repository backup-server:store1 2Starting backup: host/elsa/2019-12-03T09:35:01Z 3Client name: elsa 4skip mount point: "/boot/efi" 5skip mount point: "/dev" 6skip mount point: "/run" 7skip mount point: "/sys" 8Uploaded 12129 chunks in 87 seconds (564 MB/s). 9End Time: 2019-12-03T10:36:29+01:00 10`

This will prompt you for a password and then uploads a file archive named `root.pxar` containing all the files in the `/` directory.

Caution

Please note that the proxmox-backup-client does not automatically include mount points. Instead, you will see a short `skip mount point` notice for each of them. The idea is to create a separate file archive for each mounted disk. You can explicitly include them using the `--include-dev` option (i.e. `--include-dev /boot/efi`). You can use this option multiple times for each mount point that should be included.

The `--repository` option can get quite long and is used by all commands. You can avoid having to enter this value by setting the environment variable `PBS_REPOSITORY`. Note that if you would like this to remain set over multiple sessions, you should instead add the below line to your `.bashrc` file.

`1# export PBS_REPOSITORY=backup-server:store1 2`

After this you can execute all commands without specifying the `--repository` option.

One single backup is allowed to contain more than one archive. For example, if you want to backup two disks mounted at `/mnt/disk1` and `/mnt/disk2`:

`1# proxmox-backup-client backup disk1.pxar:/mnt/disk1 disk2.pxar:/mnt/disk2 2`

This creates a backup of both disks.

The backup command takes a list of backup specifications, which include the archive name on the server, the type of the archive, and the archive source at the client. The format is:

> <archive-name>.<type>:<source-path>

Common types are `.pxar` for file archives, and `.img` for block device images. To create a backup of a block device run the following command:

`1# proxmox-backup-client backup mydata.img:/dev/mylvm/mydata 2`

### Excluding files/folders from a backup

Sometimes it is desired to exclude certain files or folders from a backup archive. To tell the Proxmox Backup client when and how to ignore files and directories, place a text file called `.pxarexclude` in the filesystem hierarchy. Whenever the backup client encounters such a file in a directory, it interprets each line as glob match patterns for files and directories that are to be excluded from the backup.

The file must contain a single glob pattern per line. Empty lines are ignored. The same is true for lines starting with `#`, which indicates a comment. A `!` at the beginning of a line reverses the glob match pattern from an exclusion to an explicit inclusion. This makes it possible to exclude all entries in a directory except for a few single files/subdirectories. Lines ending in `/` match only on directories. The directory containing the `.pxarexclude` file is considered to be the root of the given patterns. It is only possible to match files in this directory and its subdirectories.

`\` is used to escape special glob characters. `?` matches any single character. `*` matches any character, including an empty string. `**` is used to match subdirectories. It can be used to, for example, exclude all files ending in `.tmp` within the directory or subdirectories with the following pattern `**/*.tmp`. `[...]` matches a single character from any of the provided characters within the brackets. `[!...]` does the complementary and matches any single character not contained within the brackets. It is also possible to specify ranges with two characters separated by `-`. For example, `[a-z]` matches any lowercase alphabetic character and `[0-9]` matches any one single digit.

The order of the glob match patterns defines whether a file is included or excluded, that is to say later entries override previous ones. This is also true for match patterns encountered deeper down the directory tree, which can override a previous exclusion. Be aware that excluded directories will **not** be read by the backup client. Thus, a `.pxarexclude` file in an excluded subdirectory will have no effect. `.pxarexclude` files are treated as regular files and will be included in the backup archive.

For example, consider the following directory structure:

`1# ls -aR folder 2folder/: 3. .. .pxarexclude subfolder0 subfolder1 4 5folder/subfolder0: 6. .. file0 file1 file2 file3 .pxarexclude 7 8folder/subfolder1: 9. .. file0 file1 file2 file3 10`

The different `.pxarexclude` files contain the following:

`1# cat folder/.pxarexclude 2/subfolder0/file1 3/subfolder1/* 4!/subfolder1/file2 5 6# cat folder/subfolder0/.pxarexclude 7file3 8`

This would exclude `file1` and `file3` in `subfolder0` and all of `subfolder1` except `file2`.

Restoring this backup will result in:

`1ls -aR restored 2restored/: 3. .. .pxarexclude subfolder0 subfolder1 4 5restored/subfolder0: 6. .. file0 file2 .pxarexclude 7 8restored/subfolder1: 9. .. file2 10`

## Encryption

Proxmox Backup supports client-side encryption with AES-256 in [GCM](https://en.wikipedia.org/wiki/Galois/Counter_Mode "https://en.wikipedia.org/wiki/Galois/Counter_Mode") mode. To set this up, you first need to create an encryption key:

`1# proxmox-backup-client key create my-backup.key 2Encryption Key Password: ************** 3`

The key is password protected by default. If you do not need this extra protection, you can also create it without a password:

`1# proxmox-backup-client key create /path/to/my-backup.key --kdf none 2`

Having created this key, it is now possible to create an encrypted backup, by passing the `--keyfile` parameter, with the path to the key file.

`1# proxmox-backup-client backup etc.pxar:/etc --keyfile /path/to/my-backup.key 2Password: ********* 3Encryption Key Password: ************** 4... 5`

Note

If you do not specify the name of the backup key, the key will be created in the default location `~/.config/proxmox-backup/encryption-key.json`. `proxmox-backup-client` will also search this location by default, in case the `--keyfile` parameter is not specified.

You can avoid entering the passwords by setting the environment variables `PBS_PASSWORD` and `PBS_ENCRYPTION_PASSWORD`.

### Using a master key to store and recover encryption keys

You can also use `proxmox-backup-client key` to create an RSA public/private key pair, which can be used to store an encrypted version of the symmetric backup encryption key alongside each backup and recover it later.

To set up a master key:

1.  Create an encryption key for the backup:
    
    `1# proxmox-backup-client key create 2creating default key at: "~/.config/proxmox-backup/encryption-key.json" 3Encryption Key Password: ********** 4... 5`
    
    The resulting file will be saved to `~/.config/proxmox-backup/encryption-key.json`.
    
2.  Create an RSA public/private key pair:
    
    `1# proxmox-backup-client key create-master-key 2Master Key Password: ********* 3... 4`
    
    This will create two files in your current directory, `master-public.pem` and `master-private.pem`.
    
3.  Import the newly created `master-public.pem` public certificate, so that `proxmox-backup-client` can find and use it upon backup.
    
    `1# proxmox-backup-client key import-master-pubkey /path/to/master-public.pem 2Imported public master key to "~/.config/proxmox-backup/master-public.pem" 3`
    
4.  With all these files in place, run a backup job:
    
    `1# proxmox-backup-client backup etc.pxar:/etc 2`
    
    The key will be stored in your backup, under the name `rsa-encrypted.key`.
    
    Note
    
    The `--keyfile` parameter can be excluded, if the encryption key is in the default path. If you specified another path upon creation, you must pass the `--keyfile` parameter.
    
5.  To test that everything worked, you can restore the key from the backup:
    
    `1# proxmox-backup-client restore /path/to/backup/ rsa-encrypted.key /path/to/target 2`
    
    Note
    
    You should not need an encryption key to extract this file. However, if a key exists at the default location (`~/.config/proxmox-backup/encryption-key.json`) the program will prompt you for an encryption key password. Simply moving `encryption-key.json` out of this directory will fix this issue.
    
6.  Then, use the previously generated master key to decrypt the file:
    
    `1# openssl rsautl -decrypt -inkey master-private.pem -in rsa-encrypted.key -out /path/to/target 2Enter pass phrase for ./master-private.pem: ********* 3`
    
7.  The target file will now contain the encryption key information in plain text. The success of this can be confirmed by passing the resulting `json` file, with the `--keyfile` parameter, when decrypting files from the backup.
    

Warning

Without their key, backed up files will be inaccessible. Thus, you should keep keys ordered and in a place that is separate from the contents being backed up. It can happen, for example, that you back up an entire system, using a key on that system. If the system then becomes inaccessible for any reason and needs to be restored, this will not be possible as the encryption key will be lost along with the broken system. In preparation for the worst case scenario, you should consider keeping a paper copy of this key locked away in a safe place.

## Restoring Data

The regular creation of backups is a necessary step to avoiding data loss. More importantly, however, is the restoration. It is good practice to perform periodic recovery tests to ensure that you can access the data in case of problems.

First, you need to find the snapshot which you want to restore. The snapshot command provides a list of all the snapshots on the server:

`1# proxmox-backup-client snapshots 2┌────────────────────────────────┬─────────────┬────────────────────────────────────┐ 3│ snapshot │ size │ files │ 4╞════════════════════════════════╪═════════════╪════════════════════════════════════╡ 5│ host/elsa/2019-12-03T09:30:15Z │ 51788646825 │ root.pxar catalog.pcat1 index.json │ 6├────────────────────────────────┼─────────────┼────────────────────────────────────┤ 7│ host/elsa/2019-12-03T09:35:01Z │ 51790622048 │ root.pxar catalog.pcat1 index.json │ 8├────────────────────────────────┼─────────────┼────────────────────────────────────┤ 9... 10`

You can inspect the catalog to find specific files.

`1# proxmox-backup-client catalog dump host/elsa/2019-12-03T09:35:01Z 2... 3d "./root.pxar.didx/etc/cifs-utils" 4l "./root.pxar.didx/etc/cifs-utils/idmap-plugin" 5d "./root.pxar.didx/etc/console-setup" 6... 7`

The restore command lets you restore a single archive from the backup.

`1# proxmox-backup-client restore host/elsa/2019-12-03T09:35:01Z root.pxar /target/path/ 2`

To get the contents of any archive, you can restore the `index.json` file in the repository to the target path ‘-‘. This will dump the contents to the standard output.

`1# proxmox-backup-client restore host/elsa/2019-12-03T09:35:01Z index.json - 2`

### Interactive Restores

If you only want to restore a few individual files, it is often easier to use the interactive recovery shell.

`1# proxmox-backup-client catalog shell host/elsa/2019-12-03T09:35:01Z root.pxar 2Starting interactive shell 3pxar:/ > ls 4bin boot dev etc home lib lib32 5... 6`

The interactive recovery shell is a minimal command line interface that utilizes the metadata stored in the catalog to quickly list, navigate and search files in a file archive. To restore files, you can select them individually or match them with a glob pattern.

Using the catalog for navigation reduces the overhead considerably because only the catalog needs to be downloaded and, optionally, decrypted. The actual chunks are only accessed if the metadata in the catalog is not enough or for the actual restore.

Similar to common UNIX shells `cd` and `ls` are the commands used to change working directory and list directory contents in the archive. `pwd` shows the full path of the current working directory with respect to the archive root.

Being able to quickly search the contents of the archive is a commonly needed feature. That’s where the catalog is most valuable. For example:

`1pxar:/ > find etc/**/*.txt --select 2"/etc/X11/rgb.txt" 3pxar:/ > list-selected 4etc/**/*.txt 5pxar:/ > restore-selected /target/path 6... 7`

This will find and print all files ending in `.txt` located in `etc/` or a subdirectory and add the corresponding pattern to the list for subsequent restores. `list-selected` shows these patterns and `restore-selected` finally restores all files in the archive matching the patterns to `/target/path` on the local host. This will scan the whole archive.

With `restore /target/path` you can restore the sub-archive given by the current working directory to the local target path `/target/path` on your host. By additionally passing a glob pattern with `--pattern <glob>`, the restore is further limited to files matching the pattern. For example:

`1pxar:/ > cd /etc/ 2pxar:/etc/ > restore /target/ --pattern **/*.conf 3... 4`

The above will scan trough all the directories below `/etc` and restore all files ending in `.conf`.

### Mounting of Archives via FUSE

The [FUSE](https://pbs.proxmox.com/docs/glossary.html#term-fuse "https://pbs.proxmox.com/docs/glossary.html#term-fuse") implementation for the pxar archive allows you to mount a file archive as a read-only filesystem to a mountpoint on your host.

`1# proxmox-backup-client mount host/backup-client/2020-01-29T11:29:22Z root.pxar /mnt/mountpoint 2# ls /mnt/mountpoint 3bin dev home lib32 libx32 media opt root sbin sys usr 4boot etc lib lib64 lost+found mnt proc run srv tmp var 5`

This allows you to access the full contents of the archive in a seamless manner.

Note

As the FUSE connection needs to fetch and decrypt chunks from the backup server’s datastore, this can cause some additional network and CPU load on your host, depending on the operations you perform on the mounted filesystem.

To unmount the filesystem use the `umount` command on the mountpoint:

`1# umount /mnt/mountpoint 2`

## Login and Logout

The client tool prompts you to enter the logon password as soon as you want to access the backup server. The server checks your credentials and responds with a ticket that is valid for two hours. The client tool automatically stores that ticket and uses it for further requests to this server.

You can also manually trigger this login/logout using the login and logout commands:

`1# proxmox-backup-client login 2Password: ********** 3`

To remove the ticket, issue a logout:

`1# proxmox-backup-client logout 2`

## Pruning and Removing Backups

You can manually delete a backup snapshot using the `forget` command:

`1# proxmox-backup-client forget <snapshot> 2`

Caution

This command removes all archives in this backup snapshot. They will be inaccessible and unrecoverable.

Although manual removal is sometimes required, the `prune` command is normally used to systematically delete older backups. Prune lets you specify which backup snapshots you want to keep. The following retention options are available:

`--keep-last <N>`Keep the last `<N>` backup snapshots.`--keep-hourly <N>`Keep backups for the last `<N>` hours. If there is more than one backup for a single hour, only the latest is kept.`--keep-daily <N>`Keep backups for the last `<N>` days. If there is more than one backup for a single day, only the latest is kept.`--keep-weekly <N>`

Keep backups for the last `<N>` weeks. If there is more than one backup for a single week, only the latest is kept.

Note

Weeks start on Monday and end on Sunday. The software uses the [ISO week date](https://en.wikipedia.org/wiki/ISO_week_date "https://en.wikipedia.org/wiki/ISO_week_date") system and handles weeks at the end of the year correctly.

`--keep-monthly <N>`Keep backups for the last `<N>` months. If there is more than one backup for a single month, only the latest is kept.`--keep-yearly <N>`Keep backups for the last `<N>` years. If there is more than one backup for a single year, only the latest is kept.

The retention options are processed in the order given above. Each option only covers backups within its time period. The next option does not take care of already covered backups. It will only consider older backups.

Unfinished and incomplete backups will be removed by the prune command unless they are newer than the last successful backup. In this case, the last failed backup is retained.

`1# proxmox-backup-client prune <group> --keep-daily 7 --keep-weekly 4 --keep-monthly 3 2`

You can use the `--dry-run` option to test your settings. This only shows the list of existing snapshots and what actions prune would take.

`1# proxmox-backup-client prune host/elsa --dry-run --keep-daily 1 --keep-weekly 3 2┌────────────────────────────────┬──────┐ 3│ snapshot │ keep │ 4╞════════════════════════════════╪══════╡ 5│ host/elsa/2019-12-04T13:20:37Z │ 1 │ 6├────────────────────────────────┼──────┤ 7│ host/elsa/2019-12-03T09:35:01Z │ 0 │ 8├────────────────────────────────┼──────┤ 9│ host/elsa/2019-11-22T11:54:47Z │ 1 │ 10├────────────────────────────────┼──────┤ 11│ host/elsa/2019-11-21T12:36:25Z │ 0 │ 12├────────────────────────────────┼──────┤ 13│ host/elsa/2019-11-10T10:42:20Z │ 1 │ 14└────────────────────────────────┴──────┘ 15`

Note

Neither the `prune` command nor the `forget` command free space in the chunk-store. The chunk-store still contains the data blocks. To free space you need to perform [Garbage Collection](https://pbs.proxmox.com/docs/backup-client.html#garbage-collection "https://pbs.proxmox.com/docs/backup-client.html#garbage-collection").

## Garbage Collection

The `prune` command removes only the backup index files, not the data from the datastore. This task is left to the garbage collection command. It is recommended to carry out garbage collection on a regular basis.

The garbage collection works in two phases. In the first phase, all data blocks that are still in use are marked. In the second phase, unused data blocks are removed.

Note

This command needs to read all existing backup index files and touches the complete chunk-store. This can take a long time depending on the number of chunks and the speed of the underlying disks.

Note

The garbage collection will only remove chunks that haven’t been used for at least one day (exactly 24h 5m). This grace period is necessary because chunks in use are marked by touching the chunk which updates the `atime` (access time) property. Filesystems are mounted with the `relatime` option by default. This results in a better performance by only updating the `atime` property if the last access has been at least 24 hours ago. The downside is, that touching a chunk within these 24 hours will not always update its `atime` property.

Chunks in the grace period will be logged at the end of the garbage collection task as _Pending removals_.

`1# proxmox-backup-client garbage-collect 2starting garbage collection on store store2 3Start GC phase1 (mark used chunks) 4Start GC phase2 (sweep unused chunks) 5percentage done: 1, chunk count: 219 6percentage done: 2, chunk count: 453 7... 8percentage done: 99, chunk count: 21188 9Removed bytes: 411368505 10Removed chunks: 203 11Original data bytes: 327160886391 12Disk bytes: 52767414743 (16 %) 13Disk chunks: 21221 14Average chunk size: 2486565 15TASK OK 16`

## Benchmarking

The backup client also comes with a benchmarking tool. This tool measures various metrics relating to compression and encryption speeds. You can run a benchmark using the `benchmark` subcommand of `proxmox-backup-client`:

`1# proxmox-backup-client benchmark 2Uploaded 656 chunks in 5 seconds. 3Time per request: 7659 microseconds. 4TLS speed: 547.60 MB/s 5SHA256 speed: 585.76 MB/s 6Compression speed: 1923.96 MB/s 7Decompress speed: 7885.24 MB/s 8AES256/GCM speed: 3974.03 MB/s 9┌───────────────────────────────────┬─────────────────────┐ 10│ Name │ Value │ 11╞═══════════════════════════════════╪═════════════════════╡ 12│ TLS (maximal backup upload speed) │ 547.60 MB/s (93%) │ 13├───────────────────────────────────┼─────────────────────┤ 14│ SHA256 checksum computation speed │ 585.76 MB/s (28%) │ 15├───────────────────────────────────┼─────────────────────┤ 16│ ZStd level 1 compression speed │ 1923.96 MB/s (89%) │ 17├───────────────────────────────────┼─────────────────────┤ 18│ ZStd level 1 decompression speed │ 7885.24 MB/s (98%) │ 19├───────────────────────────────────┼─────────────────────┤ 20│ AES256 GCM encryption speed │ 3974.03 MB/s (104%) │ 21└───────────────────────────────────┴─────────────────────┘ 22`

Note

The percentages given in the output table correspond to a comparison against a Ryzen 7 2700X. The TLS test connects to the local host, so there is no network involved.

You can also pass the `--output-format` parameter to output stats in `json`, rather than the default table format.