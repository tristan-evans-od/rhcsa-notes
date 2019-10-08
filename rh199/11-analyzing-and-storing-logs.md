## Chapter 11.1 - Analyzing and Storing Logs

### Describing System Log Architecture

The `systemd-journald` service collects event messages from manay sources (kernel, stdout and stderr from daemon, and syslog events). It restructures them into a standard format, writing them into a structured, indexed system journal. By default, the journal is stored on a file system that does not persist across reboots.

The `rsyslog` service reads syslog messages received by `systemd-journald` from the journal as they arrive. It then processes the syslog events, recording them to its log files or forwarding them to other serivces. It sorts and writes syslog messages to the log files that do persist across reboots in `/var/log`. 

## Chapter 11.3 - Reviewing Syslog Files

Each log message handled by `syslo` is categorized by a `facility` (a type of message) and a `priority` (the severity of the message). Available `facilities` are documented in `rsyslog.conf (5)` man page.

The following table lists the standard eight syslog `priorities` from highest to lowest.

| Code | Priority | Severity |
|-|-|-|
| 0 | emerg | System is unusable |
| 1 | alert | Action must be taken immediately |
| 2 | crit | Critical condition |
| 3 | err | Non-critical error condition |
| 4 | warning | Warning condition |
| 5 | notice | Normal but significant event |
| 6 | info | Information event |
| 7 | debug | Debugging-level message |

The `rsyslog` utility uses the facility and priority of log messages to determine how to handle them. This is configured in `/etc/rsyslog.conf` and any file in `/etc/rsyslog.d`.

### Log File Rotation

The `logrotate` tool rotates log files to keep them from taking up too much space in the file system containing the `/var/log` directory.

## Chapter 11.5 - Reviewing System Journal Entries

The `systemd-journald` service stores logging data in a structured, indexed binary file called the journal. You can retrieve messages from the journal using the `journalctl` command. It takes several useful options:

* `-n` displays a certain amount of lines at the end of the journal.
* `-f` tails the journal.
* `-p` lists journal entries of specified priority or higher.
* `--since` shows all logs since specified date. Takes arguments in "YYYY-MM-DD hh:mm:ss" format. Also takes arguments such as `yesterday`, `tomorrow`, `-1 hour`, `-1 day`.
* `--until` shows all logs until specified date. Takes arguments in "YYYY-MM-DD hh:mm:ss" format. Also takes arguments such as `yesterday` and `tomorrow`, `-1 hour`, `-1 day`.

## Chapter 11.7 - Preserving the System Journal

By default, the system journals are kept in the `/run/log/journal` directory, which means the journals are cleared when the system reboots. You can change the configuration settings of the `systemd-journald` service in `/etc/systemd/journald.conf` file to make the journals persist across reboot.

The above is accomplished via the `Storage` parameter in mentioned configuration file. The below outlines the different options:

* `persistent` stores journals in the `/var/log/journal` directory to persist across reboots.
* `volatile` stores journals in the `/run/log/journal` directory (non-persistent).
* `auto` rsyslog determines how to store logs. If `/var/log/journal` exists, it logs there.

## Chapter 11.9 - Maintaining Accurate Time

### Settings Local Clocks and Timezones

The `timedatectl` command shows an overview of the current time-related system settings. It also takes the below options:

* `list-timezones` shows all available timezones to chose from.
* `set-timezone` takes timezone as argument and configures system for it.
* `set-ntp` takes a boolean argument, instructing the system to use ntp or not.

The `tzselect` command is useful for identifying correct zoneinfo time zone names.

### Configuring and Monitoring Chronyd

The `chronyd` service keeps the usually-inaccurate local hardware clock on track by synchronizing it to the configured NTP servers. If no network connectivity is available, `chronyd` calculates the hardware clock drift, which is recorded in the driftfile specified in the `/etc/chronyd.conf` configuration file.
