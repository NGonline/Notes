[TOC]

# Introduction
- This is the Unix philosophy: Write programs that do one thing and do it well. Write programs to work together. Write programs to handle text streams, because that is a universal interface.

# Text Processor

## awk
- `awk '{print $1, $4}' f` prints the first and fourth colume of `f`
- `awk '{printf "%s\t%s\n",NR-1,$0}' xxx > ind` add row index to xxx (begin from 0) and redirect to ind
- `awk 'BEGIN{FS="[|]+";OFS="\t";}'` uses regex as input seperator, and OFS is the output seperator
- `awk 'FNR==m{print}' filename` shows the m-th line
- `awk 'FNR&gt;=j &amp;&amp; FNR &lt;=k{print}' filename` shows the j-th to the k-th lines
- multiple files:
 - `awk 'NR==FNR{...}NR>FNR{...}' file1 file2 或 awk 'NR==FNR{...}NR!=FNR{...}' file1 file2`
 - `awk 'NR==FNR{...;next}{...}' file1 file2`
- `awk 'NR==FNR{a[$1]=$2;next}{print $0,a[$2]}' f1 f2`, `a` is a hash map

## less
- If you want to view really large file, you will want to view it in a program which is as fast as possible.
- Often you would not want to accidentally change something in a file.
- --ch<ENTER><ENTER> or --chop-long-lines will enable horizontal scrolling.
- Using j and k for scrolling, and q for exit. <SPACE> is used to scroll a whole page.
- &something<ENTER> shows only those lines which contains something. The filter can be discarded by &<ENTER>
- / is used for search.
- By default, man is viewed with less.
- `-S` causes lines longer than the screen width to be chopped rather than folded.

## grep
- To print lines matching a pattern. It will search the standard input if no files (or a single `-`) are named. e.g. `command |grep XXX`
- `-v` choose the lines without pattern
- `-i` ignores case distinction
- `-m NUM` stop reading a file after NUM matching lines
- '{' and '(' need to escape in the regex
- `grep` patterns are matched against individual lines so there is no way for a pattern to match a newline found in the input. `\n` separate the patterns around it and they are or'd together:`grep $'pattern1\npattern2'` is equivalent to `grep 'pattern1\|pattern2'`, and `grep $'abc\n'` matches every line.
- "\d", "\D", "\s", "\S", "\w", "\W" are common extensions of basic regex. So they are not supported in the default `-G` mod. You need to use `-P`.
- `-E` option supports regex for "+", "?", "|", "()", with some other improvements.
- `-r` read all files under each directory recursively.

## sed
- `sed 's/pattern/replace/option'` is a stream editor for filtering and transforming text.
- `sed -ne 'mp' filename` shows the m-th line.
- `sed -ne 'j,kp' filename` shows the j-th to the k-th lines.
- `sed '1!G;h;$!d' t.txt` reverse the lines of a file.

## vim

### Basic 
:w --- write
:q --- quit
:q! --- quit without saving

### Movement
h,j,k,l

### Inserting
i --- insert before the current character
a --- insert after the current character
o --- insert a line below the current line

### Deleting
x --- delete the current character
dd --- delete the current line

## regex
- `\<` matches begin of a word, `\>` matches end of a word.
- `?` means literal "?" without escape (unlike java).
- There is no non-greedy regex in sed, grep, or vi:
```
// cancel all tags in html
sed 's/<[^>]*>//g' xxx.html // true
sed 's/<.*\?>//g' xxx.html  // will delete whole lines as <a>b<\a>
```

