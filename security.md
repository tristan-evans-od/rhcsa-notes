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

| Special Permission | On an Executable File                                                    | On a Directory                                                                    |
|--------------------|--------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| SUID               | The effective user ID of the process is that of the file upon execution  | No effect.                                                                        |
| SGID               | The effective group ID of the process is that of the fiel upon execution | Give files created in directory the same group ownership as that of the directory |
| Sticky bit         | No effect.                                                               | Files in a directory can be renamed or removed only by their owners.              |

* The "s" in the execute bit for the user owner is the SUID bit. 
* The "s" in the execute bit for the group owner is the SGID bit.
* The "t" in the execute bit for the other users is the sticky bit.

#### ACLs


#### Firewall


#### SSH


#### SELinux
