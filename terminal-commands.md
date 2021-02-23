# Disk Usage
Get file size of a folder/file
```
$ du -hs <file/folder>
```
List all folders, their size and give a total: 
```
$ du -hsc *
```
Sort the list of all folders, their size the total: 
```
$ du -hsc * | sort -h
```

# SSD Health - (macOS)
The flash memory in SSD's and NVME drives have a limited amount of write cycles before they become unstable. One way to check the health of an ssd is to see how many writes have performed on the drive and comparing that to the drives rated Total Bytes Written. On average, a small 256GB SSD has a TBW of 150TB.

Install smartmontools
```
$ brew install smartmontools
```
Check SMART/Health information using the smartctl tool. The `Data Units Written` is what you want to be checking.
```
$ smartctl -a disk0

=== START OF SMART DATA SECTION ===
SMART overall-health self-assessment test result: PASSED

SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        27 Celsius
Available Spare:                    100%
Available Spare Threshold:          99%
Percentage Used:                    0%
Data Units Read:                    24,551,572 [12.5 TB]
Data Units Written:                 19,754,123 [10.1 TB]
Host Read Commands:                 1,175,280,718
Host Write Commands:                663,809,001
Controller Busy Time:               0
Power Cycles:                       67
Power On Hours:                     535
Unsafe Shutdowns:                   11
Media and Data Integrity Errors:    0
Error Information Log Entries:      0
```


# which
The linux `which` command is used to find the location of a program.
```
$ which <program-name>
```
Example:
```
$ which gcc
/usr/bin/gcc
```

# alias
Aliases are a way to set keyboard shortcuts or abbreviations.  If you want it to be **permanent**, add the alias to your `~/.bash_profile` or `~/.zshrc`.

Create a temp alias
```
$ alias <alias-name>=<command>
```
Remove an alias
```
$ unalias <alias-name>
```
See list of aliases
```
$ alias
```

Examples:
```bash
# Set an alias
$ alias ll="ls -la"

# Remove an alias
$ unalias ll

# See specific alias
alias ll
ll='ls -la'

# See List of aliases
$ alias
g=git
ga='git add'
gaa='git add --all'
.
.
.
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

# BASH: multiple commands in single line
In shell, `;` and `&&` are similar in that they both can be used to terminate commands. The difference is `&&` is also a conditional operator. With `;` the following command is always executed, but with `&&` the later command is only executed if the first succeeds.
```
$ false; echo "yes"     # prints "yes"
$ true; echo "yes"      # prints "yes"
$ false && echo "yes"   # does NOT echo
$ true && echo "yes"    # prints "yes" 
```
