### Security

#### Basic File Permissions

File permissions and ownership mean different things between simple files and directories.

| Permission  | On a File                      | On a Directory                        |
|-------------|--------------------------------|---------------------------------------|
| read (r)    | Permission to read the file    | Permission to list files              |
| write (w)   | Permission to change the file  | Permission to create and remove files |
| execute (x) | Permission to run file         | Permission to access files            |

* Permissions granted to the group take precedence over those granted to other users.
* Permissions granted to the owner take precedence over all other categories.
* In the below example, users in group "test" cannot read, write, or execute the file.

`-rwx---rwx. 1 root test 127 Dec 13 07:21 test.sh`

* The last bit in the permissions set in a file listing represents if the file has a SELinux security context or ACL permissions.
    * A "." means that the file has a SELinux security context.
    * A "+" means that the file has a ACL permission set.

* The first bit in the permissions set in a file listing represents the special permission bits.

Special permissions mean different things on files and directories:

| Special Permission | On an Executable File                                                    | On a Directory                                                                    |
|--------------------|--------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| SUID               | The effective user ID of the process is that of the file upon execution  | No effect.                                                                        |
| SGID               | The effective group ID of the process is that of the fiel upon execution | Give files created in directory the same group ownership as that of the directory |
| Sticky bit         | No effect.                                                               | Files in a directory can be renamed or removed only by their owners.              |

* The "s" in the execute bit for the user owner is the SUID bit. 
* The "s" in the execute bit for the group owner is the SGID bit.
* The "t" in the execute bit for the other users is the sticky bit.

There are also special file attributes that help control what anyone can do to different files.

| Attribute | Description |
|-----------|-------------|
| append only (a) | Prevents deletion, but allows appending to file |
| no dump (d) | Disallows backups of file with the `dump` command |
| extent format (f) | Set with the ext4 filesystem; an attribute that cannot be removed |
| immutable (i) | Prevents deletion or any type of change |

There are several commands to manipulate file ownership, basic permissions, and attributes:

* **chmod** - Change basic permissions of files
* **chown** - Change ownership of files
* **chgrp** - Change just the group owner of files
* **chattr** - Change special attributes of files
* **lsattr** - List special attributes of file

RHEL handles `umask` like most other distributions, but does have some differences too:

* The `umask` sets the default permissions of files and directories
* In RHEL, you cannot configure `umask` to allow automatic creation of new files with executable permissions
* When entering the `umask` command, it returns the 4-digit octal number which defines the default file and directory permissions
* The default `umask` value is defined in `/etc/profile` and `/etc/bashrc` files
* By default, user accounts above UID "200" have a `umask` of "002". All below "200" have a `umask` of "022".

#### Access Control Lists (ACL)

ACLs provide a second level of discretionary control that support overriding of standard ugo/rwx permissions.

* To use ACLs, you'll need to mount the appropriate filesystem with the `acl` option (default behavior)
* To use ACLs, you'll need to setup execute permissions on the associated directories
* ACLs are supported on the following filesystems: `ext4`, `XFS`, `NFS4`

There are several commands used to manage ACLs:

* **getfacl** - Displays the current ACLs of a file
* **setfacl** - Sets the ACLs of a file, see examples below:

`setfacl -m u:tristan:rwx /home/exam-prep/the-answers` (gives user "tristan" rwx permissions to specified file)  
`setfacl -x u:tristan /home/exam-prep/the-answers` (gives user "tristan" no permissions to specified file)  
`setfacl -b /home/exam-prep/the-answers` (removes all ACLs from file)  




#### Firewall


#### SSH


#### SELinux
