## Section 5.1 - Changing the SELinux Enforcement Mode

### Basic SELinux Security Concepts

SELinux consists of a set of policies, defined by the application developers, that declare exactly what actions and accesses are proper and allowed for each binary executable, configuration file, and data file used by an application. This is known as a targeted policy because one policy is written to cover the activities of a single application. Policies declare predefined labels that are placed on individual programs, files, and network ports.

SELinux has three different modes it can be in:

* Enforcing: Enforcing access control rules.
* Permissive: Active and recording warnings of rules that have been violated, but not enforcing.
* Disabled: Turned off completed.

SELinux is a set of security rules that determine which process can access which files, directories, and ports. Every file, process, directory, and port has a special security label called an SELinux context. 

* `context` - a name used by the SELinux policy to determine whether a process can access a file, directory, or port. By default, the policy does not allow any interaction unless an explicit rule grants access. If there is no allow rule, no access is allowed.
* `label` - represents the context of a specified file. Labels have several `contexts`: user, role, type, and sensitivity. An example of a label is shown below:

unconfined_i:object_r:httpd_sys_content_t:s0 /var/www/html/file2

1. User
2. Role
3. Type
4. Level (sensitivity)
5. File

* Many commands that deal with files use the `-Z` option to display or set SELinux contexts (`ps`, `ls`, `cp`, `mkdir`).

### Changing The Current SELinux Mode

One can change the current SELinux modes using the command line:

* `getenforce` - print the current SELinux mode.
* `setenforce` - change the SELinux mode.

The mode which SELinux starts during boot can also be set as a kernel command line argument:

* `encorcing=0` - boots the system into permissive mode.
* `enforcing=1` - boots the system into enforcing mode.
* `selinux=0` - disables SELinux completely on boot.
* `selinux=1` - enables SELinux on boot.

### Setting the Default SELinux Mode

* `/etc/selinux/config` - configuration file where SELinux's default settings are kept.
  * `SELINUX=` can be set to `enforcing`, `permissive`, or `disabled`.

