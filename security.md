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
* **setfacl** - Sets the ACLs of a file

Here are the options for the `setfacl` command:

| Switch | Description |
|--------|-------------|
| -b | Removes all ACL entries |
| -k | Deletes default ACL entries |
| -m | Modifies the ACLs of a file with specific user and group |
| -n | Omits the recalculation of the mask entry |
| -R | applies changes recursively |
| -x | Removes a specific ACL entry |

Here are some examples of the `setfacl` command:

`setfacl -m u:tristan:rwx /home/exam-prep/the-answers` (gives user "tristan" rwx permissions to specified file)  
`setfacl -x u:tristan /home/exam-prep/the-answers` (gives user "tristan" no permissions to specified file)  
`setfacl -b /home/exam-prep/the-answers` (removes all ACLs from file)  

#### Firewall

There exists on a default RHEL system three kinds of firewall management tools:

* **iptables** - uses INPUT, OUTPUT and FORWARD chains to manage packet filtering and routing.
* **firewall-cmd** - a command-line utility for the firewalld service
* **firewall-config** - a GUI utility for the firewalld service

The basic outline of a `iptables` command looks like so:

`iptables -t tabletype <action_direction> <packet_pattern> -j <what_to_do>`

* The "tabletype" can be:
  * a `filter` which sets a rule for filtering packets (default)
  * a `nat` configures Network Address Translation
* The "action direction" refers to the type of action you are taking to the chain:
  * **-A** - appends a rule to end of a chain
  * **-D** - deletes a rule from a chain
  * **-L** - lists the currently configured rules in a chain
  * **-F** - flushes all the rules in the current `iptables` chain
* If you are appending or deleting a chain, you'll want to apply it to network data traveling in one of three directions:
  * **INPUT** - All incoming packets are checked against the rules in this chain
  * **OUTPUT** - All outgoing packets are checked against the rules in this chain
  * **FORWARD** - All packets received from one computer and being sent to another (routed) are checked against this chain's rules
* The "packet pattern" is used to match packets based on destination IP, source IP, port or protocol type
* The "what to do" tells `iptables` what to do with packets that match:
  * **DROP** - The packet is dropped, no error message is returned to sender
  * **REJECT** - The packet is dropped, an error message is returned to sender
  * **ACCEPT** - The packet is allowed to proceed

You can manage the actual `iptables` service using `systemctl`:

`systemctl status iptables`

You can manage firewalld using the command line utility `firewall-cmd`:

* Get the default zone:

`firewall-cmd --get-default-zone`

* Set the default zone:

`firewall-cmd --set-default-zone=internal`

* List all the configured interfaces and services allowed through a zone:

`firewall-cmd --list-all`

* Use the `--permanent` switch for changes that persist through reboots

* Use the `--zone` switch to make changes to specified zone, instead of the default

* Use the `--add-port`, `--add-service`, `--remove-port`, `--remove-server` switches respectively

* After changes have been made, reload the run-time configuration of the firewall:

`firewall-cmd --reload`

#### SSH



#### SELinux
