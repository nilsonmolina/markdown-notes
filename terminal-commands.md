# Disk Usage
Get file size of a folder/file
```
$ du -hs <file/folder>
```
List all folders, their size and give a total: 
```
$ du -hsc *
```

# Shutdown 
To shutdown now
```
$ sudo shutdown -h now
```
To restart now
```
$ sudo shutdown -r now
```
To shutdown in 60 minutes
```
$ sudo shutdown -h +60
```
To restart in 5 minutes
```
$ sudo shutdown -r +5
```
Cancel scheduled shutdown
```
$ shutdown -c
```

# Show Date & Time
To show the current date and time
```
$ date
```

# Stop Parsing Command Line Options
Useful for deleting files that start with: `-`
```
$ rm -- -file
```

# Compression
File and folder compression saves on file size and ensures the contents are captured and delivered or stored as one monolithic file. A compressed file which contains files and folders is generally referred to as an archive. Here are some built in compression applications you can use including zip, tar, bz2, and gz.

## ZIP – Cross Platform
First up is ZIP one of the most commonly used compression techniques used across all platforms.

**To compress**
```
zip -r archive_name.zip folder_to_compress
```
If you want to make a zip without those invisible Mac resource files such as `_MACOSX` or `._Filename` and `.ds` store files, use the `-X` option in the command:
```
zip -r -X archive_name.zip folder_to_compress
```
**To extract**
```
unzip archive_name.zip
```
## TAR.GZ – Cross Platform
Second up is TAR, an old favourite on Unix/Linux – you add the GZ for the compression – compresses tighter than zip

**To compress**
```
tar -zcvf archive_name.tar.gz folder_to_compress
```
**To extract**
```
tar -zxvf archive_name.tar.gz
```
## TAR.BZ2 – Cross Platform
A variation on TAR GZ but with better compression than both tar.gz and zip.

**To compress**
```
tar -jcvf archive_name.tar.bz2 folder_to_compress
```
**To extract**
```
tar -jxvf archive_name.tar.bz2
```
## GZ
Without the tar

**To extract**
```
gunzip archivename.gz
```