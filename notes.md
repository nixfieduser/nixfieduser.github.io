---
layout: default
---
The following are a list of Linux commands, proprieties, and related programs that I am learning such as VIM and markdown editors. I intent to update it regularly.


In this page likely there won't be any Nix specific commands as I am trying to be as generic as possible. In my course I am studying Debian, only commands such as those related to it's package management will be directly tied to a specific distribution, mentions of NixOS are solely due to output and  my preferences.


---

## **TTY**:

`/etc/issue`

Exhibits a configurable message above the login prompt in a TTY session, e.g.,
```
cat /etc/issue

<<< Welcome to NixOS 25.05.20250402.2c8d3f4 (\m) - \l >>>
```

In the above message, (\m) returns the running architecture, in my case (x86_64). The (\l) displays the name of the current TTY session, most likely TTY1-9.

Other common outputs is the (\t), current time, and (\d) for current date.

---

## BASH:

`PS1`

Variable that defines the shell prompt. My current line is:
```
[hal9000@hal9000:~]$
```
Defined by the PS1= line command in my system *bashrc* file:
```
PS1="\n\[\033[$PROMPT_COLOR\][\u@\h:\w]\\$\[\033[0m\] "
```

\n forces a new line
\u username
\h hostname
\w current directory
PROMPT_COLOR is part of a larger IF block statement in my bashrc


To temporary edit the PS1 variable just type anything after PS1=
```
PS1="[I'm sorry \u, I'm afraid I can't do that] >> "
```
However, in order to make the changes permanent the above line needs to be written in your .bashrc file.

`.bashrc`

Config file per user account. In it can be edited shell variables, aliases and more.

My distribution already enables a few ls aliases. I defined the following two:
```
alias upgrade-now="nix flake update --flake ~/etc/nixos && nixos-rebuild switch"

alias upgrade-later="nix flake update --flake ~/etc/nixos && nixos-rebuild boot"
```


`.bash_history`

User file that contains all commands typed. The limit of registries is determined by a shell variable.

There are various ways of interacting with this file. Simply typing **history** will promptly show all lines. Better format options would be:
```
history | more
cat .bash_profile | more
```

To view a specific amount of listed commands, a number can be informed:
```
history 15 | more
#the output will be the last 15 registries.
```

---

## Chmod:

The first information available when using **ls** with the listing format concerns the file or directory permissions and the user and group that holds the authority over these locations and archives. To edit these parameters  Chmod is the command utilized.

The following are two listed objects from my filesystem:

```
drwxr-xr-x 1 hal9000 users  586 2025-01-27 17:19 plasma-panel-colorizer-main  

-rw-r--r-- 1 hal9000 users 3.9M 2025-01-26 15:18 plasma-panel-colorizer-main.zip
```

The first object listed is a directory named *plasma-panel-colorizer-main*, which contains the extracted contents from the zipped file by the same name. 

The letter `D`means directory.
The `-`in the same position means file.

There are three sets of 3 permissions each. About the mentioned directory these are the current settings:

- owner (read, write, execute)
   -   the owner (hal9000) can view (read), modify (write) and access the directory;
- group (read, write, execute)
	- the user group can view (read) but NOT modify (write), and can access the directory;
- other (read, write, execute)
	- all other users can ONLY read and access.

When changing permissions using the Chmod command, it needs to be specified if the intended modifications are about the user/owner of the file, the user group, the other users or all of them.

Let's edit the permissions of the folder:
```
chmod u-w plasma-panel-colorizer/ # I removed my permission to write in this directory.

chmod u+w plasma-panel-colorizer/ # restored write permission.

chmnod ug-rw plasma-panel-colorizer/ # read and write permissions revoked from the user and group.
```

Instead of letters, this alternative notation exists:

```
chmod 555 plasma-panel-colorizer/ # I removed my permission to write in this directory.

chmod 755 plasma-panel-colorizer/ # restore write permission.

chmod 115 plasma-panel-colorizer/ # read and write permissions revoked from the user and group.
```

This numeric method establishes that for each of the three sets of permissions (user, groups and others) the maximum total number is 7, calculated by the individual permissions:

- read = 4
- write = 2
- execute = 1



---

## File-system navigation:

To print the current user location type `pwd`. In order to change directory use `cd`.

To list the content of the current directory, the following options are available:
```
ls       # display ONLY the visible folders and files.
ls -a    # display folders and files, INCLUDING the invisible ones.
ls -al   # display all content but using a listing format.
```

