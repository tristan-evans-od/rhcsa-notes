## Chapter 13.1 - Scheduling Recurring System Jobs

The most common way to schedule recurring system jobs is using `cron`. There are various places to setup cronjobs:

* `/etc/crontab`
* `/etc/cron.d/`
* `/etc/cron.hourly/`
* `/etc/cron.daily/`
* `/etc/cron.weekly/`

The below is a description of how to format a job in a crontab script:

```
 .---------------- minute (0 - 59)
 |  .------------- hour (0 - 23)
 |  |  .---------- day of month (1 - 31)
 |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
 |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue ...
 |  |  |  |  |
 *  *  *  *  * user-name  command to be executed
```

The command `run-parts` called from the `/etc/cron.d/0hourly` file runs the `/etc/cron.hourly/*` scripts. It also runs the daily, weekly, and monthly jobs, but is called from a different configuration file called `/etc/anacrontab`.

* `/etc/anacrontab` makes sure that important jobs always run, and are not skipped accidentally because the system was turned off or hibernating when the job should have been executed.
* `/var/spool/anacron` is a directory which contains files for each of the daily, weekly, and monthly jobs to determine if a particular job has run. When `crond` starts a job from `/etc/anacrontab`, it updates timestamps of the files.

### Introducing Systemd Timer

The systemd init program has brought with it `systemd timer units`. A `systemd timer unit` activates another unit of a different type (such as a service) whose unit name matches the timer unit name.

An example if the `sysstat-collect.timer`, which is a `systemd timer unit` provided by the `sysstat` package. It simply collects system statistics every 10 minutes. The following output shows the configuration lines of `/usr/lib/systemd/system/sysstat-collect.timer`:

```
[Unit]
Description=Run system activity accounting tool every 10 minutes

[Timer]
OnCalendar=*:00/10

[Install]
WantedBy=sysstat.service
```

## Chapter 13.3 - Managing Temporary Files

There are two main places to store temporary files on a RHEL system:

* `/tmp` - used to store temporary files that persist across reboots.
* `/run` - used to store temporary files that do not persist across reboots.

The `systemd-tmpfiles` tool provides a structured and configurable method to manage temporary directories and files.

When a system boots up, one of the first service units executed is `systemd-tmpfiles-setup`. It reads the following configuration files from the following directories to decide what it does:

* `/usr/lib/tmpfiles.d/`
* `/run/tmpfiles.d/`
* `/etc/tmpfiles.d/`

Based on the configurations defined in the above locations, the `systemd-tmpfiles --create --remove` command executed by the `systemd-tmpfiles-setup` service unit will delete specified files and/or create (update permissions of) specified files.

### Cleaning Temporary Files with a Systemd Timer

The systemd timer unit `systemd-tmpfiles-clean.timer` triggers `systemd-tmpfiles-clean.service` on a regular interval, which in turn executes the `systemd-tmpfiles --clean` command. This ensures that long-running systemd do not fill up their disks with stale data.

The below is a snippit of the `systemd-tmpfiles-clean.timer` file:

```
[Unit]
Description=Daily Cleanup of Temporary Directories
Documentation=man:tmpfiles.d(5) man:systemd-tmpfiles(8)

[Timer]
OnBootSec=15min
OnUnitActiveSec=1d
```

* The parameter `OnBootSec=15min` indicates that the service unit called systemd-tmpfiles-clean.service gets triggered 15 minutes after the system has booted up. 
* The parameter `OnUnitActiveSec=1d` indicates that any further trigger to the systemd-tmpfiles-clean.service service unit happens 24 hours after the service unit was activated last.

### Clean Temporary Files Manually

The command `systemd-tmpfiles --clean` parses the same configuration files as the "--create" paramater, but instead of creating files and directories, it will purge all files which have not been accessed, changed, or modified more recently than the maximum age defined in the configuration file.

The format of the configuration files for `systemd-tmpfiles` is detailed in the `tmpfiled.d(5)` manual page.
