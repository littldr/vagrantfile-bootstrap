# Vagrantfile-Bootstrap

This repository contains my personal bootstrapping `Vagrantfile`. You are free to use it for your own projects.

Some parts of this `Vagrantfile` are configurable via an additional `.vagrantfile`.

## Features

* **Network**: The default configuration will be accessible via a private IP in your network. To make this easier the vagrant plugin `vagrant-hostsupdater` (see [Repo](https://github.com/cogitatio/vagrant-hostsupdater)) will update your `/etc/hosts` file according to your configuration. So you can access the guest system from your host via the given hostname or aliases.

* **User**: A new user is created based on the `USER_NAME` configuration. (Default is `vagrant`)

* **SSH**: By default all (configurable via `HOST_SSH_KEYS`) your public keys from `~/.ssh/*` will be added to the `authorized_keys` in the guest system. This makes it possible to connect via `ssh user@hostname`. `vagrant ssh` is still working for the `vagrant` user.

* **NFS**: Your current directory is mounted as `app` into your user's home via `NFS`.

* **tbd** in the future.

## Installation

Installation is simple, just copy the [`Vagrantfile`](./Vagrantfile) into your project, create a `.vagrantfile` with your configuration and run `vagrant up`.

## Configuration

To change the default configuration create an additional `.vagrantfile` in the same directory where you placed the `Vagrantfile`.
In this file you can simple set some environment variables.
This file will be parsed on every `vagrant` command and the used configuration will be printed.
```bash
# Sample
$ ls
. .. Vagrantfile .vagrantfile
$ cat .vagrantfile
BOX_NAME = 'ARTACK/debian-jessie'
USER_NAME = 'andi'
HOSTNAME = 'vagrant'
HOSTNAME_ALIASES = 'vagrant.local, vagrant-dev'
$ vagrant status
==>  config: Configuration:
==>  config: 	Box: ARTACK/debian-jessie
==>  config: 	User: andi
==>  config: 	Hostname: vagrant
==>  config: 	Hostname Aliases: vagrant.local, vagrant-dev
==>  config: 	Private IP: 192.168.150.150
==>  config: 	Used SSH Keys: /Users/Andi/.ssh/sample.pub, /Users/Andi/.ssh/sample2.pub
Current machine states:

default                   running (virtualbox)
#...
```

### Overview
| Variable                 | Default           | Notes                                                           |
| ------------------------ |:-----------------:| --------------------------------------------------------------- |
| `BOX_NAME`               | `base`            | Name of the used box image.                                     |
| `USER_NAME`              | `vagrant`         | This user will be created.                                      |
| `HOSTNAME`               | `vagrant`         | Hostname from which the system will be accessible from outside. |
| `HOSTNAME_ALIASES`       | Not set           | Hostname-Aliases.                                               |
| `PRIVATE_IP`             | `192.168.150.150` | IP for the vagrant box.                                         |
| `HOST_SSH_KEYS`          | `*`               | Names of the ssh-key in your `~/.ssh/` directory. Only this keys will be copied into the guest system |
| `AFTER_PROVISION_SCRIPT` | Not set           | Path to script which will be uploaded and executed after provisioning. |
