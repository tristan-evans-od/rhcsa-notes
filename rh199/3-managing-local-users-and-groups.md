## Section 3.1 - Describing User and Group Concepts

### Users

* `/etc/passwd` - The file which stores information about users. Each line is a colon-seperated list of details about a particular user. See the example below.

`user01:x:1000:1000:User One:/home/user01:/bin/bash`

The above defines:

1. Username.
1. User's password ("x" means it is kept in `/etc/shadow`).
1. UID of user.
1. GID of user.
1. Real name of user.
1. Home directory of user.
1. Default shell program of user.

### Groups

* `/etc/group` - The file which stores information about groups. Each line is a colon-seperated list of details about a particular group. See the example below.

`group01:x:10000:user01,user02,user03`

The above defines:

1. Group name.
2. Obsolete group password field. The field should always be 'x'.
3. GID of the group.
4. A list of users which are members of the group.

* Every user has a `primary group`. For local users, this is the group ID in the `/etc/passwd` file. By default, this group will own files created by the user.
* Normally when a user is created, a group of the same name is also created, which becomes the `primary group` for the new user.
* Users also have `supplementary groups`. Membership in `supplementary groups` are determined by the `/etc/group` file.

## Section 3.3 - Gaining Superuser Access

### Switching Users

* `su` - A command used to switch-users. If a "-" is passed to the command, a login shell is started (that users environment variables are loaded). If a "-" is not passed, the original users environment variables remain loaded.

### Running Commands with Sudo

* `sudo` - A command used to execute a single command as the `root` user.
* All `sudo` commands are logged to `/var/log/secure`.
* In RHEL7 and RHEL8, all members of the `wheel` group can use `sudo` to run commands as any user, including `root`.
* `/etc/sudoers` - A file used to configure the `sudo` program. Must be edited with the `visudo` command. 
* `/etc/sudoers.d` - A directory to place `sudo` rules in.

See examples of different `sudo` configurations below.

To enable full `sudo` access for a user:

`user01 ALL=(ALL) ALL`

To enable full `sudo` access for a group:

`%group01 ALL=(ALL) ALL`

To enable full `sudo` access without requiring a password:

`user01 ALL=(ALL) NOPASSWD:ALL`

## Sections 3.5 - Managing Local User Accounts

* `useradd` - Creates a new user.
* `usermod` - Modifies an existing user.

Both the `useradd` and `usermod` commands take the below options:

* `-c` Add the user's real name to the comment field.
* `-g` Specify the primary group of user.
* `-G` Specify a comma-seperated list of supplementary groups for user.
* `-a` Used with the `-G` option to append group to supplementary groups for user, instead of replacing.
* `-d` Set path to home directory for user.
* `-m` Move the user's home directory to new location. Must be used with the `-d` option.
* `-s` Specifiy a particular login shell for user.
* `-L` Lock the user.
* `-U` Unlock the user.
* `-e` Set account expiration date.

* `userdel` - Removes user entry from `/etc/passwd`. Use the `-r` option to also remove the user's home directory.
* `passwd` - Sets password for user.

Specific UID numbers and ranges of numbers are used for specific purposes by Red Hat Enterprise Linux.

* UID 0 is always assigned to `root`.
* UID 1-200 is a range of "system users" assigned statically to system processes by Red Hat.
* UID 201-999 is a range of "system users" used by system processes that do not own files in the file system. 
* UID 1000+ is the range available for assignment to regular users.

## Section 3.7 - Managing Local Group Accounts

* `groupadd` - Creates a group. The `-g` option specifies a particular GID.
* `groupmod` - Changes the properties of an existing group. The `-n` option specifies a new name for the group. The `-g` option specifies a new GID.
* `groupdel` - Deletes a group.

## Section 3.9 - Managing User Passwords

* `/etc/shadow` - A file that can be only read by `root` which contains a database of user's encrypted passwords. Each line is a colon-seperated list of details about a particular user. See the example below.

`user01:4ac6c5fd91ef3e5691579c7c6dca97a1502459df:17933:0:99999:7:2:19113:

1. Username of account the password belongs to.
1. The encrypted password of user.
1. The day on which the password was last changed (days since epoch).
1. The minimum number of days that has to elapse since the last password change before the user can set it again.
1. The maximum number of days that can pass before the password expires. An empty field means the password does not expire.
1. Warning period. When a user begins to get warned about password expiration.
1. Inactivity period. Once the password has expired, it will still be accepted for login this many days before being locked.
1. The day on which the password expires (days since epoch).
1. Reserved for future use.

* `chage` - Command used to set password aging settings for a user.

The below options set different age related aspects of a user's password:

* `-m` Set the minimum age.
* `-M` Set the maximum age.
* `-W` Set the warning period.
* `-I` Set the inactivity period.
* `-d` Set amount of days for next forced password update.
* `-l` List password aging details.
* `-E` Set expiration date.

* The default aging settings can be set in the `/etc/login.defs` file, using the `PASS_MAX_DAYS`, `PASS_MIN_DAYS`, and `PASS_WARN_AGE` settings.

* `nologon` is a shell setting for user accounts not intended to be used to interactively log into a system. 
