## Security

### Basic File Permissions

File permissions and ownership mean different things between simple files and directories.

| Permission | On a File | On a Directory |
|-|-|-|
| read (r) | Permission to read the file | Permission to list files |
| write (w) | Permission to change the file | Permission to create and remove files |
| execute (x) | Permission to run file | Permission to access files |

* Permissions granted to the group take precedence over those granted to other users.
* Permissions granted to the owner take precedence over all other categories.
* In the below example, users in group "test" cannot read, write, or execute the file.

`-rwx---rwx. 1 root test 127 Dec 13 07:21 test.sh`

* The last bit in the permissions set in a file listing represents if the file has a SELinux security context or ACL permissions.
    * A "." means that the file has a SELinux security context.
    * A "+" means that the file has a ACL permission set.

* The first bit in the permissions set in a file listing represents the special permission bits.

Special permissions mean different things on files and directories:

| Special Permission | On an Executable File | On a Directory |
|-|-|-|
| SUID | The effective user ID of the process is that of the file upon execution | No effect. |
| SGID | The effective group ID of the process is that of the fiel upon execution | Give files created in directory the same group ownership as that of the directory |
| Sticky bit | No effect. | Files in a directory can be renamed or removed only by their owners. |

* The "s" in the execute bit for the user owner is the SUID bit. 
* The "s" in the execute bit for the group owner is the SGID bit.
* The "t" in the execute bit for the other users is the sticky bit.

There are also special file attributes that help control what anyone can do to different files.

| Attribute | Description |
|-|-|
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

### Access Control Lists (ACL)

ACLs provide a second level of discretionary control that support overriding of standard ugo/rwx permissions.

* To use ACLs, you'll need to mount the appropriate filesystem with the `acl` option (default behavior)
* To use ACLs, you'll need to setup execute permissions on the associated directories
* ACLs are supported on the following filesystems: `ext4`, `XFS`, `NFS4`

There are several commands used to manage ACLs:

* **getfacl** - Displays the current ACLs of a file
* **setfacl** - Sets the ACLs of a file

Here are the options for the `setfacl` command:

| Switch | Description |
|-|-|
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

### Firewall

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

* Use the `--get-default-zone` switch to get the default zone:

* Use the `--set-default-zone` switch to set the default zone:

* Use the `--list-all` switch to list all the configured interfaces and services allowed through a zone:

* Use the `--permanent` switch for changes that persist through reboots

* Use the `--zone` switch to make changes to specified zone, instead of the default

* Use the `--add-port`, `--add-service`, `--remove-port`, `--remove-server` switches respectively

* Use the `--reload` switch to reload the run-time configuration of the firewall:

### SSH

There are several notable SSH-oriented utilities:

* **sshd** - The daemon service; this must be running to receive inbound SSH requests.
* **ssh-agent** - A program to hold private keys used for DSA, ECDSA, and RSA authentication. The idea is that the `ssh-agent` command is started in the beginning of an X session or a login session, and other programs are started as clients to the ssh-agent program.
* **ssh-add** - Adds private key identities to the authentication agent, `ssh-agent`.
* **ssh** - The SSH client which provides a secured shell connection to a remote system. To make this work with key-based authentication, you need a private key on the client and a public key on the server. Take the public key file, such as `id_rsa.pub`, and copy it to the server. Place it in the home directory of an authorized user in the `~/.ssh/authorized_keys` file.
* **ssh-keygen** - A utility that creates private/public key pairs for SSH authentication. The `ssh-keygen -t` command will create a key pair based on the specified protocol.
* **ssh-copy-id** - A script that copies a public key to a target remote system.

Systems configured with SSH include configuration files in two different directories:

* **/etc/ssh/** - System-wide configuration.
* **~/.ssh/** - Per-user configuration.

The `~/.ssh/` subdirectory can include the following files:

* **authorized_keys** - Includes a list of public keys from remote users. Users with public encryption keys in this file can connect to remote systems. The system users and names are listed at the end of each public key copied to this file.
* **id_dsa** - Includes the local private key based on the DSA algorithm.
* **id_dsa.pub** - Includes the local public key for the user based on the DSA algorithm.
* **id_ecdsa** - Inculdes the local private key for the user based on the ECDSA algorithm.
* **id_ecdsa.pub** - Includes the local public key for the user based on the ECDSA algorithm.
* **id_rsa** - Includes the local private key based on the RSA algorithm.
* **id_rsa.pub** - Includes the local public key for the user based on the RSA algorithm.
* **known_hosts** - Contains the public host keys from remote systems. The first time a user logs in to a system, she's prompted to accept the public key of the remote server.

Most of the common issues with SSH key-based authentication are related to file permissions. The below table outlines how they should be set:

| File | Permissions |
|-|-|
| private keys | 600 |
| public keys | 644 |
| ~/.ssh/ | 700 |

To setup a private/public pair for key-based authentication:

1. Generate a key pair: `ssh-keygen`
  * `-b` specify size of bits of RSA keys
  * `-t` specify the type of key to use
2. Transmit the public key to remote system: `ssh-copy-id -i .ssh/id_rsa.pub tristan@tester1.example.com`
2. SSH into the remote host: `ssh tristan@tester1.example.com`

### SELinux

The SELinux security model is based on the below concepts:

* **subjects** - A process; such as a running command or application.
* **objects** - A file, a device, a socket, or in general any resource that can be accessed by a subject.
* **actions** - What may be done by the subject to the object.
* **context** - A label which is used by the SELinux security policy to determine whether a subject's action on an object is allowed or not.
  * `ls -Z` lists files, including their SELinux security contexts.

SELinux can be put into several different modes:

* **enforcing** - Running and taking action on violations. When set to `enforcing`, it has two different modes:
  * `targeted` - The default policy. Allows you to customize what is protected by SELinux in a fine-grain manner.
  * `mls` - Supports layers of security between levels c0 and c3.
* **permissive** - Running, not taking action on violations, but logging them.
* **disabled** - Not running at all.

The above settings can be set in `/etc/selinux/config` and will take affect upon reboot.

There are several commands used to managed SELinux:

* **getenforce** - Return the mode SELinux is set to. 
* **setenforce** - Set SELinux to `enforcing`, `permissive` or `disabled` modes. This change is not persistent. To make permanent changes, update the `SELINUX` variable in `/etc/selinux/config`. If SELinux is set to `disabled`, you will need to make the update in the file and reboot system.
* **sestatus** - Returns information about how SELinux is currently configured.
* **semanage** - Use to manage different features of SELinux.
  * `login -l` prints the status of current SELinux users.
* **getsebool** - View boolean settings.
* **setsebool** - Set boolean settings.
* **chcon** - Change context of target.
* **restorecon** - Restores target to default contexts defined in `/etc/selinux/targeted/context/files/file_contexts`.
* **ausearch** - Can help filter for specific types of audit related problems.
* **system-config-selinux** - Starts the SELinux GUI configuration utility.
