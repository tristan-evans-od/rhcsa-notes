## Section 9.1 - Identifying Automatically Started System Processes

The `systemd` daemon manages startup for Linux, including service startup and service management in general. It activates system resources, server daemons, and other processes both at boot time and on a running systems.

Daemons are processes that either wait or run in the background, performing various tasks. Generally, daemons start automatically at boot time and continue to run until shutdown or until they are manually stopped.

### Describing Service Units

`systemd` uses units to manage different types of objects. Some common unit types are listed below:

* Service units have a `.service` extension and represent system services. This type of unit is used to start frequently accessed daemons, such as a web server.
* Socket units have a `.socket` extension and represent inter-process communication (IPC) sockets that `systemd` should monitor. If a client connects to the socket, `systemd` will start a daemon and pass the connection to it. Socket units are used to delay the start of a service at boot time and to start less frequently used services on demand.
* Path units have a `.path` extension and are used to delay the activation of a service until a specific file system change occurs. This is commonly used for services which use spool directories such as a printing system.

### Listing Service Units

Use the `systemctl` command to explore the current state of the system. You can list units like so:

`systemctl list-units --type=service`

The above command will display the following columns:

* UNIT - The service unit name.
* LOAD - Whether `systemd` properly parsed the unit's configuration and loaded the unit into memory.
* ACTIVE - The high-level activation state of the unit. This information indicates whether the unit has started successfully or not.
* SUB - The low-level activation state of the unit. This information indicates more detailed information about the unit. The information varies based on unit type, state, and how the unit is executed.
* DESCRIPTION - The short description of the unit.

### Viewing Service States

View the status of a specific unit with `systemctl status`. If the unit type is not provided, systemdctl will show the status of a service unit, if one exists. See example below:

`systemctl status sshd.service`

This command will provide information on the following:

| Field | Description |
|-|-|
| Loaded | Whether the service unit is loaded into memory. |
| Active | Whether the service unit is running and if so, how long it has been running. |
| Main PID | The main process ID of the service, including the command name. |
| Status | Additional information about the service. |

It also provides information on the state of the application:

| Keyword | Description |
|-|-|
| loaded | Unit configuration file has been processed. |
| active (running) | Running with one or more continuing processes. |
| active (exited) | Successfully completed a one-time configuration. |
| active (waiting) | Running but waiting for an event. |
| inactive | Not running. |
| enabled | Is started at boot time. |
| disabled | Is not set to be started at boot time. |
| static | Cannot be enabled, but may be started by an enabled unit automatically. |

### Vierifying the Status of a Service

The `systemctl` command provides methods for verifying the specific states of a service. For example, use the following command to verify that a service unit is currently active (running):

`systemctl is-active sshd.service`

To check if it is enabled:

`systemctl is-enabled sshd.service`

To check if it failed during start up:

`systemctl is-failed sshd.service`

The above command either returns `active` if it is properly running or `failed` if an error has occurred during startup. In case the unit was stopped, it returns `unknown` or `inactive`.

The list all failed units:

`systemctl --failed --type=service`

## Section 9.3 - Controlling System Services

### Starting and Stopping Services

To start a service: 

`systemctl start sshd.service`

To stop a service:

`systemctl stop sshd.service`

### Restarting and Reloading Services

To restart a running service:

`systemctl restart sshd.service`

Some services can perform a "service reload", which simply causes a process to reapply it's configurations. To perform this:

`systemctl reload sshd.service`

In the case which you are not sure if a service has a reload function, you can tell it to try and reload. If it can't, then fallback to a restart:

`systemctl reload-or-restart sshd.service`

### Listing Unit Dependencies

Some services require that other services be running first, creating dependencies on the other services. Othe services are not started at boot time, but rather only on demand. In both cases, `systemd` and `systemctl` start services as needed whether to resolve the dependency or start an infrequently used service.

You can list a service's dependencies like so:

`systemctl list-dependencies sshd.service`

### Masking and Unmasking Services

At times, a system may have different services installed that are conflicting with each other. For example, there are multiple methods to manage mail servers (`postfix` and `sendmail`, for example). Masking a service prevents an administrator from accidentally starting a service that conflicts with others. Masking creates a link inthe configuration directories to the `/dev/null` file, preventing the service from starting.

To mask a service:

`systemctl mask sendmail.service`

To unmask a service:

`systemctl unmask sendmail.service`

### Enabling Services to Start or Stop at Boot

To start a service automatically at boot:

`systemctl enable sshd.service`

To disable a service from automatically starting at boot:

`systemctl disable sshd.service`

To verify if a service is enabled:

`systemctl is-enabled sshd.service`

## Section 9.5 - Selecting the Boot Target

