# Linux

### useful links
```bash
https://www.commandlinefu.com/commands/browse
https://tldr.sh/
https://linux.die.net/man/
```

## list of package managers
```bash
apt       # debian based   #.deb  # Ubuntu, Mint,kali, Debian, elementary OS, Zorin OS, BlackBox
dnf  or yum        #Red Hat-base     #.rpm   # Fedora, CentOS, RHEL, Amazon Linux, Oracle Linux, openSUSE
pacman    #Arch-based       #.rpm   # Arch Linux, Manjaro, EndeavourOS, ArcoLinux
portage   #Gentoo-based     #.rpm   # Gentoo
slackpkg,pkgtool   #Slackware-based    # Slackware
snap     #cross-distribution
flatpak   #cross-distribution
AppImage   #cross-distribution
zypper(ZYpp)    #openSUSE and SUSE Linux
emerge
synaptic      # graphical package manager
# Many distributions offer graphical package managers (e.g., Ubuntu Software Center, GNOME Software)
 ```
## Linux Version
```bash
uname                  # always return linux
uname -a               # Linux + distro + version + date release + cpu architecture + os
head -n1 /etc/issue    # Show distri-bution (display a banner to SSH users before the login prompt)
hostname               # show hostname (/etc/hostname)
hostname -i            # show hostname with ip
sudo hostname something # change temporary hostname to something 
lsmod                   # List of modules of kernel
uname -a                # show system kernel
```
## os
```bash
cat /etc/*release             # what distro we are running 
lsb_release -a               # what distro we are running
cat /etc/os-release          # what distro we are running
```

## Doc
```bash
help                       # list of all commands + shell
help cd                    # doc for cd command == cd --help
help for                   #doc for for command in bash
man -k print              # seacrh in commands and short descriptoin like grep    **
compgen -c 
dman                      # read man page for command without installing that( apt i debian-goodies)
wman                      # text web browser for reading man page on internet
```

## man
```bash
man builtins
man -k ^ls$
info sort | grep "\-s" or grep " -s"                        # Search for '-s' in the doc of sort
man ps | grep -E "ali |" -n    # you can see line number in man page by grep 
man rsync| less -p -v     #search for -v flag in rsync command
rsync -h| less -p -v      #search for -v flag in rsync command
g45                             # go to line 45 | find the line number with -n in grep | use / for search 
-N press Enter                  # toggle line numbers in man page
```

## WH
```bash
who
whoai
which
whereis
logname       # 
```
## finding apps
```bash
type vim                                  # find the location of app
which vim                                 # find the location of app
command -v vim                            # find the location of app
whereis vim                               #  locate the 1.binary(executable file), 2.source, 3.manual pages of a command
compgen -c | -a                            # list of binary files
```

## List of commands
```bash
help
alias
compgen -c                 # will list all the commands you could run.
compgen -a                 # will list all the aliases you could run.
compgen -b                 # will list all the built-ins you could run.
compgen -k                 # will list all the keywords you could run.
compgen -A function        # will list all the functions you could run.
compgen -A function -abck  # will list all the above in one go.
```
## apt
```bash
apt update                   # Refreshes repository index
apt upgrade                  # Upgrades all upgradable packages
apt search vim              # Search for a package in  internet
apt search ^vim$             # apt search == google search
apt-cache search vim               #like apt search vim
apt show vim                # List information about the vim (no need to install)
apt list          # list of all packages
apt list vim                  # find
apt list --all-versions wget # List all versions of the package
apt list --installed          # List all installed packages
apt list --upgradable         # List all upgradable packages
apt list --all-versions        # This shows all available versions of each package
apt install wget             # Install the latest version of the wget package
apt install wget=1.2.3       # Install a specific version of the wget package
apt remove wget              # Removes the wget package
```


## Command History

```bash
!!            # Run the last command
history -c        # clear all history
touch 1.txt   # update timestamp of file exists
```

## Creating Files

```bash
touch 1.txt          # create file or update(touch) timestamp of existing file  
touch foo           # foo is a file without any format
touch 1.txt 2.txt       # create multiple files
touch {1,2}.txt         # create multiple files
touch test{a..c}.txt       #create testa.txt, testb.txt and testc.txt
cat > 1.txt             # start writing in a file in terminal prompt
echo "hi" > 1.txt
printf "hi" > 1.txt
vim 1.txt
nano 1.txt
```

## Creating Directories

```bash
mkdir folder                        # Create a directory
mkdir folder1 folder2                    # Create multiple directories
mkdir -p parent/child       # -p or --parents creates nested directory
mkdir -p {parent1,parent2}/childfolder            # childfolder is inside both parents
touch a; mkdir a                  #create a as a file and gets error for directory
mktemp -d              # -d or --directory creates a temporary directory
```
## Links
+ Hard links cannot cross filesystems but Symlinks can cross filesystems
+ Deleting the original file does not delete the hard link 
+ soft link = symbolic link `ln -s`

