## Chapter 15.1 - Managing Server Firewalls

### Firewall Architecture Concepts

* `netfilter` - a framework build into the kernel for network traffic operations such as packet filtering, network address translation, and port translation. Configured via utilities like `iptables`.
* `nftables` - a new filter and packet classification subsystem that has enhanced portions of the `netfilter` code. Faster packet processing, ruleset updates, and simultaneous IPv4 and IPv6 processing from the same rules.
* `nft` - a user-space utility used to configured `nftables`.
* `firewalld` - a dynamic firewall manager, a front end to the nftables framework using the `nft` command. Available from the `firewalld` RPM package. Network traffic is classified into zones.

### Configuring the Firewall

System administrators interact with `firewalld` in three ways:

* Directly edit configuration files in `/etc/firewalld`.
* The Web Console graphical interface.
* The `firewalld-cmd` command-line tool.