### Rebooting and Shutting Down

To shut down:

`systemctl poweroff`

To reboot:

`systemctl reboot`

### Selecting a Systemd Target

A `systemd` target is a set of `systemd` units that the system should start to reach a desired state. The following table lists the most important targets:

| Target | Purpose |
|-|-|
| graphical.target | System supports multiple users, graphical and text based logins. |
| multi-user.target | System support multiple users and text based logins only. |
| rescue.target | `sulogin` prompt, basic system initialization completed. |
| emergency.target | `sulogin` prompt, `initramfs` pivot complete, and system root mounted on `/` read only. |

A target can be part of a another target. For example, the `grpahical.target` includes `multi-user.target`, which in turn depends on `basic.target` and others. You can view these dependencies with the following command:

`systemctl list-dependencies graphical.target | grep target`

To list available targets:

`systemctl list-units --type=target --all`

To switch (isolate) targets:

`systemctl isolate multi-user.target`

Isolating a target stops all services not required by that target (and its dependencies), and starts any required services not yet started. This only works on targets that have `AllowIsolate=yes` set in their unit files.

To set a default target:

`systemctl set-default graphical target`

To display the current default target:

`systemctl get-default`

To select a different target at boot time, append the below option to the kernel command line from the boot loader (GRUB):

systemd.unit=rescue.target`

## Section 9.6 - Resetting the Root Password

The below covers how to reset a lost `root` password.

### Resetting the Root Password from the Boot Loader

On RHEL8, it is possible to have the scripts that run from the `initramfs` pause at certain points, provide a `root` shell, and the continue when that shell exits. This can be used for debugging, as well as reseting a lost `root` password.

The access that `root` shell:

1. Reboot the system.
1. Interrupt the boot loader countdown by pressing any key.
1. Move the cur to the kernel entry to boot.
1. Press the `e` key to edit the selected entry.
1. Move the cursor to the kernel command line (this line that starts with `linux`).
1. Append `rd.break`. With that option, the s ystem breaks just before the system hands control from the `initramfs` to the actual system.
1. Press `Ctrl+X` to boot with the changes.

At this point, you are at a `root` shell prompt with the actual root file system on the disk mounted read-only on `/sysroot`. You will need to remount the filesystem in read/write mode.

To reset the `root` password from this point, use the following procedure:

1. Remount the `/sysroot` as read/write.

	`mount -o remount,rw /sysroot`

1. Switch into a `chroot` jail, where `/sysroot` is tread as the root of the file-system tree.

	`chroot /sysroot`

1. Set a new `root` password.

	`passwd root`

1. Make sure that all unlabeled files, including `/etc/shadow` at this point, get relabeled (SELinux) during boot.

	`touch /.autorelabel`

1. Type `exit` twice. The first command exits the `chroot` jail, and the second command exits the `initramfs` debug shell.

### Inspecting Logs

You can use the `journalctl` tool to inspect logs.

By default, the system journals are kept in the `/run/log/journal` directory, which means the journals are cleared when the system reboot. To instead store them persistently in `/var/log/journal`, set the `Storage` parameter to `persistent` in `/etc/systemd/journald.conf`.

To inspect logs of a previous boot, use the `-b` option. Pass it a negative number as an argument to display the logs of previous boots.

### Repairing Systemd Boot Issues

The `debug-shell` service spawns a `root` shell on `TTY9` early during the boot sequence. It simply needs to be enabled:

`systemctl enable debug-shell.service`

By appending either `systemd.unit=rescue.target` or `systemd.unit=emergency.target` to the kernel command line from the boot loader, the system spawns into a rescue or emergency shell instead of starting normally. Both of these shells require the `root` password.

The emergency target keeps the root file system mounted read-only.

The rescue target waits for `sysinit.target` to complete, so that more of the system is initialized (such as logging services and the file systems).

During startup, `systemd` spawns a number of jobs. If some of these jobs cannot complete, they block other jobs from running. You can list jobs to see if any are stuck:

`systemctl list-jobs`

## Section 9.9 - Repairing File System Issues at Boot

### Diagnosing and Fixing File System Issues

The below table lists common file system issues and the results/symptoms of these issues:

| Problem | Result |
|-|-|
| Corrupt file system | `systemd` attempts to repair the file system. If it can't, it drops user to an emergency shell. |
| Nonexistent device or UUID referenced in `/etc/fstab` | `systemd` waits for a set amount of time. If the device does not become available, the system drops the user to an emergency shell. |
| Nonexistent mount point in `/etc/fstab` | The system drops the user to an emergency shell. |
| Incorrect mount option specified in `/etc/fstab` | The system drops the user to an emergency shell. |