```bash
ln 1.txt hardlink.txt	          # Creates a hard link (shares same inode)
# change 1.txt will also change hlink.txt. but delete 1.txt will not delete hlink.txt
ln /path/to/1.txt /path/to/hardlink.txt       # change location
ln -s  1.txt link.txt            # -s or --symbolic creates a soft link for 1.txt into link.txt. 
ln -s -f 1.txt link.txt          # -f or --force overwrite an existing symbolic link 'bar'
ls -l                               # Show where symbolic links are pointing
unlink symlink_name            # Safely remove a symlink
```

## Temp files

```bash
/tmp                                 # is typically cleared on system reboots.
/var/tmp                              # might retain files across reboots.
mktemp                                 # create a random temporary file
mktemp -d myname.XXXX                  # cretae a temp diretory in current folder (-d folder)
mktemp -u /tmp/temp1.XXXXXX /tmp/temp2.XXXXXX       # create multiple temp file
```
### zip
```bash
# Compresses multiple files into *.zip files ---> Install zip
zip comp.zip /1.txt                # Compress into comp.zip 
zip comp.zip /1.txt /2.txt       # Compress multiple files
zip comp.zip /{1,2}.txt          # Compress multiple files
zip -r comp.zip /folder            # Compress a folder into a zip file -r or --recurse-paths
# Compresses a single file into *.gz files.
gzip 1.txt            # Compress 1.txt and then delete it
gzip -k 1.txt         # Compress 1.txt but not delete it -k = keep alive
# Compresses multiple files into *.tar , *.tar.gz, *.tpz , *.tgz
# tar is the best
tar -cz -f comp.tgz 2.txt 1.txt # -f should be separate and last or --file:comp.tgz==> -c: create -z: gzip -f: file
```
### unzip

```bash
# becarefull since unzip may delete your file
unzip comp.zip                    # Unzip file into current directory --->Install unzip
gunzip comp.gz                    # Unzip file into current directory and delete the comp.zip
gunzip -k comp.gz                # Unzip but not delete -k: --keep-alive (do not delete)
tar -xz -f comp.tar.gz          # -x or --extract means unzip, -f or --file, -z or --gzip for gz format
tar -x --file=comp.tar              # no need for -z bcz format is not gz
```

## Navigating Directories

```bash
pwd                       # show current directory path
cd                        # go to home directory
cd ~                      # go to home directory
cd -                      # go to previous directory
pushd app/folder1                 # pushd changes directory like "cd" and save previuos directory in a stack memory
# always current directory is in stack
popd                      # go to directory in stop of stack
dirs                      # list of stack memory
```

## List direcoty

```bash
tree                      # list in tree format
tree -a                   # including hidden files
tree -d                   # just directories
ls -R                      # -R:recursive List inner folders like tree
echo *                    # echo all files in current directory 
echo ?                    # list files in current directory with one char in name like a
ls                        # list directories sort by name alphabetically
dir                       # like ls (diff in format) like win
ls -a                      # -a or --all also shows hidden files ===> -aA show hidden but not . and ..
ls -d | ls -d */            # List just directories
ls -l                     # long format
ls -l -h                   # -h or --human-readable is for sizes
ls -lhS                    # -S List and Sort by size
ls -lhSr                  # -Sr List and Sort then reverse
ls -lhSX                  # -SX List and Sort by extension or format
ls -lhSt                  # -St List and Sort by time
ls -t                     # list by time, new files come first
ls folder1                    # list files inside folder1
ls -c -ltd folder            # just info of specific folder
file 1.txt              # simple file info ==> simple stat
stat 1.txt              # stat in a table format for file or folder
```
## ls -l cols
```bash
# total (in ls -ls commnd)   # total number of bloacks ===> ls -asl ===> see block size of each file (Blocks have a fixed size :4kb)
1. file type + file permissions
3. number of hard links     # for files =1 for dir =2 
4. owner
5. group
6. size                      # 4kb for empty folder
7. last modified time
8. name
```


## wildcard

```bash
ll 1.js                  # just info of 1.js
ls *.js                   # List all .js files
ls *.???                  # List files with three chars in format like .txt
ls [12].txt               # List files with numbers in format like 1.txt and 2.txt
ls [!12].txt              # List files without this numbers
ls [1-9].txt              # List files 1.txt through 9.txt
```
## Deleting Directories and Files

```bash
rmdir folder1                        # delete empty directory (safe command)
rm -d ./*                        # delete empty directory ==rmdir
rm -r  folder1            # delete directory including contents (-r or --recersive )
rm 1.txt                        # delete file
rm -r 1.txt                         # -r also delete files
rm -f 1.txt                             # delete  without prompting for any confirmation
rm -i 1.txt                                         # interactively (ask question)
trash 1.txt                      # move file to trash (you need to install)
```

