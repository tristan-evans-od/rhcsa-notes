## User Administration

### The Shadow Password Suite

* /etc/passwd - contains basic information about every user.
* /etc/group - contains each group and the users assigned to it.
* /etc/shadow - contains each user and their encrpyted password.
* /etc/gshadow - contains the group administrators, which can add other group members using the `gpasswd` command. Once a password is set on a group, other users can becomes members with the `newgrp` command.
* /etc/login.defs - provides the baseline for a number of parameters in the shadow password suite. 

### Command-Line Tools

* `vigr` 
* `gpasswd` sets a password for a group, which can be used by `newgrp` and `sg` commands.
* `useradd` adds a user to `/etc/passwd`. Also creates the users home directory with any files from `/etc/skel`.
* `userdel` deletes target user.
* `usermod` modifies various settings in `/etc/passwd`.
* `passwd` assigns a password to target user.
* `groupadd` adds a group to `/etc/group`.
* `groupdel` deletes a group from `/etc/group`.
* `groupmod` modifies group numbers and names.
* `chage` manages aging information for a password, as stored in the `/etc/shadow` file.
* `su` switches the current user.
* `sg` executes another command with the rights associated with a special group.
* `sudo` allows users to temporarily execute commands as the super-user. These users and commands are defined in `/etc/sudoers`.
* `visudo` used to modify `/etc/sudoers`.

### User and Shell Configuration

* /etc/skel - contains a default set of files that get deployed to a user created with the `useradd` command.
* /etc/bashrc - used for aliases and functions on a system-wide basis. It also assigns a value of `umask`, defines a shell prompt and includes settings from .sh files in the `/etc/profile.d` directory.
* /etc/profile - used for system-wide environments and start up files. It is sourced when bash is invoked as a login shell. It mainly sets the `PATH`, `USER`, `LOGNAME`, `MAIL`, `HOSTNAME`, `HISTSIZE`, and `HISTCONTROL` variables.
* /etc/profile.d - designed to contain scripts to be executed in a login or interactive shell

