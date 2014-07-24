[TOC]

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

## grep
- To print lines matching a pattern. It will search the standard input if no files (or a single `-`) are named. e.g. `command |grep XXX`
- `-v` choose the lines without pattern
- `-i` ignores case distinction
- '{' and '(' need to escape in the regex
- `grep` patterns are matched against individual lines so there is no way for a pattern to match a newline found in the input.

## sed
- `sed 's/pattern/replace/option'` is a stream editor for filtering and transforming text.
- `sed -ne 'mp' filename` shows the m-th line.
- `sed -ne 'j,kp' filename` shows the j-th to the k-th lines.

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
- stdin is 0, stdout is 1, stderr is 2
- `command > file` redirect the output and rewrite the file
- `command >> file` append the file
- `command < file` read the parameter from file

## cat
- `cat` simply copy standard input to standard output
- `cat f - g` output f's contents, then standard input, then g's contents
- `cat -n f` output f's contents with numbering all output lines
- `cat -v f` show nonprinting

## chmod
- Change the file mode. The three part of the mode string correspond to the owner, the user in the same group as the owner, and the other users.
- `chmod [who] [+-=mode]`. `who` can be `u` for owner, `g` for those in the same group, `o` for the others, and `a` for all. `mode` can be `r` or `4` for read, `w` or `2` for write, and `x` or `1` for excute.
- `chmod 751 file`
- `-R` means recursively.

## cp
- `cp -v f g` explain what is being done

## chown
- Change the file owner and group.

## curl
- To transfer data from or to a server, using one of the supported protocols (HTTP, HTTPS, FTP, FTPS, SCP, SFTP, TFTP, DICT, TELNET, LDAP or FILE).

## date
- `date -d "2012-04-10 -1 day" +%Y-%m-%d` returns 2012-04-09

## df
- `df -h dir` show the disk usage of the dir

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

## find
- `find /path/ -name xxx`
- `-iname` ignore upper or lower cases

## history
- `history -w` writes all your command history to .bash_history file. Normally this is done at the end of your session, when you close it by typing exit or by pressing <CTRL>+D.
- If you prepend a command with space it won't be saved in history.

## kinit
- obtain and cache Kerberos ticket-granting ticket

## ln
- `ln t l` creates a link to `t` with the name `l`. It differs from `cp` in not copying the contents of the file.

## ls 
- Every file starts with dot is hidden. **-a** shows all files including the hidden ones. **-l** prints file list in **long** format: permissions, owner, group, size, timestamp (normally modification time) and filename.
- Note that **-l** shows the size of the directory in *blocks*.
- `ls -tr` mean that file list is sorted by time in reverse direction. This means that most recently created and modified files are printed last.
- `ll` is a common alias to `ls -l`

## lzop
- `lzop file` compress, `lzop -x file` extract

## man
- `man -K` will search for text in all manual pages. This is a brute-force search. The result will be shown one manual after another.

## mv
- Notice that it overwrites the destination file without asking.
- `mv xxx xxx xxx xxx destDir` move sources to one destination

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
- `grep -P "31.22038\t121.41149" fingerprints` or `grep '1.22038'$'\t''121.41149'" fingerprints"` matches '\t'

## pwd
- Print out the current working directory.

## rm
- Beware, always use `-v` key.

## rename
- `rename from to file` will rename the specified files by replacing the first occurrence of from in their name by to.
- `rename .htm .html *.htm` will fix the extension of your html files.

## scp
- `scp -P 58422 convertid.jar data_algo@10.1.1.164:/data/home/data_algo/runchao.mao`

## shuf
- generate random permutation of lines to standard output

## sort
- start from 1
- Note that "\t" doesn't work fine in some implementation of `sort`. `sort -t $'\t' -o Density10m -k 2,2rn part-00000` only works for bash. The dollar sign tells bash to use ANSI-C quoting. `"Ctrl-v<tab>"` also works.

## tar
- `tar cf log.tar file.log` compress
- `tar xf file.tar` unpack

## tail
- `tail -n 5 .profile` prints out exactly five last lines from .profile file

## touch
- Updates timestamp to current date and time. This means that all time attributes, `st_atime`, `st_mtime` and `st_ctime` are set to current date and time. You may become sure of this by `stat`.

## tr
- Translate or delete characters: `tr -d 'x'` delete all x, `tr '\n' ' '` replace all `'\n'` with `' ' `

## wc
- Reads either standard input or a list of files, and generates: newline count (**-l**), word count (**-w**), byte count (**-c**), length of the longest line (**-L**).
- `cat filename | wc -l` will eliminate the filename in the output.

## xargs
- xargs reads items from the standard input, delimited by blanks (which can be protected with double or single quotes or a backslash) or newlines, and executes the command (default is /bin/echo) one or more times with any initial-arguments followed by items read from standard input. Blank lines on the standard input are ignored.

## zip
- `zip -r xxx.zip file1 file2 ...`
- `unzip xxx.zip`
- jar is packed by zip

## crontab

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

## .bash_rc
- Every time you log into this user, the commands in it will be executed.

## 

# Reference
[1] Learn Linux the Hard Way. http://nixsrv.com/?id=llthw