Regarding the  size informed when using the listing format, without additional parameters the default output is in bytes, to make the reading easier the following command might be better:

```
ls -alh  # display all content using listing and all sizes are formated          with the respective size sufixes (KB, MB, GB).

ls -alhS # same as above but with the files and directory sorted by size
```

---
## Text output:

Any command text output can be automatically redirected to a file utilizing:

```
COMMAND >> texfile  #create new file or append content to the existing file.

COMMAND > textfile  #create new file or overwrite the existing one.
```

`cat`

Any text content can be viewed with this command when specifying the filename.
```
cat filename

cat -e filename  #appends $ in each newline break

cat -t filename  #occupies each tab space with I
```

As  suggested before, *cat* can be used as a very basic text editor by calling cat followed either by > to start a new file or overwrite it, or >> to append to a existing file.

`echo`

Compared to cat, the echo command is not well suited to show the entire written content of a file,  he  is very commonly used to transfer content and work with shell variables instead.

```
echo -e "Hi! I want the following text in a new line \nthis is the second part\n bye" >> output

echo -e "Hi! \bI \bam \btyping \bwhatever just to show the missing space"

echo -e "Hi!"\t this text will appear after TAB space"

echo -e "Hi! \v this text will appear after vertical TAB space"

echo -e "Hi! I am experimenting vertical tab space \vis it the same as a new line? \nwhat about the next line? "
```

The -e parameter is required to interpret the commands set by \

---
## VIM:

Basic text navigation can be done by using the letters `H J K L`. 
Other nice position related commands are:

```
0     #move cursor to the beginning of the line

e     #move cursor to the end of each word
```

To switch into writing mode there are several options:

- press i to start typing at the current line position and BEFORE the cursor.
- press a to start typing at the current line position and AFTER the cursor.
- press A to start typing at the END of current line.

While in `normal`mode, the following operations are available:

```
dw        #delete word starting from the cursor position

de        #similar to the previous command but e stands for the last element (keeping blank spaces)

d[2...9]w #delete N amount of words starting from the cursor position

x         #delete current character 

d$        #delete the entire line starting from the cursor

dd        #delete the entire line regardless of the cursor position (single d also works)

[2...9]dd #delete N amount of lines

u         #undo command
U         #undo entire line

p         #paste the last deleted line bellow the cursor

ctril-r   #redo
```

To quickly edit without enter into `edit`mode one can can utilize the following commands:

```
r[a...z]  #r accompanied by any symbol will overwrite the current value under the cursor

ce        #allows the overwriting of the current word until its last element

cc        #overwrite the entire line extension (single c also works)

c$        #similar to the d$ command, allows inserting over the current position until the end of the line
```

##### Copy/Paste:

- press `yy` to copy the entire line and `p` to paste it.
- press `y6y` or `6y`  to copy the current and 5 other lines.
- press `yw`  copy the current word.
- press `y6w` to copy the current word and the following 5.

##### To switch between lines:

- **Ctrl-g** to exhibit the filename/location and current line number.
- to move forward type the specific line number and press enter.
- to move backwards type the specific line number and press uppercase G.
- to move to the previous location type Ctrl-o.

##### Searching:

- press `/` and type the desired word or phrase. Press n or N to move forward and backwards among the results.   
- press `?`to search in the reverse order. Press n or N to move forward and backwards among the results.
- press `%`  to search for corresponding symbols over current cursor position. Specially useful for programming blocks such as () {} {{}} "" '' 
 
##### Substitution:

To search and automatically overwrite words and phrases use the following commands:

```
:s:old_word/new_word/g  #change all ocurrances of old_word from current line into new_word

:s:old_word/new_word   #change only the first ocurrence of old_word

:283,576s/old_word/new_word/g  #change all ocurrances of old_word between the lines 283 and 576

:%s/old_word/new_word/gc   #change ocurrances of old_word in the ENTIRE file. Adding % at the beginning is required and C is for confirmation only
```

##### External commands:

While using VIM one can execute external commands such as `ls -al | less`without opening another terminal emulator window or switching away from the editor. Simply type `:! ls -al | less`.

##### Buffers:

To utilize multiple buffers within the same VIM session you may use the following commands:

- press `Ctrl-w` and then **v** to split vertically or type `:vs`.
- press `Ctrl-w` and then **s** to split horizontally or type `:sp`.
- press `Ctrl-w` plus **-** or **+** to resize vertically and **=** to reset.
- press `Ctrl-w` plus **<** or **>** to resize horizontally and **=** to reset.



ps  (parameters) | grep (name)
pgrep (ps + grep)
