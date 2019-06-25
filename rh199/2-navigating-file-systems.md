## The File-system Hierarchy

* /usr - Installed software, shared libraries, include files, and read-only program data.
  * /usr/bin - user commands.
  * /usr/sbin - system administrator commands.
  * /usr/local - locally customized software.
* /etc - Configuration files specific to this system.
* /var - Variable data that should persist between boots (databases, cache directories, log files, printer-spooled documents, and website content).
* /run - Runtime data for processes started since the last boot (process ID files and lock files).
* /home - Home directories are where regular users store their personal data and configuration files.
* /root - Home directory for the administrative superuser, root.
* /tmp - A world-writable space for temporary files. Files which have not been accessed, changed, or modified for 10 days are deleted from this directory automatically. The `/var/tmp` directory is the same, but lasts 30 days.
* /boot - Files needed in order to start the boot process.
* /dev - Contains special device files that are used by the system to access hardware.

The following directories have identical contents to their coutners located in /usr:

* /bin -> /usr/bin
* /sbin -> /usr/sbin
* /lib -> /usr/lib
* /lib64 -> /usr/lib64