# Bash
- Any command which you type is not executed directly, but expanded firstly and only then executed. For example, when you type ls * the star * is expanded into list of all files in current directory.
- Command output can be used as variables using `$(command)`.
- Hot keys in bash:
| Hot key | effect |
| - | - |
| Ctrl + p | Previous command (Up arrow) |
| Ctrl + n | Next command (Down arrow) |
| Ctrl + a | Go to the beginning of the line (Home) |
| Ctrl + e | Go to the End of the line (End) |
| Alt + b | Back (left) one word |
| Alt + f | Forward (right) one word |
| Ctrl + f | Forward one character |
| Ctrl + b | Backward one character |
| Ctrl + xx | Toggle between the start of line and current cursor position |
| Ctrl + l | Clear the Screen, similar to the clear command |
| Ctrl + u | Cut/delete the line before the cursor position |
| Ctrl + k | Cut the Line after the cursor to the clipboard |
| Alt + Del | Delete the Word before the cursor |
| Alt + d | Delete the Word after the cursor |
| Ctrl + w | Cut the Word before the cursor to the clipboard. |
| Alt + t | Swap current word with previous |
| Ctrl + t | Swap the last two characters before the cursor (typo) |
| Esc + t | Swap the last two words before the cursor |
| ctrl + y | Paste the last thing to be cut (yank) |
| Alt + u | UPPER capitalize every character from the cursor to the end of the current word |
| Alt + l | Lower the case of every character from the cursor to the end of the current word |
| Alt + c | Capitalize the character under the cursor and move to the end of the word |
| Alt + r | Cancel the changes and put back the line as it was in the history (revert) |
| ctrl + _ | Undo |
| Ctrl + s | Stop output to the screen (for long running verbose commands), XOFF ASCII character |
| Ctrl + q | Allow output to the screen (if previously stopped using command above), XON ASCII character |
| Ctrl + r | Recall the last command including the specified character(s) (equivalent to : vim ~/.bash_history) |
| !! | Repeat last command |
| !abc | Run last command starting with abc |
| !abc:p | Print last command starting with abc |
| !$ | Last argument of previous command |
| !* | All arguments of previous command |
| ^abc­^­def | Run previous command, replacing abc with def |
| Ctrl + d | Send an EOF marker, unless disabled by an option, this will close the current shell (EXIT) |
| Ctrl + z | Send the signal SIGTSTP to the current task, which suspends it. To return to it later enter fg 'process name' (foreground). |

- shell常见通配符：
| 字符 | 含义 | 实例 |
|-|-|-|
| * | 匹配 0 或多个字符 | a*b  a与b之间可以有任意长度的任意字符, 也可以一个也没有, 如aabcb, axyzb, a012b, ab。|
| ? | 匹配任意一个字符 | a?b  a与b之间必须也只能有一个字符, 可以是任意字符, 如aab, abb, acb, a0b。|
| [list] | 匹配 list 中的任意单一字符 | a[xyz]b   a与b之间必须也只能有一个字符, 但只能是 x 或 y 或 z, 如: axb, ayb, azb。|
| [!list] | 匹配 除list 中的任意单一字符 | a[!0-9]b  a与b之间必须也只能有一个字符, 但不能是阿拉伯数字, 如axb, aab, a-b。|
| [c1-c2] | 匹配 c1-c2 中的任意单一字符 | [0-9] [a-z] a[0-9]b 0与9之间必须也只能有一个字符 如a0b, a1b... a9b。|
| {string1,string2,...} | 匹配 sring1 或 string2 (或更多)其一字符串 | a{abc,xyz,123}b    a与b之间只能是abc或xyz或123这三个字符串之一。
需要说明的是：通配符看起来有点象正则表达式语句，但是它与正则表达式不同的，不能相互混淆。把通配符理解为shell 特殊代号字符就可。而且涉及的只有，*,? [] ,{} 这几种。
- Bash commands can be executed consecutively: ```commmand1 && command2```


## Brace Expansion
- Bash just takes a parameter before the brace, and adds everything which is in braces, separated by commas, to this parameter consequentially: something{1,2,3}` is exactly `something1 something2 something3`

## Environment Variables
- Tthere are many variables in your shell, many of which are set up automatically, and every time you run a program, some of them are passed to this program:
 - Local shell variables are just for your current shell. They can be listed by **set**, which is processed by bash itself;
 - Other variables are passed to every program you launch from your current shell. They are called environment variables and can be listed by **env** program.
 - By convention, environment variables have UPPER CASE and shell variables have lower case names.
- `foo='xxx'` will put `foo='xxx'` into local shell variable, and `export foo` makes variable `foo` available to all programs you execute from the current shell.

## |
- `command 1 | command 2` will take the result of `command 1` as the input of `command 2`

## .*
- Note that is includes the `..` directory. Do not use this construct with any command, especially rm, ever! Chances are, you will accidentally cripple your system by deleting wrong files or changes access right to them.

## &
- If a command is terminated by the control operator `&`, the shell executes the command in the background in a subshell

## ' and "
- `"` will expand the variable and command in "\`" of after "$", `'` will not
- `"` allows backslash escape, `'` doesn't. The backslash retains its special meaning only when followed by one of the following characters: "$", "`", "\"", "\", or "newline".
- `$''` are treated as ANSI-C quoting, with more bachslash-escaped characters.

