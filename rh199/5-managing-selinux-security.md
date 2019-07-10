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

## Section 5.3 - Controlling SELinux File Contexts

### Initial SELinux Context

On systems running SELinux, all processes and files are labeled. The label represents the security relevant information, known as the SELinux context.

New files typically inherit their SELinux context from the parent directory. The only exceptions to this are: 

* The file is created in one directory and then moved to another with a different context. 
* The file is copied in a way which preserves the SELinux context (`cp -a` for example).

### Changing the SELinux Context of a File

The below commands can be used for changing the SELinux context on files:

* `semanage fcontext` - Used to set the default labeling for a file.
* `restorecon` - Restores context of file to default. This combined with `semanage fcontext` will set context to new default.
* `chcon` - Changes context of file in file system, does not update the SELinux context database. Using `restorecon` on such files will return them to their default contexts. 

### Defining SELinux Default File Context Rules

To ensure that you have the tools to manage SELinux contexts (`restorecon` and `semanage`), ensure the following packages are installed:

* `policycoreutil`
* `policycoreutil-python`

The `semanage fcontext` command displays and modifies the rules that `restorecon` uses to set default file contexts. It takes the following options:

* `-a`, `--add` add a record of the specified type.
* `-d`, `--delete` delete a record of the specified object type.
* `-l`, `--list` list records of the specified type.

## Section 5.4 - Adjusting SELinux Policy with Booleans

SELinux booleans are switches that change the behavior of the SELinux policy. SELinux booleans are rules that can be enabled or disabled. They are used to tune the policy to make selective adjustments.

* The `selinux-policy-doc` package provides `man` pages that describe the purpose of available booleans. The `man -k '_selinux'` command lists these man pages.

Commands useful for managing SELinux booleans include:

* `getsebool` - Lists booleans and their state. Can be used by non-privileged users.
* `setsebool` - Modifies booleans.
  * `-P` modifies the SELinux policy to make the modification persistent. Must be a super-user.
* `semanage boolean -l` - Reports on whether or not a boolean is persistent, along with a short description of the boolean. Must be a super-user.

## Section 5.6 - Investigating and Resolving SELinux Issues

* `sealert` - After identifying a specific incident by UUID in `/var/log/messages/ or `/var/log/audit/audit.log`, use `salert -l` to produce a report for a specific incident.

