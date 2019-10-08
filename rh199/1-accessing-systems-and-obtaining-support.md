### Section 1.2 - Accessing the Command Line

* By default, there are 6 virtual consoles (tty).
* Virtual console tty1 represents the graphical login. It will create the graphical environment upon logon in the next available virtual console (tty2 for example).
* Private keys used for SSH public key authentication must only be readable by the user that owns the file (600 permissions).
* Upon a first SSH connection, the remote host will send it's host key. The client maintains a list of trusted host keys in relation to each server. This prevents man-in-the-middle attacks.

### Section 1.3 - Configuring SSH Key-based Authentication

The following files/directories are used by the SSH mechanism:

* `~/.ssh` - directory the contains the below SSH related files for a user.
* `~/.ssh/config` - SSH configurations at user level.
* `~/.ssh/known_hosts` - contains signatures of known connected hosts at user level.
* `~/.ssh/authorized_keys` - contains list of public keys to allow access from at user level.

The following commands can be used to manage SSH:

* The `ssh-keygen` command is used to create a private key and matching public key for SSH authentication. The keys are by default saved as `~/.ssh/id_rsa` and `~/.ssh/id_rsa.pub` files, respectively.
    * `-f` specify path and name of new key pair.
* If using private key passphrases, the `ssh-agent` program can temporarily cache passphrases in memory at the start of your session. Start it like so: `eval $(ssh-agent)`.
* The `ssh-add` command adds private keys to the `ssh-agent` program. You can provide a path to a key or allow it to load the `~/.ssh/id_rsa` key by default.
* The permissions modes must be `600` on private keys and `644` on public keys.
* The `ssh-copy-id` command copies the public key of the SSH keypair to the destination system. If no path to key is provided, it uses the default `~/.ssh/id_rsa.pub`.
    * `-i` specify path to public key.
* The `ssh` command is an SSH client used estalish the remote shell session.
    * `-i` use a private key to authenticate. If you don't specify path to private key, it uses the default `~/.ssh/id_rsa` file.

### Section 1.5 - Getting Help From Red Hat Customer Portal

* The Red Hat Customer Portal (https://access.redhat.com) provides customers access to documentation, downloads, tools, and technical expertise.
    * Access to official documentation.
    * Submit and manage support tickets.
    * Manage software subscriptions.
    * Obtain software downloads, updates, and evaluations.
    * Consult tools that can help you optimize the configuration of your systems.

* The Red Hat Support Utility, `redhat-support-tool`, provides a text-based interface that allows you to search Knowledgebase articles and to file support cases on the Customer Portal from you system's command line.
    * It stores credentials in `~/.redhat-support-tool/redhat-support-tool.conf`.
    * The `search` option returns matching Knowledgebase articles based on a search string.
    * The `kb` option returns matching Knowledgebase articles based on a Document ID.
    * The `opencase` option submits a Bug Report to Red Hat.

* The `sosreport` command generates a compressed tar archive of diagnostic information gathered from the running system.
* The `kdump` command creates a kernel crash dump core file. This is a backtrace which reports the active stack from at the point of a crash dump.