## Redirection
- Almost every Linux program opens at least these 3 files when started: stdin, stdout, stderr. This is how it reads:
```
Start Program 1
    Start reading data from keyboard
    Start writing errors to display
    Start Program 2
        Start reading input from Program 1
        Start writing errors to Display
        Start Program 3
            Start reading input from Program 2
            Start writing errors to Display
            Start writing data to Display
```
- stdin is 0, stdout is 1, stderr is 2: `&> file` redirect stdout and stderr to file; `2> file` redirect stdout to file; `2>&1` redirect stderr to stdout; `>&file` redirect stdout to file
- `command > file` redirect the output and rewrite the file
- `command >> file` append the file
- `command < file` read the parameter from file

## at
- Reads from stdin and executes the command at a specified time. `at now+2 hours`, `at 17:30 2/24/99`, `at 2:30 PM next month` ...
- `atq` lists pending jobs; `atrm` removes a job; `batch` executes commands when system in idling.
- The atd process must have been started (`/etc/init.d/atd start`) for `at` to work.

## cat
- `cat` simply copy standard input to standard output
- `cat f - g` output f's contents, then standard input, then g's contents
- `cat -n f` output f's contents with numbering all output lines
- `cat -v f` show nonprinting

## cd
- `cd -` goes back to the previous directory.

## chmod
- Change the file mode. The three part of the mode string correspond to the owner, the user in the same group as the owner, and the other users.
- `chmod [who] [+-=mode]`. `who` can be `u` for owner, `g` for those in the same group, `o` for the others, and `a` for all. `mode` can be `r` or `4` for read, `w` or `2` for write, and `x` or `1` for excute.
- `chmod 751 file`
- `-R` means recursively.

## chown
- Change the file owner and group.

## cp
- `cp -v f g` explain what is being done
- `-i` will ask before overwriting
- `-f` won't break if one file in the file list inexists
- `-a` copy recursively and keeps the mode