## Copy
```bash
cp file.txt /path/to/destination/               # copy a file to a directory
cp -r folder1 /path/to/destination/            # copy a directory (including subdirectories)
cp -i file.txt /path/to/destination/          # prompt before overwriting 
cp -n folder1 /path/to/destination/            # copy with no overwrite
cp 1.txt 2.txt /path/to/destination/           # copy multiple files
cp -s file.txt link_name                      # create a symbolic link instead of copying
cp 1.txt 2.txt                                # copy a file
scp file.txt user@remote_host:/path/to/destination/        # Copy a Local File to a Remote Server
scp user@remote_host:/path/to/file.txt /local/destination/ # Copy a Remote File to Local Machine
scp -r dir/ user@remote_host:/remote/destination/           # copy a folder
# rsync is faster than scp for large data because it only transfers changes and supports compression with -z option
rsync 1.txt dest/                 # copy this file in dest/
rsync -a source/ dest/            # -a for archive mode, it will not copy folders without -a, we can use -r alternatively
rsync -avh source/ dest/          # -v for verbose mode, -h for human-readable sizes
rsync -av source dest/          # "source/" → copies contents of source/ but "source" → copies the entire directory source
rsync -avz source/ dest/         # -z or --compress enables compression for faster transfers
rsync -av s1/ s2/ dest/         # copy s1/ and s2/ into dest/
rsync -avz --bwlimit=500 /local/dir/ sername@hostname:/folder       # copy local to remote (we can use vice versa) # set Limit Bandwidth
rsync -av --exclude='*.tmp' --exclude='logs/' source/ dest/        # Exclude Specific Files/Directories
rsync -zv 1.txt 2.txt                           # copy and rename for files
rsync -l folder1                      # like "ls -l"
```

## Moving Files

```bash
mv folder1 folder2                                # Move directory
mv 1.txt folder                                # Move file
mv 1.txt 1new.txt                                  # Rename file  # there is no command for renaming a file
```
## Reading Files

```bash
cat 1.txt            # Print content in termianl
cat 1.txt 2.txt         # Print multiple files
cat -b 1.txt         # show line number for cat
open 1.txt           # open in the default editor
more 1.txt           # show some contents with option more
less 1.txt           # show some contents with option more (keys: g: go to top of file, G: bottom of file, /foo: to search for 'foo')
head 1.txt           # Print top 10 lines of file
head 1.txt -n 200    # show 200 line
tail 1.txt           # Print bottom 10 lines of file (tail -n 3 1.txt)
wc 1.txt             # number of lines + number of words + number of characters
file f1oo.txt           # file type like ASCII, ...
basename /home/1.js    # gives you the last section of the path == 1.js
basename /home/1.js .js  # remove .js from the basename
dirname /home/mycomputer/project  # remove last section and return others= /home/mycomputer
```

## Writing Files

```bash
vim 1.js                        # write inside vim
cat > 1.js                      # write inside terminal (replace)
cat >> 1.js                     # write inside terminal (append)
cat >> 1.js << D                # write inside terminal until you type D and press enter
echo "this is a text" > 1.js    # write without opening the file (replace)
echo > 1.js                     # empty the content of a file with a space char
> 1.js                          # empty the content of a file with no char
truncate -s 0 1.js              # empty file 
yes "Line" | head -n 100 > my_file.txt      # create a file with 100 lines
```

## Vim
```bash
vim -c "/ali" 1.txt                             #take me to position that ali is. -c means commnad mode and / means search mode 
vim +14 1.txt                                  # open file at line 14
```

## nano


# Finding Files


