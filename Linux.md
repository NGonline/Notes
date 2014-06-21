[TOC]

# Text Processor

## awk
- `awk '{print $1, $4}' f` prints the first and fourth colume of `f`
- `awk '{printf "%s\t%s\n",NR-1,$0}' xxx > ind` add row index to xxx (begin from 0) and redirect to ind
- `awk 'FNR==m{print}' filename` shows the m-th line
- `awk 'FNR&gt;=j &amp;&amp; FNR &lt;=k{print}' filename` shows the j-th to the k-th lines

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
- Shortcuts in bash:
| 快捷键 | 作用 |
| - | - |
| ctrl + a | 光标跳转到行首 |
| ctrl + e | 光标跳转到行末 |
| ctrl + u | 删除从行首到光标之前的部分 |
| ctrl + k | 删除从光标所在位置到行末的部分 |
| ctrl + w | 删除光标之前的单词 |
| ctrl + y | 常配合上面三条命令一起使用，将它们删除的部分粘贴到光标所在位置 |
| ctrl + r | 反向搜索执行过的命令 |
| alt + f | 以单词为单位，向前移动光标 |
| alt + b | 以单词为单位，向后移动光标 |

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
- Change the file mode.

## cp
- `cp -v f g` explain what is being done

## chown
- Change the file owner and group.

## date
- `$ date -d "2012-04-10 -1 day" +%Y-%m-%d` returns 2012-04-09

## echo
- `echo Hello, $LOGNAME!` tells your shell to output a string `Hello, $LOGNAME!`, substituting `$LOGNAME` with environment variable $LOGNAME which happens to contain your login.
- `echo 'echo Hello, $LOGNAME!' >> .profile` add a line to **.profile**. Note that it will add a '\n' at first.
- `echo 'xxx' > f` replace instead of pending

## history
- `history -w` writes all your command history to .bash_history file. Normally this is done at the end of your session, when you close it by typing exit or by pressing <CTRL>+D.
- If you prepend a command with space it won't be saved in history.

## ln
- `ln t l` creates a link to `t` with the name `l`. It differs from `cp` in not copying the contents of the file.

## ls 
- Every file starts with dot is hidden. **-a** shows all files including the hidden ones. **-l** prints file list in **long** format: permissions, owner, group, size, timestamp (normally modification time) and filename.
- Note that **-l** shows the size of the directory in *blocks*.
- `ls -tr` mean that file list is sorted by time in reverse direction. This means that most recently created and modified files are printed last.
- `ll` is a common alias to `ls -l`

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
- Note that "\t" doesn't work fine in some implementation of `sort`. `sort -t $'\t' -o Density10m -k 2rn part-00000` only works for bash. `"Ctrl-v<tab>"` also works.

## tail
- `tail -n 5 .profile` prints out exactly five last lines from .profile file

## touch
- Updates timestamp to current date and time. This means that all time attributes, `st_atime`, `st_mtime` and `st_ctime` are set to current date and time. You may become sure of this by `stat`.

## tr
- Translate or delete characters: `tr -d 'x'` delete all x, `tr '\n' ' '` replace all `'\n'` with `' ' `

## wc
- Reads either standard input or a list of files, and generates: newline count (**-l**), word count (**-w**), byte count (**-c**), length of the longest line (**-L**).

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