## crontab
- `crontab -e` to edit the config file. `minute, hour, day, month, week command`
- `crontab file` install crontab for the current user, overwriting existing one in the process.
- `crontab -l` prints out current crontab.
- `30 7 * * * command` each 7:30 am.
- `*/1 * * * * command` every minute.
- `*/3 6-8 * * * command` every three minutes between 6:00 am and 8:00 am.
- `L` means the last possible value. `0 8 L * ?` every 8:00 am at the last day of each month.
- You can't specify values on both day and week since that is ambiguous. `?` means whatever on these fields. If you specify a value to one of these two fields, the other must be `?`.
- **/etc/crontab** is system-wide cron configuration file. **/var/spool/cron/crontabs/** is the directory for storing user configuration files.

## curl
- To transfer data from or to a server, using one of the supported protocols (HTTP, HTTPS, FTP, FTPS, SCP, SFTP, TFTP, DICT, TELNET, LDAP or FILE).

## cut
- Print selected parts of lines: `cut -d' ' -f2-` deletes the first colume

## date
- `date -d "2012-04-10 -1 day" +%Y-%m-%d` returns 2012-04-09. So will `date -d "2012-04-10 yesterday"`
- `%x` keeps leading zeros, `%-x` deletes leading zeros

## dd
- Copy a file, converting and formatting according to the operands:
 - if=FILE : read from FILE instead of stdin
 - of=FILE : write to FILE instead of stdout
 - bs=BYTES : read and write BYTES bytes at a time
 - count=BLOCKS : copy only BLOCKS input blocks

## df
- `df -h dir` show the disk usage of the current filesystem

## du
- show the disk usage of the directory

## diff
- `diff file1 file2` shows the difference of file2 to file1
- `diff -u` shows the lines of unified context.

## du
- show size of file. `-h` print the size in human readable format

## echo
- `echo Hello, $LOGNAME!` tells your shell to output a string `Hello, $LOGNAME!`, substituting `$LOGNAME` with environment variable $LOGNAME which happens to contain your login.
- `echo 'echo Hello, $LOGNAME!' >> .profile` add a line to **.profile**. Note that it will add a '\n' at first.
- `echo 'xxx' > f` replace instead of pending
- `echo -e` enables backslash escape. The default setting disables this. `echo "a\tb"` will actually print `a\tb`

## env
- Run a program in a modified environment.
- It can prints out all list of your environment variables which are passed to any program you execute.
```
bash~: foo="hello world"
bash~: set | grep foo
foo='hello world'
bash~: env | grep foo
bash~: export foo
bash~: env | grep foo
foo=hello world
```

## export
- Export names to the environment of subsequently executed commands. The child process will inherit the names, but changes of them in the child processes will not affect the father process.
- `=` without `export` will only affects the current process.
- `export` or `export -p` prints a list of all names that are exported in this shell.

## fg
- Bring a program to foreground. It will bring last suspended program to foreground if called without parameters.

## file
- determine file type

## find
- `find /path/ -name xxx`
- `-iname` ignore upper or lower cases.
- `-mmin n` finds files whose data were last modified n minutes ago.

## free
- Display amount of free and used memory in the system

## fsck
- Check partition for errors

## gzip
- `gzip` compress
- `gzip -d` unpack
- `-c` writes output on a standard output, keep original files unchanged.

## history
- `history -w` writes all your command history to .bash_history file. Normally this is done at the end of your session, when you close it by typing exit or by pressing <CTRL>+D.
- If you prepend a command with space it won't be saved in history.

## iconv
- `iconv -f a -t b` change charset from a to b

## info
- <UP>, <DOWN>, <LEFT>, <RIGHT> allows you to scroll text. <ENTER> open the link under cursor. Links start with * . <TAB> — jump to the next link in document.
 - u --- go up one level.
 - p --- go to previous page, just like in browser.
 - n --- go to next pages.
 - q --- close info.

## jobs
- List all background programs.

## join
- Join lines of two files on a common field, and write a line to standard output for each pair with identical join fields. Note that both files must be sorted on the join fields.
- `join -t $'\t -o 1.1 2.2 1.2 -1 2 file1 -2 1 file2' outputs the first and second column of file1 and the second column of file2, of the join result of the second column of file1 with the first column of file2.
- `-a1` is left outer join, `-a2` is right outer join, `-a1 -a2` is full outer join. But the null fields will not be replaced by empty character:
```
-bash-$ cat a
1 2
-bash-$ cat b
2 3
-bash-$ join -a1 -a2 a b
1 2
2 3
```

## kill
- Send the specified signal to the specified process or process group. `kill -2` sends SIGINT, `kill -3` sends SIGQUIT, `kill -15` sends SIGTERM (default), `kill -9` sends SIGKILL, `kill -18` sends SIGCONT, `kill -19` sends SIGSTOP.
- SIGQUIT：On POSIX-compliant platforms, SIGQUIT is the signal sent to a process by its controlling terminal when the user requests that the process perform a core dump. SIGQUIT can usually be induced with Control-\. On Linux, one may also use Ctrl-4 or, on the virtual console, the SysRq key.
- SIGTERM：SIGTERM is the default signal sent to a process by the `kill` or `killall` commands. It causes the termination of a process, but unlike the SIGKILL signal, it can be caught and interpreted (or ignored) by the process. Therefore, SIGTERM is akin to asking a process to terminate nicely, allowing cleanup and closure of files. For this reason, on many Unix systems during shutdown, init issues SIGTERM to all processes that are not essential to powering off, waits a few seconds, and then issues SIGKILL to forcibly terminate any such processes that remain.
- SIGINT：On POSIX-compliant platforms, SIGINT is the signal sent to a process by its controlling terminal when a user wishes to interrupt the process. SIGINT is sent when the user on the process' controlling terminal presses the interrupt the running process key — typically Control-C, but on some systems, the "delete" character or "break" key.
- SIGKILL：On POSIX-compliant platforms, SIGKILL is the signal sent to a process to cause it to terminate immediately. When sent to a program, SIGKILL causes it to terminate immediately. In contrast to SIGTERM and SIGINT, this signal cannot be caught or ignored, and the receiving process cannot perform any clean-up upon receiving this signal.

## kinit
- obtain and cache Kerberos ticket-granting ticket

## klist
- List the Kerberos tickets held in a credentials cache, or the keys held in a keytab file.

## ln
- `ln t l` creates a link to `t` with the name `l`. It differs from `cp` in not copying the contents of the file. Both files point to the same physical blocks. If one of them is deleted, the other still works. If all links are removed, the file content will be removed.
- `ln -s` creates soft link. If the origin file is deleted, the link file will still exists but empty.

## locale
- Prints out all locale variables which are used by programs for setting up number, address, phone format, etc. according to the format of the specified country.
- Variable `LANG` is used by programs to determine which language to use when interacting with you.

## logger
- Make entries in the system log

## ls 
- Every file starts with dot is hidden. **-a** shows all files including the hidden ones. **-l** prints file list in **long** format: permissions, owner, group, size, timestamp (normally modification time) and filename.
- Note that **-l** shows the size of the directory in *blocks*.
- `ls -tr` mean that file list is sorted by time in reverse direction. This means that most recently created and modified files are printed last.
- `ll` is a common alias to `ls -l`
- `-i` prints the index number of each file, can be used to determine symbolic link

## lzop
- `lzop file` compress, `lzop -x file` extract

## man
- `man -K` will search for text in all manual pages. This is a brute-force search. The result will be shown one manual after another.
- `man -k` searches something in man pages descriptions.
- `man -wK` searches something in man page bodies.

## mv
- Notice that it overwrites the destination file without asking.
- `mv xxx xxx xxx xxx destDir` move sources to one destination
- `-i` will ask before overwriting
- `-f` won't break if one file in the file list inexists

## name
- Print certain system information.

## nl
- Write each FILE to standard output, with line numbers added.
- `nl -s $'\t' xxx` adds '\t' after line number.

## nohup
- `nohup command parameter &`

## ps
- `aux`
- `ps -eo pid,tty,user,comm,stime,etime`
- `--sort` specifies sorting order. Sorting syntax is [+|-]key[,[+|-]key[,...]]. For example: `ps jax --sort=uid,-ppid,+pid`
- `ps -u | grep [j]ava` will match all process with "java" but not the command itself.
- In the result of `ps -u`:
 - TIME: the execution time --- the time the process actually used the CPU(s), not including the time the process waits for I/O events.
 - RSS: resident set size, the non-swapped physical memory that a task has used (in kiloBytes). (alias rssize, rsz).
 - VSZ: virtual memory size of the process in KiB (1024-byte units). Device mappings are currently excluded; this is subject to change. (alias vsize).
- `grep -P "31.22038\t121.41149" fingerprints` or `grep '1.22038'$'\t''121.41149'" fingerprints"` matches '\t'
- `ps -ax --forest` will show ASCII-art process hierarchy.

## pwd
- Print out the current working directory.

## rm
- Beware, always use `-v` key.

## rename
- `rename from to file` will rename the specified files by replacing the first occurrence of from in their name by to.
- `rename .htm .html *.htm` will fix the extension of your html files.

## rz
- 

## scp
- `scp -P 58422 convertid.jar data_algo@10.1.1.164:/data/home/data_algo/runchao.mao`
- `sshpass -p "password" scp -r user@example.com:/some/remote/path /some/local/path` can avoid typing password.

## set
- Prints out all list of your shell variables (`printenv` lists all environment variables).

## shuf
- generate random permutation of lines to standard output

## sort
- start from 1
- Note that "\t" doesn't work fine in some implementation of `sort`. `sort -t $'\t' -o Density10m -k 2,2rn part-00000` only works for bash. The dollar sign tells bash to use ANSI-C quoting. `"Ctrl-v<tab>"` also works.

## sshpass
- Provide noninteractive ssh password. `sshpass -p password ssh -p58422 -o StrictHostKeyChecking=no name@0.0.0.0 "command"`

## sudo
- `sudo -s` executes root (superuser) shell

## tac
- Concatenate and print files in reverse.

## tar
- `tar cf log.tar file.log` compress
- `tar xf file.tar` unpack
- same usage as `jar`

## tail
- `tail -n 5 .profile` prints out exactly five last lines from .profile file

## tcpdump
- Prints out a description of the contents of packets on a network interface that match the user-defined boolean expression.
- `tcpdump -XX -r` read packets and print headers and datas of each packet.

## tee
- Copy standard input to each file and also to standard output.

## test
- Check file types and compare values: `test "abc" = "def" ;echo $?`
- Equivalent to `[]`

## time
- `time command [arguments...]`. When command finishes, time writes a message to standard error giving timing statistics about this program run.
- The statistics include:
 - real time between invocation and termination
 - the user CPU time: time spent on user space
 - the system CPU time: time spent on kernel space (system call)
- `-p` format the output, default is "read %f\nuser %f\nsys %f\n"

## top
- VIRT: virtual memory usage
- RES: resident memory usage
- SHR: shared memory
- DATA: the amount of physical memory devoted to other than executable code

## touch
- Updates timestamp to current date and time. This means that all time attributes, `st_atime`, `st_mtime` and `st_ctime` are set to current date and time. You may become sure of this by `stat`.

## tr
- Translate or delete characters: `tr -d 'x'` delete all x, `tr '\n' ' '` replace all `'\n'` with `' ' `
- `-s SET` squeezes repeats: replace each input sequence of a repeated character that is in SET into a single occurrence.

## uniq
- Filter adjacent matching lines, usually used after `sort`.

## w
- Show who is logged on and what they are doing.

## wc
- Reads either standard input or a list of files, and generates: newline count (**-l**), word count (**-w**), byte count (**-c**), length of the longest line (**-L**).
- `cat filename | wc -l` will eliminate the filename in the output.

## write
- `echo xxx | write username` sends a message to another user.

## xargs
- xargs reads items from the standard input, delimited by blanks (which can be protected with double or single quotes or a backslash) or newlines, and executes the command (default is /bin/echo) one or more times with any initial-arguments followed by items read from standard input. Blank lines on the standard input are ignored.

## zip
- `zip -r xxx.zip file1 file2 ...`
- `unzip xxx.zip`
- jar is packed by zip

# Files

- A computer file is a block of arbitrary information, or resource for storing information, which is available to a computer program and is usually based on some kind of durable storage. A file is durable in the sense that it remains available for programs to use after the current program has finished. Computer files can be considered as the modern counterpart of paper documents which traditionally are kept in offices' and libraries' files, and this is the source of the term.
- **man stat**tells us that file is an object which has the following properties additionally to information it contains:
```
struct stat {
   dev_t     st_dev;     /* ID of device containing file */
   ino_t     st_ino;     /* inode number */
   mode_t    st_mode;    /* protection */
   nlink_t   st_nlink;   /* number of hard links */
   uid_t     st_uid;     /* user ID of owner */
   gid_t     st_gid;     /* group ID of owner */
   dev_t     st_rdev;    /* device ID (if special file) */
   off_t     st_size;    /* total size, in bytes */
   blksize_t st_blksize; /* blocksize for file system I/O */
   blkcnt_t  st_blocks;  /* number of 512B blocks allocated */
   time_t    st_atime;   /* time of last access */
   time_t    st_mtime;   /* time of last modification */
   time_t    st_ctime;   /* time of last status change */
};
```
- **Ctrl+D** produces EOF character (ASCII 04).

## .bash_profile
- Bash reads commands from it when invoked as the login shell. It usually loads `.bashrc`.
- `/etc/profile` is global, while `.bash_profile` only works for one user.
- After login, bash will read these files in order:
 - /etc/profile
 - ~/.bash_profile
 - ~/.bash_login
 - ~/.profile

## .bashrc
- When you start bash as an interactive shell (i.e., not to run a script), it reads `~/.bashrc` (except when invoked as a login shell, then it only reads `~/.bash_profile`).
- `/etc/bashrc` is global, while `.bashrc` only works for one user.

## Special Devices

### /dev/stdin, /dev/stdout, /dev/stderr
- `cat > testf < /dev/stdin` prints your input and write it to `testf`
- `cat testf > /dev/stdout | grep xxx` redirects testf to stdout, and passed by pipe to grep
- `cat testf > /dev/stderr | grep xxx` prints testf, and since it's not stdout, nothing will be passed to grep through pipe

### /dev/null
- It's a special device file that discards all data written to it but reports that the write operation succeeds.

### /dev/zero
- Once you read it, it will provide infinite number of NULL (ASCII 00) character.
- It's usually used to create a fixed-size of empty file:
```
# create a file of 1 MB, filled with 0
dd if=/dev/zero of=foobar count=1024 bs=1024
```

### /dev/full
- Once you write to it, it will return the error code `ENOSPC` (meaning "No space left on device"), and provide an infinite number of null characters to any process that reads from it (similar to /dev/zero).
- This device is usually used when testing the behaviour of a program when it encounters a "disk full" error.

### /dev/fd
- Opening the files in the directory is equivalent to copy those file descriptor: `fd=open("/def/fd/0",mode);` is equivalent to `fd=dup(0)`.
```
$> cat a
aaa
$> cat b
bbb
$> echo abc | cat a b
aaa
bbb
$> echo abc | cat a - b
aaa
abc
bbb
$> echo abc | cat a /dev/fd/0 b
aaa
abc
bbb
```

## Directory
- In Linux all same types of files are installed in the same locations. For example, executable files from all programs are installed in **/usr/bin**, documentation from all programs in **/usr/share/doc** and so on.
- Which files go where is defined a document called FHS standard, you can view it by invoking `man 7 hier`.
 - / --- This is the root directory. This is where the whole tree starts.
 - /bin --- This directory contains executable programs which are needed in single user mode and to bring the up or repair it.
 - /boot --- Contains static files for the boot loader. This directory only holds the files which are needed the boot process. The map installer and configuration files should go to /sbin and /etc.
 - /dev --- Special or device files, which refer to physical devices. See mknod(1).
 - /etc --- Contains configuration files which are local to the machine.
 - /home --- On machines with home directories for users, these are usually beneath this directory, directly o The structure of this directory depends on local administration decisions.
 - /lib --- This directory should hold those shared libraries that are necessary to boot the system and to run commands in the root file system.
 - /media --- This directory contains mount points for removable media such as CD and DVD disks or USB sticks.
 - /mnt --- This directory is a mount point for a temporarily mounted file system. In some distributions, /mnt contains subdirectories intended to be used as mount points for several temporary file systems.
 - /proc --- This is a mount point for the proc file system, which provides information about running processes and the kernel. This pseudo-file system is described in more detail in proc(5).
 - /root --- This directory is usually the home directory for the root user (optional).
 - /sbin --- Like /bin, this directory holds commands needed to boot the system, but which are usually not executed by normal users.
 - /srv --- This directory contains site-specific data that is served by this system.
 - /tmp --- This directory contains temporary files which may be deleted with no notice, such as by a regular job or at system boot up.
 - /usr --- This directory is usually mounted from a separate partition. It should hold only sharable, read-only data, so that it can be mounted by various machines running Linux.
 - /usr/bin --- This is the primary directory for executable programs. Most programs executed by normal users which are not needed for booting or for repairing the system and which are not installed locally should be placed in this directory.
 - /usr/local --- This is where programs which are local to the site typically go.
 - /usr/share --- This directory contains subdirectories with specific application data, that can be shared among different architectures of the same OS. Often one finds stuff here that used to live in /usr/doc or /usr/lib or /usr/man.
 - /usr/share/doc --- Documentation about installed programs.
 - /var --- This directory contains files which may change in size, such as spool and log files.
 - /var/log --- Miscellaneous log files.
 - /var/spool --- Spooled (or queued) files for various programs.
 - /var/tmp --- Like /tmp, this directory holds temporary files stored for an unspecified duration.

# Job Control
- Linux is a multitasking operating system, and bash surely has tools to control multiple jobs execution for you:
| command | effect |
| - | - |
| Ctrl + z | place currently running program in the background |
| jobs | list all background programs |
| fg | bring a program to foreground |
| Ctrl + c | stop execution of currently running programs at once |

## Exit Status
- In Linux there is a standard mechanism for getting information from child process to parent process, and this mechanism is called exit status, or return code. When program encounters no errors during execution it returns zero, 0. If some errors did occur, this code is non-zero. That simple. In Bash this exit code is saved into `?` environment variable, which as you know now can be accessed as `$?`.

# Processes
- Outline of what happens when you running `ls`:
1. Bash:
 - locates ls on hard disk
 - forks itself to Bash clone, i.e. clones itself to the new location in memory
 - becomes parent process to Bash clone
 - control is now passed to the Bash clone
2. Bash clone
 - becomes child process to Bash
 - preserves parent Bash process environment
 - knows that it is a clone and reacts accordingly: overwrites itself with ls
 - control is now passed to ls
3. ls
 - prints out a directory listing for you or returns an error
 - returns exit code
 - control is now passed to Bash
4. Bash
 - assigns ls exit code to `?` variable
 - waits for your input
 
## Process States
- The process state codes of `ps`:
 - D: uninterruptible sleep (usually IO). Process is busy or hung, and does not respond to signals.
 - R: running or runnable (on run queue).
 - S: interruptible sleep (waiting for an event to complete). Terminal processes and Bash are often in this state.
 - T: stopped, either by a job control signal or because it is being traced.
 - W: paging (not valid since the 2.6.xx kernel).
 - X: dead (should never be seen).
 - Z: zombie process, terminated but not reaped by its parent.
 - <: high-priority (not nice to other users).
 - N: low-priority (nice to other users).
 - L: has pages locked into memory (for real-time and custom IO).
 - s: is a session leader. Related processes in Linux are treated as a unit, and have shared Session ID (SID).
 - l: is multi-threaded (using CLONE_THREAD, like NPTL pthreads do).
 - +: is in the foreground process group. Such processes are allowed to input and output to the tty.
- ![Process States](http://www.linux-tutorial.info/Linux_Tutorial/The_Operating_System/The_Kernel/Processes/procflowa.gif "Process States")
- Sending signals is a way to communicate with processes. Possible signals of `kill`:
| Signal | Num | Action | Description |
| - | - | - | - |
| 0 | 0 | n/a | exit code inicates if a signal may be sent |
| HUP | 1 | exit | hangup on controlling terminal or parent process died |
| INT | 2 | exit | interrupt from keyboard |
| QUIT | 3 | core | quit from keyboard |
| ILL | 4 | core | illegal instruction |
| TRAP | 5 | core | trace/breakpoint trap |
| ABRT | 6 | core | abort signal from abort(3) |
| FPE | 8 | core | floating point exception |
| KILL | 9 | exit | non-catchable, non-ignorable kill |
| SEGV | 11 | core | invalid memory reference |
| PIPE | 13 | exit | broken pipe: write to pipe with no readers |
| ALRM | 14 | exit | timer signal from alarm(2) |
| TERM | 15 | exit | terminate process |

# System Boot
- Typical system boot process:
1. BIOS: 
 - performs hardware-specific tasks
 - makes POST, Power-On Self-Test, which tests your hardware
 - detects installed hardware, such as hard disks, memory type and amount, ...
 - initializes hardware by writing initial values into their memory
 - finds a boot device, which is usually a hard disk
 - read and executes MBR (Main Boot Record) located at the beginning of this disk
 - control is now passed to MBR
2. MBR
 - MBR finds and executes GRUB (Grand Unified Bootloader)
 - control is now passed to GRUB
3. GRUB
 - finds available filesystems
 - finds and reads its configuration file, to find out: where system is located, what system to boot, what other actions to perform
 - executes Linux Kernel, main part of Linux OS
 - control is now passed to Linux Kernel
4. Linux Kernel:
 - finds and loads initrd, which is initial ram disk. It contains necessary drivers which allow to access and mount real filesystems
 - mounts the root file system, which is specified in GRUB config file
 - executes /sbin/init, a special program which starts all the others
 - control is now passed to init
5. init
 - looks at the /etc/inittab to determine desired run level
 - loads all programs appropriate for this run level. All programs from /etc/rc.d/rc2.d/ are loaded, because 2 is default Debian run level. Then SSH and TTY are started so you are able to connect to your computer
 - booting up is now finished
6. user
 - connect to computer using SSH
 - SSH daemon executes bash shell
- Daemon --- a program which runs in background all the time. This means that it does not care if you are logged into the system, and usually you do not need to start it manually, because daemond are started automatically when computer boots up.
- Runlevel --- a mode of system operation. Basically, this is just numbers which are fed to init program, which knows which daemons are associated with each number, and startd and stops those daemons as needed. You can see run levels through `find /etc -type d -name 'rc*' 2>/dev/null | sort`
- Scripts in rc directories are executed in alphabetical order. The number in those file names defines their start order. Scripts starting with "S" is executed with action start, those starting with "K" is killed when entering this runlevel.

# Logging
- Daemons write their status and operations in log files. In Debian these files reside in **/var/log/** directory.
- Daemons usually write logs through the daemon called **rsyslogd**, which is called a "logging daemon". It writes logs to different files for simplifying searching and analyzing them.
- "Facilities" distinguish between the log files, and each entry is also marked with severity status (alert, critical, debug, ...).
- Log rotation is performed by **logrotate** daemon.

# Filesystem
- Unix-like operating systems create a virtual file system, which makes all the files on all the devices appear to exist in a single hierarchy. This means, in those systems, there is one root directory, and every file existing on the system is located under it somewhere. Unix-like systems can use a RAM disk or network shared resource as its root directory.
- **/etc/fstab** stores static information about the filesystems. It's only read by programs, and not written
- Inode --- an Index Node, is a structure which store all the information about a file system object (file, directory, etc.) except data content and file name.
- Block --- a minimum chunk of disk space which can be allocated. It usually defaults to 4096 bytes, or 4 Kilobytes.

## mkfs
- `mkfs.ext3 /dev/xxx` create an ext3 file system. If executed on a device with existing file system, this file system will be destroyed.
- `mkfs.ext4` create an ext4 file system.

## tune2fs
- print out and change file system parameters.

## fsck
- check and repair a Linux file system.

# Reference
[1] Learn Linux the Hard Way. http://nixsrv.com/?id=llthw
