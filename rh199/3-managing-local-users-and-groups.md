## Describing User and Group Concepts

* `/etc/passwd` - file store information about users. Each line is a colon-seperated list of details about a particular user. See the example below.

`user01:x:1000:1000:User One:/home/user01:/bin/bash`

The above defines:

1. Username
1. User's password ("x" means it is kept in `/etc/shadow`)
1. UID of user
1. GID of user
1. Real name of user
1. Home directory of user
1. Default shell program of user