```bash
sudo updatedb -v                                  # Update the index in verbose mode
sudo updatedb -U /home                   # Only index a specific directory
sudo updatedb --output=~/mylocate.db       # Store the database in a custom location
locate nginx.conf                           # locate command use indexing (pre-built db by updatedb) so it is faster than find  # Install
locate -i "f*.JPG"                             # Find all files starting with 'f' in Case-Insensitive mode 
locate -l 5 "*.log"                          # limit result with -l, show only 5 result
locate "*.conf" | grep "nginx"              # combine for finding a text in specfic files
find                                       # like tree (find -print)
find 1.txt                                                 # find 1.txt in current directory with no depth in folders (bad use)
find -name 1.txt                                             # find 1.txt in current directory with depth in folders
find /path -name 1.txt                                          # find a file in specifc directory
find /path -iname foo.txt                                    # case insensitive mode
find /path -name "*.txt" -or -name "*.js"                     # combination with or
find /path -name 1.txt -delete                                # Find a file then delete it
find /path -name "*.png" -exec pngquant {} \;                  # Find all .png files and execute pngquant on it # \; = end of command.
find /path -name "*.jpg" -exec cp {} /backup \;                # execute cp on each file       
find /path -name "*.conf" -ls                                     # find and print detail
find /path -type f -not -path "excluded_path/.git/*"            #  Ignore .git # -not for excluding directories
find /path -type d,l,f  				# find all directorys ,links and files in any name
find /path -type f -name app          # find files only with this name (try to use this flag always)
find /path -type d -name app               # find only directories with this name
find /path -size +1k    				# size of result is > 1kB
find /path -size +50M -size -100M       # Size between 50MB – 100MB
find /path -size 0 -delete                       # find empth files and folders and delete them
find /path -mtime -30                   # files modified in last 30 days
find /path -mtime +30 -delete             # Modified >30 days ago and delete them
find /path -perm 644              # find by permissions
find /path ! -perm 777          # Find all the files without permission 777
find /path -perm -u=rw            # files that user has read/write
find /path -user root                           # find by owner
```

## Find content of files

```bash
# grep= Global Regular Expression Print
grep "error" ./log.txt        # basic Search in a File
grep "error" log.txt --colour         # add color to output
grep -n "error" log.txt         # show line numbers with -n or --line-number  (use this flag always)
grep -i "error" log.txt        #  case-insensitive
grep -r "error" /path/         # search recursively in this directory
grep -R "error" /path/         # -R or --dereference-recursive  means follow symbolic links
grep -w "error" log.txt         # find exact word
grep -x "error" log.txt         # match the entire line with -x or --line-regexp 
grep -v "error" log.txt         # invert match with -v or --invert-match 
grep -c "error" log.txt         # count matches
grep -l "error" ./*.js         # show file names only with -l or --files-with-matches
grep -L "error" ./*.js         # show file names that don't match with -L or --files-without-match
grep "error" -A 5 log.txt                       # find 5 lines After the match
grep "error" -B 5 log.txt                       # find 5 lines Before the match (-A 5 -B 5)
grep "error" -C 5 log.txt                       # find 5 lines After and Before (content) the match (lowercase -c is for count)
grep 'foo' -lrc ./*                           # show count + name files that match in all of directories and subdirectories
grep 'error\|warning' log.txt                       # or
grep '...-...-...$' log.txt                    # use regex in grep  (as2-we1-dsa)
grep -p '\d{3}-\d{3}$' log.txt                # use regex in grep with -p or --perl-regexp  (333-333)  
grep  -E 'error|warning' log.txt                # Extended Regex (egrep) with -E or --extended-regexp|
grep -e "error" -e "warning" log.txt           # Search for Multiple words
egrep 'error|warning' log.txt                       # Use regular expressions
zgrep "error" /var/log/syslog.2.gz             #   Search in Compressed Files
man ps | grep -E --color "word| "              # ?????
man lsof | grep -e "^" -e "negated"            # ????
man lsof | egrep  "^|negated"                  # ?????
```


### Replace in Files

```bash

sed 's/foo/bar/' 1.txt                          # Replace foo with bar in foo.txt (find all first matches in each line)
sed 's/noexsits/bar/' 1.txt                       # we will see the output even no match find  # s:substitute
sed 's/foo/bar/g' 1.txt                       # -g means find all matches in entire line 
sed 's/foo/bar/i' 1.txt                       # -i for case insensitive like: fox, FOX, ...
sed 's/foo/bar/2' 1.txt                      # just second match of line
sed '3s/foo/bar/' file.txt                   # Replace foo with bar only on line 3
sed '1,5s/foo/bar/' file.txt                 # Replace between lines 1-5
sed '1,3 s/unix/linux/' 1.txt                  # Just in line 1 to 3 (4,$)
sed '/baz/s/foo/bar/' file.txt                # Replace only on lines containing baz
sed 's/foo/bar/3g' 1.txt                      # Replacing from nth occurrence to all occurrences in a line
sed -i 's/foo/bar/g' file.txt                # Edit file in-place with -i or --in-place 
sed -i.bak 's/foo/bar/g' file.txt               # Create backup with .bak extension
sed -n 's/unix/linux/p' 1.txt                    # -p :Print the matches twice, -n: Printing only the replaced lines
-------
sed '/foo/d' file.txt              # Delete lines containing foo
sed '/start/,/end/d' file.txt      # Delete range from start to end
sed '5d' 1.txt                     # delete line 5
sed '$d' 1.txt                     # delete last line
sed '3,6d' 1.txt                   # delete line 3 to 6
sed '/abc/d' 1.txt                 # delete line that contain abc
```


