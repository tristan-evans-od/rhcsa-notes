## Section 7.1 - Registering Systems for Red Hat Support

Red Hat Subscription Management provides tools that can be used to entitle machines to product subscriptions, allowing administrators to get updates to software packages and track information about support contracts and subscriptions used by systems.

### Registration from the Command Line

* `subscription-manager` - A tool used to register and attach a system for support. It takes several options:
  * `register` will register a system to a Red Hat account (requires username/password).
  * `list` will list all available subscriptions for your system.
    * `--available` will list only available subscriptions.
    * `--consumed` will list only consumed subscriptions.
  * `attach` will attach a subscription to a system.
    * `--auto` will automatically attach a subscription.
    * `--pool` will attach specific subscription based on pool ID.
  * `unregister` will unregister a system.

### Entitlement Certificates

An entitlement is a subcription that has been attached to a system. Digital certificates are used to store current information about entitlements on the local system. They are stored in `/etc/pki`.

* `/etc/pki/product` - Contains certificates indicating which Red Hat products are installed on the system.
* `/etc/pki/consumer` - Contains certificates identifying the Red Hat account to which the system is registered.
* `/etc/pki/entitlement` - Contains certificates indicating which subscriptions are attached to the system.

These certificates can be inspected using the `rct` utility.

## Section 7.3 - Installing and Updating Software Packages with Yum

* `yum` - Used to install, update, remove, and get information about software packages and their dependencies.
  * `help` display usage information.
  * `list` displays installed and available packages.
  * `search` lists packages by keywords found in the name and summary fields only.
    * `all` searching through package names, summaries, and description fields.
  * `info` returns detail information about a package.
  * `provides` displays packages that match the path name specified.
  * `install` obtains and installs a software package, including any dependencies.
  * `update` obtains and installs a newer version of the specified package, including any dependencies.
  * `remove` removes and installed software package, including any supported packages.
  * `group list` will list available groups to install, which are collections of related software installed together for a particular purpose. Regular groups are collections of packages. Environment groups are collections of groups.
  * `group info` displays information about a group.
  * `group install` will install a group.
  * `history` displays a summary of install and remove transactions.
    * `undo` will reverse a specified transaction.

## Section 7.5 - Enabling Yum Software Repositories

### Enabling Red Hat Software Repositories

* `yum repolist all` - lists all available repositories.
* `yum-config-manager` - Adds, enables, or disables a repository.
* `/etc/yum.repos.d` - Contains ".repo" files which define a system's repositories.

### RPM Configuration Packages for Local Repositories

The following commands install the Red Hat Enterprise Linux 8 EPEL repository package:

```
$ rpm --import http://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-8
$ yum install http://dl.fedoraproject.org/pub/epel/8/x86_64/e/epel-release-8-2.noarch.rpm
```

The configuration files for repositories are stored in `/etc/yum.repos.d/`. Below is an example:

```
[epel]
name=Extra Packages for Enterprise Linux 8 - $basearch
#baseurl=http://download.fedoraproject.org/pub/epel/8/$basearch
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-8&arch=$basearch
failovermethod=priority
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-8
```


