## Package Management

### The Red Hat Package Manager

* rpm - a container of files with instructions for adding, removing, and upgrading them.
* /var/lib/rpm - contains the RPM database, which tracks the version and location of every file in each RPM.

The Red Hat Portal gives access to the following RHEL 7 Server repositories:

* Red Hat Enterprise Linux Server - the main repository, which includes both the packages associated with the original installation of RHEL 7, along with updates.
* RHEL Server Optional - a large group of open-source packages, provided with no support from Red Hat.
* RHEL Server Supplementary - a collection of packages released under licenses other than open source, such as the IBM Java Runtime and Development Kit.
* RHEL Extras - includes Docker
* RHN Tools - client tools to subscribe to the Red Hat Network via a Satellite server, along with utilities for automating Kickstart installations.

Each repository includes a database of packages in a repodata subdirectory. That database includes information on each package and allows installation requests to include all dependencies.

The `rpm` command can be used to work with rpm packages. Here are some common options used with it:

* `-i` installs specified package
* `-U` upgrades specified package
* `-F` upgrades onliy existing packages
* `-vh` verbose output with hash marks
* `--force` useful for downgrading a package
* `-e` uninstall specified package

### RPM Installation Security