## Sort
```bash
sort 1.js                       #sort lines inside 1.js and print in terminal per first column by alphabet
sort -o output.txt file.txt     #sort lines inside 1.js and save in output.txt
sort -u filename.txt            #sort lines inside 1.js and remove repeated 
sort -k 2 1.js                  #sort lines inside 1.js per second column(key) (date > 1.txt or ls-la >1.txtj j) 
sort -k 1 -k 2 1.js             #sort lines inside 1.js per first then per second column 
sort -k 2 -n 1.js               #sort lines inside 1.js per second column numerically [ -M for month ] 
sort -t : -k 3n /etc/passwd    # sort -t for separator -k for col and 3n means third col by numerically
sort 1.js -r                   #sort reverse
sort 1.js -n                   #sort numerically
sort 1.js -f                   #sort uppercase and lowercase together
sort 1.js +4                   #ignore 4lines
sort 1.js | uniq -c            # count repeated lines
sort 1.js | uniq -c | sort -n  # sort by repeated liens
```


## cut
```bash
cut -b 1,2,3 state.txt # cutting out the column of a file (slice a file)
# Tabs and backspaces are treated like as a character of 1 byte
ps -aux | cut -b 1-3,5-7 #slice with range (2- means 2 to end) (-3 means first to col 3)
ps -aux| cut -d " " -f 1 #slice with -d delimiter and -f field
cut -d " " -f 1,2 state.txt --output-delimiter='%'
cat state.txt | head -n 3 | cut -d ' ' -f 1 > list.txt
fold -w 60 -s foo.txt    # wrap in 60 char wrap ( width) and -s for break the lines on spaces
```

## awk

```bash
/pattern/ {awk-commands}                             # syntax awk
awk -f 1.awk /etc/passwd                             # run a awk file with content "{print}"
ps | awk '{print $1}'                                # print column one
ps | awk '{print $NF}'                               # print last column
ps | awk '{print $0}'                                # print all columns (whole a row)
ps | awk '{print}'                                   # print all columns with all rows
ps | awk 'NR==1'                                     # print first row (row number one)
ps | awk '{print NR $0}'                             # print whole row with NumberRow 
ps | awk 'NR==1,NR==10 {print $0}'                   # print rows between 1 to 9 (range)
ps | awk '{print substr($1,2)}'                      # print substring
awk -F: '{print $1, $NF}' /etc/passwd                # use colon as Filed separator or -F ":"
awk -F: '{print $1 ",,,,," $NF}' /etc/passwd         # You can use separators with "
awk -F: '{print $1 "\t" $NF}' /etc/passwd            # tab as separator
awk -F: 'BEGIN {FS=":"; OFS="-----"} {print $1,$6,$7}' /etc/passwd #use formula as a separator
awk -F: '/^m/ {print $1,$6,$7}' /etc/passwd          #  awk syntax / / for using Regex pattern  
df | awk '{print $1 "\t" $2 - $3}'                      # + - * /  for math operator  
awk 'length($0) > 12' /etc/shells                    # find the lines that have more than 12 char in col $0
ps -ef | awk '{if($NF) == "/bin/fish" print $0}'     # use if
awk 'BEGIN {for ( i=1; i<10; i++ ) print "Hi" i }'
awk 'END' {print NR} /etc/shells                     # print number of line of rows
awk 'END' {print NR} /etc/shells /etc/passwd         # sum of number of rows of multiple files
```

## Standard Output +  Standard Error + Standard Input

```bash
echo "hi" > 1.txt       # overwrite the content of 1.txt  (redirection mark)
echo "hi" >> 1.txt      # append to file
ls -lh > newfile.txt         #first create newfile.txt then run the command ls -h then write
ls xxxNO > 1.txt                   # if xxxNO does not exist the contenet of 1.txt would be empty (not error)
ls exists 1> stdout.txt              # by default it is channel 1
ls noexist 2> stderror.txt          # use channel2 (standard error) if you want to see error in contenct
ls xxx 1> output.txt 2> error.txt      # errors will write in error.txt and normal output in output.txt
ls >both.txt 2>&1            # send channel 2  and 1 in the same file
ls > /dev/null             # ????
read myvar                   # read from user input and write to the variable myvar (foo=myvar() in python)
tee -a 1.txt             # echo + save in file (-a append)
```


## command combination
```bash
cmd1 ; cmd2 ; cmd3	# run multi commands 
cmd1 && cmd2		# cmd2 runs if cmd1 runned success
cmd1 || cmd2		# cmd2 runs if cmd1 NOT runned success
cmd  > filename		# send output to filename (ovewwrite)
cmd >> filename		# append output to filename
cmd < filename		# send input data to cmd from filename
help | more
echo "1.txt 2.txt" | xargs mkdir
echo -n "hello"; date  # print hello and date in one line===> echo -n ==> one line
{ echo -n "hello"; date;} > output.txt     # command grouping and redirect both output into a file{space needed}
```


