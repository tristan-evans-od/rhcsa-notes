## Section 10.1 - Managing Networking

* `ip link` - List all network interfaces available on your system.
* `ip addr` - View device and address information.
  * An active interface is `up`.
  * The `link/ether` line specifies the hardware address of the device.
  * The `inet6` line shows an IPv6 address, its network prefix length, and scope.
* `ip -s link show` - Will show statistics about network performance.
* `ping` is used to test connectivity.


### Troubleshooting Routing

* `ip route` - Shows the IPv4 routing table.
* `tracepath` - Trace the path that network traffic takes to reach a remote host through multiple routers.
* `ss` - Replaces `netstat`. 

## Section 10.3 - Configuring Networking from the Command Line

### Describing NetworkManager Concepts

`NetworkManager` is a daemon that monitors and manages network settings. Command-line and graphical tools talk to NetworkManager and save configuration files in the `/etc/sysconfig/network-scripts` directory.

* A `device` is a network interface.
* A `connection` is a collection of settings that can be configured for a device.
* Only one connection can be active for any one device at a time. Multiple connections may exist for use by different devices or to allow a configuration to be altered for the same device. If you need to temporarily change network settings, instead of changing the configuration of a connection, you can change which connection is active for a device. For example, a device for a wireless network interface on a laptop might use different connections for the wireless network at a work site and for the wireless network at home.
* Each connection has a `name` or ID that identifies it.
* The `nmcli` utility is used to create and edit connection files from the command line.

### Viewing Networking Information

The `nmcli dev status` command displays the status of all network devices.

The `nmcli con show` command displays a list of all connections.

### Adding a Network Connection

The `nmcli con add` command is used to add new network connections. 

The following command adds a new connection named `eno2` for the interface `eno2`. It gets IPv4 networking information using DHCP and autoconnects on startup.

`nmcli con add con-name eno2 type ethernet ifname eno2`

The following command creates an `eno2` connection for the `eno2` device with a static IPv4 address.

`nmcli con add con-name eno2 type ethernet ifname eno2 ipv4.address 192.168.0.5/24 ipv4.gateway 192.168.0.254`

### Controlling Network Connections

The `nmcli con up` command activates the connection name on the network interface it is bound to. 

The `nmcli dev dis` command disconnects the network interface device and brings it down.

### Modifying Network Connection Settings

`NetworkManager` connections have two kinds of settings:

* `static` connection properties, configured by the admin and stored in the configurations in `/etc/sysconfig/network-scripts/ifcfg-*`.
* `active` connection data, which the connection gets from a DHCP server and which are not stored persistently.

The `nmcli con mod` command is used to change the settings for a connection. These changes are are also saved in the `/etc/sysconfig/network-scripts/ifcfg-*`.

To set the IPv4 address and default gateway:

`nmcli con mod ens3 ipv4.address 192.0.2.2/24 ipv4.gateway 192.0.2.254`

### Deleting a Network Conection

The `nmcli con del` command deletes the specified connection from the system, disconnecting it from the device and removing it's network-script file.

### Who Can Modify Network Settings?

The `root` user can modify network connections with `nmcli`. Regular users that are logged in on the local console can also make many network configuration changes. The `nmcli gen permissions` command can be used to see what your current permissions are.

## Section 10.5 - Editing Network Configuration Files

By default, changes made with `nmcli con mod` are automatically saved to `/etc/sysconfig/network-scripts`. The file can also be manually edited with a text editor. After doing so, run `nmcli con reload` so that NetworkManager reads the configuration changes. The interface still needs to be restarted for the changes to take effect:

```
nmcli con reload
nmcli con down "ens3"
nmcli con up "ens3"
```

## Section 10.7 - Configuring Host Name and Name Resolution

* `hostname` - A command that displays or temporarily modifies the system's fully qualified host name.
* `/etc/hostname` - The file which the static hostname is defined.
* `hostnamectl` - Used to modify the `/etc/hostname` file and may be used to view the status of the system's FQDN.

### Configuration Name Resolution

The `stub resolver` is used to convert host names to IP addresses or the reverse. It determines where to look based on the configuration of the `/etc/nsswitch.conf` file. By default, the contents of the `/etc/hosts` file are checked first.

* `getent hosts` - Can be used to test host name resolution used the `/etc/hosts` file.

If an entry is not found in the `/etc/hosts` file, by default the stub resolver tries to look up the hostname by using a DNS nameserver. The `/etcresolv.conf` file control how this qurey is performed:

* `search` - a list of doman names to try with a short host name.
* `nameserver` - the IP address of a nameserver to query. Up to three nameserver directives may be given to provide backups.

`NetworkManager` updates the `/etc/resolv.conf` file using DNS settings in the connection configuration files. Use the `nmcli` to modify the connections.

The default behavior of `nmcli con mod eno1 ipv4.dns 10.92.128.40` is to replace DNS settings with the new IP list provided. A `+` or `-` symbol in front of the `ipv4.dns` argument adds or removes an individual entry:

`nmcli con mod eno1 +ipv4.dns 10.92.128.41`

### Testing DNS Name Resolution

* `host` - Can be used to test DNS server connectivity.

Note that DHCP automatically rewrites the `/etc/resolv.conf` file as interfaces are started unless you specify `PEERDNS = no` in the relevant interface configuration files. Set this using the `nmcli` command:

`nmcli con mod ens3 ipv4.ignore-auto-dns yes`
