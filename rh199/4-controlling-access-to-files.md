## Section 4.3 - Managing Default Permissions and File Access

### Special Permissions

| Special permission | Effect on files | Effect on directories | Numberical value |
|-|-|-|-|
| u+s (suid) | File executes as the user that owns the file. | No effect. | 4 |
| g+s (sgid) | File executes as the group that owns the file. | Files newly created in the directory have their group owner set to match the group owner of the directory. | 2 |
| o+t (sticky) | No effect. | Users with write access to the directory can only remove files that they own; they cannot remove or force saves to files owned by other users. | 1 |

### Default File Permissions

* Files and directories get different default permissions upon creation.
* If you create a new directory, it gets octal permissions of 0777 (drwxrwxrwx).
* If you create a new regular file, it gets octal permissions of 0666 (-rw-rw-rw-).
* The shell session will set a `umask`, which is an octal bitmask used to further retrict the permissions that are already set. For example, the umask of 0002 clears the write bit for other users when creating a new file. A umask of 0077 clears all the group and other permissions of newly created files.
* `umask` - A command that returns the current umask setting. Add a numberical argument to change the current umask of the current shell.
* The system's default umask values for Bash shell users are defined in the `/etc/profile` and `/etc/bashrc` files. Users can override the system defaults in the `.bash_profile` and `.bashrc` files in their home directories.