## Language Translation

```bash
trans -i input.txt -o output.txt                # translate a language    # sudo apt install translate-shell
```

## Char Trnaslation
```bash
echo "this is a test" | tr ' ' '_'              # replace space with _ 
echo "a" | tr [a-z] [A-Z]                                     # uppercase   # tr is a utility for translating or deleting characters
tr A-Z a-z < foo.txt                                       # lowercase (you can do not use [])
```


## Calculator
```bash
bc                          # basic calculator
quit                        # not <C-c>
echo "1 + 2" | bc           # use pipeline
bc < foo.txt                # use file (4 +5 inside the foo.txt) input redirection
```

### Calendar
```bash
calendar                # occasaional wikipea calendar       # sudo apt install calendar
cal                    # month view like paper calendar     # sudo apt install ncal
cal -y                 # display 365 days in 12 months
cal -3              # Display the previous, current, and next month
cal -A 2               # Display the current month and the next 2 months
cal -B 1                 # Display the current month and the previous month
```


### Date and time

```bash
date                   # Sun Jun  1 23:30:40 +0330 2025
date +%s                   # 1748808533 = current timestamp
date -d "@1620000000"         # convert timestamp to "date" command output
date --iso-8601          # 2025-06-01
date --iso-8601=ns          # 2025-06-01T23:31:39,815450406+03:30
date +"%Y-%m-%d"           # 2024-03-15 (iso-8601)      # Y=2024, y=24
date +"%H:%M:%S"           # 14:30:45 (24-hour time)
date +"%A, %B %d, %Y"      # Friday, March 15, 2024 (full date)     # a=Abbreviated weekday (Fri)   # b=Abbreviated month (Mar)
date +"%F %T"              # 2024-03-15 14:30:45 (ISO timestamp)
date -d "now + 1 day"      # Tomorrow
date -d "now - 1 week"     # One week ago
date -d "next monday"      # Next Monday
time tree              # how long takes this command(tree) to execute
```

### cronjob

### Shutdown and Reboot

```bash
shutdown
reboot
```

## Variables
```bash
printenv          # list all variables on bash like $PATH, $HOME, $DISPLAY
echo $USER    # display user
echo $SHELL   # display shell
chsh -s /bin/zsh    # change default shell
chsh -s $(which zsh)
cat /etc/shells          # list of all shells 
$HOME
$PWD
$PATH
$TERM
$TERMINFO
```


## Environment Variables

``` bash
TEST="Unix" | echo $TEST             #set a variable by user
export				    # export a VAR to sub shels and sob processe
printenv  			    # list all variables on bash like $PATH, $HOME, $DISPLAY
unset VAR_NAME			# drop VAR_NAME from bash
env 				    # list all variables on bash like $PATH, $HOME, $DISPLAY
env VAR=value cmd 		# Run cmd with new value of VAR 
echo $?                 # returns exit status code of last command
```


## Important Files
```bash
cat /etc/passwd                     # list of all users
cat /etc/shells                     # list of all shells
cat /etc/services                   # list of all ports
cat /etc/crontabs                   # list of all crontabs
```


## User Management
```bash
sh                    # become shell ==> bash is better
bash                   # become bash
sudo sh -c 'cd dirname' # run a command as a shell
su - mohamad            # become another user
sudo -s                 # become root
sudo su                 # become root
useradd ali
passwd ali               # add password for ali
userdel -r ali            # -r is for delete home directory  
useradd -m -s [shell_name] username # creates a new user -m create a home (/bin/bash)
useradd ali -g [pg_name] 		# adds user to group_name as primary group
groups                  # display groups
sudo tail /etc/group
groupadd [OPTIONS] GROUPNAME
id ali                    # show USERID for ali (username) ==> id show id of cuurent user
who                    # show who is logged on the system
w                      # like who with more details
users                   # list logged in users
cat /etc/passwd                     # list of all users
```

## passwd cols

```bash
compgen -u  # list of all users
man passwd
username:password-encrypted(x)(/etc/shadow):uid:gid:gecos(User information):home:shell
```

## File Permissions
+ For a directory, execute means you can enter a directory.

| -r Read =4 | -w Write =2 | -x Execute =1 |

- u - User
- g - Group
- o - Others
- a - All of the above

```bash
ls -l            # List file permissions
chown  username 1.txt    # change owner
chown -r ali folder      # -r change all inener folders too 
chgrp groupname 1.txt
chmod -744 1.sh        # rwx for user, r for group, r for others (+ or -)
chmod u+x 1.sh         # add execute permission to user
chmod u-x,g-x 1.sh     # remove execute permission from user and group 
chmod o+rwx 1.sh                 # add rwx for other
chmod o=rw 1.sh                   # set rw for other
chmod og-x 1.sh                # remove x from other and group
chmod a+r 1.sh               # add everyone(all) read permission
chmod +x 1.sh          # add everybody(all) execute permission
umask                    # umask 0444 : the permissions that the user does not want to be given (default :0022 or 0002)
stat 1.js                # view Inode details
```

## SUID and SGID File Permission  ?????????????


### System Monitoring Commands
```bash
top
vmstat
free
iostat
netstat
iotop
htop
atop
nmon
iftop
sysdig
sar
```

### Disk usage

```bash
df
du
```
### Memory Usage

```bash

```
### Processes

```bash

```
### services

```bash

```
### systemd

+ systemd is a modern `system and service manager` for Linux. It is used to `bootstrap the user space` and `manage system processes` after the kernel is booted.
+ It replaces older init systems like `SysVinit` and `Upstart`, and is now the default init system for most major Linux distributions.
+ systemd is responsible for `Init System` which means Starts and stops services during boot and shutdown.
+ systemd uses `systemctl` as a "service manager"
+ systemd uses `journald` or `journalctl` for "logging"
+ systemd is a collection of components like `systemd-analyze`, `systemd-resolved`, `systemd-networkd`, `systemd-logind` and so on.

```bash
ps -p 1 -o comm=                   # chick if you use systemd
ls /lib/systemd/system               # the place of components
ls /usr/lib/systemd/system           # user installed packages locally
ls /run/systemd/system
ls /etc/systemd/system
```

##### systemctl
+ managing systemd services on Linux

```bash
# start and stop services
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx
systemctl reload-or-restart nginx       # Reload if possible, else restart
# status and logs
systemctl status nginx                  # Service status (active, failed, etc.)
systemctl is-active nginx                 # check if running
systemctl is-failed nginx                   # check if failed
journalctl -u                               # View logs from a service
journalctl -xe                                # show latest logs
# startup
systemctl enable nginx                   # start on boot
systemctl disable nginx                   # do not start on boot
systemctl is-enable nginx                   # Check if enabled
systemctl reboot                            # 
systemctl poweroff
systemctl halt                              # Halt system (no power off)
systemctl get-default                       # go to default graphic
systemctl daemon-reexec                       # Restart systemd itself
# list
systemctl list-units --type=service          # Active services
systemctl list-unit-files --type=service       # All services and their states
systemctl list-timers                         # Show all active timers
```

###  HTTP Requests
```bash
curl example.com                                                  # get body
curl https://httpbin.org/                                               # dummy api
curl https://reqres.in/                                              # dummy api
curl -i  example.com                                                  # -i or --include for HTTP headers beside body
curl -L example.com                                                # follow redirect with -L or --location
curl -v example.com                                               # verbose mode 
curl -o localfile.zip http://example.com/file.zip                         # download a file with  -o = output name
curl -o 1.txt example.com                                         # Output to a text file
curl -X PUT https://api.example.com/resource/123                         # GET / POST / PUT / DELETE
curl -d "name=John&age=30" https://httpbin.org/post                       # send data WITH -d or --data
curl -F "file=@report.pdf" https://example.com/upload                       # upload a file
curl -H "Content-Type: application/json" \                                 # send json with header
     -d '{"name":"John","age":30}' \                                  
     https://httpbin.org/post
wget example.com                                            # download the page in index.html in current diretory
wget example.com/file.png                                  # download a file in current direcoty
wget -o new_name.png example.com/file.png                                  # download with custome name
```


### Network Connectivity Commands
```bash
ping example.com            # Send infinite ping requests to a hostname or ip using the ICMP protocol
ping -c 4 example.com         # send 4 requests -c for count
ping -i 0.5 example.com      # Send packets every 0.5 seconds
ping -s 1000 example.com       # Send packets of 1000 bytes
ping -W 2 example.com          # Wait up to 2 seconds for each reply
sudo ping -f example.com          # Flood Ping :Sends packets as fast as possible (be careful)
ping -q -c 4 example.com       # Quiet output; show summary only
ping -4 example.com                # use ipv4
ping -6 example.com               # use ipv6
ping $HOSTNAME         # Ping your own machine by name
route
traceroute
netstat
telnet
mail
arp
w
iw
ngrep
```
### DNS

```bash
dig example.com            # dig (Domain Information Groper) Show complete DNS information
dig example.com A           # IPv4 address
dig example.com AAAA        # IPv6 address
dig example.com MX         # Mail exchange servers
dig example.com NS         # Nameservers
dig example.com +short     # Shortened output
dig example.com +noall +answer   # Only show the answer section
dig example.com +noquestion      # No question section in output
dig -x 8.8.8.8             # Reverse DNS lookup (PTR)
dig @8.8.8.8 openai.com    # Use specific DNS server (e.g., Google)
nslookup                               # Interactive Mode  [1.exit 2. server 8.8.8.8]
nslookup -query=A example.com         # A (IPv4 address)
nslookup -query=AAAA example.com      # AAAA (IPv6 address)
nslookup -query=MX example.com        # Mail servers
nslookup -query=NS example.com        # Nameservers
nslookup -query=CNAME www.example.com # Canonical name
nslookup -query=TXT example.com       # TXT records (SPF, DKIM, etc.)
nslookup 8.8.8.8                          # Reverse DNS Lookup (PTR)
cat /etc/resolv.conf                          # Check DNS Configuration and lists nameservers
host example.com                     # Show the IPv4 and IPv6 addresses
systemd-resolve --status             # ?????????
drill                                # ???????
```


### network configuration
```bash
ip addr                     # List IP addresses on the system
ip a                          # Short form for addr
ip route show               # Show IP addresses to router
ip r                                 # Short form for route
hostname
ifconfig
iwconfig
ifup
ifdown
ifquery
netplan
nmcli
dhcclient
```

### Network Information Commands
```bash
finger
whois
```

### Remote Access Commands
```bash
ssh
scp
rsync
sftp
```

### Network Testing
```bash
hping
mtr
nc
ncat
netcat
smokeping
socat
speedometer
speedtest-cli
ss
tracepath
```
### Network Analysis
```bash
tcpdump
nmap
bmon
bmon-ng
iftop
iperf
iptraf-ng
nethogs
vmstat
```

### Firewall

#### iptables
1. iptables: is a tool to configure the Linux kernel firewall
2. table: A group of rules for specific purposes (e.g., filter, nat)
3. chain: A set of rules applied in order (e.g., INPUT, OUTPUT, FORWARD)
4. rule: A single condition and action (e.g., "drop all traffic from 1.2.3.4")
5. target: The action taken if the rule matches (e.g., ACCEPT, DROP, REJECT)

#### firewalld
1. firewalld: firewalld is a zone-based dynamic firewall manager (CLI+GUI)
2. firewalld zone: 	A set of rules applied to network interfaces
3. firewalld service: Predefined sets of ports/protocols (e.g., SSH, HTTP)
4. firewalld runtime: 	Temporary changes (reset after reboot)
5. firewalld permanent: Saved changes (persist across reboots)


```bash
ufw
iptables
nftables
sudo iptables -L -v                                         # List all rules
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT           # Allow SSH
# firewalld
sudo systemctl start firewalld
sudo systemctl enable firewalld
firewall-cmd --state                     # Check if firewalld is running
firewall-cmd --list-all                 # All active settings in default zone
firewall-cmd --list-all-zones           # All settings across zones
firewall-cmd --get-services             # All predefined services
firewall-cmd --get-default-zone         # Default zone
firewall-cmd --get-active-zones         # Show interfaces and their zones
firewall-cmd --zone=public --list-all   # Show rules for a zone
sudo firewall-cmd --zone=public --add-service=http             # Allow http service in runtime
sudo firewall-cmd --zone=public --add-service=http --permanent            # Allow a http service permanent
sudo firewall-cmd --remove-service=ssh                   # Remove ssh service in runtime
sudo firewall-cmd --remove-service=ssh   --permanent                   # Remove ssh service in permanent
sudo firewall-cmd --reload                             # # Apply permanent rules
sudo firewall-cmd --complete-reload                 # Full reload
sudo firewall-cmd --zone=public --add-port=8080/tcp
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent
sudo firewall-cmd --zone=public --remove-port=8080/tcp --permanent
```

### Security Commands

```bash
snort
wireshark
aria2
ethtool
```

### VPN Tools
```bash
OpenVPN
WireGuard
vpnc
openconnect
```
### other ??????????

```bash
shred
atime, mtime, ctime
bzip2, bunzip2, gunzip
flatpak, snap  ---> Universal Linux Packages
uanme, hostname
ulimit
modprobe
dmesg
findmnt
watch
sleep
xargs
shadow
 useradd, usermod, userdel, finger
groupadd, groupdel, groupmod
 uptime, last
 Octal (Numeric) Notation
 chgrp,chmod, chown, umask

```
### firewall

### Shortcut keys


## encrypt
```bash
gpg -c FILE         # encrypt file
gpg FILE.gpg        # decrypt file
```


## Partition
```bash
fdisk -l               # list of partitions and drivers
parted 
gparted
```

## Mounting
```bash
mount
```
## Hardware

```bash
lsusb                  # List USB devices
lspci                  # List PCI hardware
lshw                   # List all hardware
lscpu                   # List all cpu
lsblk                   # List block devices
lsscit                   # List scsi/sata
```

















