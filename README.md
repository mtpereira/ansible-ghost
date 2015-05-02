Ghost [![Build Status](https://travis-ci.org/mtpereira/ansible-ghost.svg)](https://travis-ci.org/mtpereira/ansible-ghost)
=========

Installs Ghost, a blogging platform. By default it'll install the latest Ghost version available from [Ghost's homepage](https://ghost.org).

By default, it installs the latest Node version available on [Nodesource's repository](https://deb.nodesource.com/node/) using the [nodesource.node](https://galaxy.ansible.com/list#/roles/1488) role.

By default, it installs and configures a Nginx proxy using the [jdauphant.nginx](https://galaxy.ansible.com/list#/roles/466) role.

This role also takes care of Ghost's [issue #2639](https://github.com/TryGhost/Ghost/issues/2639) on hosts using libc 2.13 or older, making sure that npm's sqlite3 is compiled beforehand. This has been tested and verified to work on Debian Wheezy.

Requirements
------------

None.

Role Variables
--------------

Required variables:

* `ghost_install_dir`: Directory where Ghost will be installed. It is also the home of the user that runs Ghost. Defaults to `/var/www/ghost'.
* `ghost_user_name`: Username for the user that runs Ghost. Defaults to `ghost`.
* `ghost_user_group`: Group for the user that runs Ghost. Defaults to `ghost`.
* `ghost_config_mail`: Ghost's [mail configuration](http://support.ghost.org/mail/). It expects a YAML dictonary. Defaults to `{}`.
* `ghosts_config_database`: Ghost's [database configuration](http://support.ghost.org/config/#database). It expects a YAML dictionary. Defaults configure a sqlite3 database, which is Ghost's default.
* `ghosts_config_server`: Ghost's [server configuration](http://support.ghost.org/config/#server). It expects a YAML dictionary. Defaults to localhost on port 2368.
* `ghost_nodejs_enabled`: Enables or disables installing nodejs. Defaults to `yes`.
* `ghost_nginx_enabled`: Enables or disables configuring a nginx proxy. Defaults to `yes`.
* `ghost_nginx_port`: Defines the nginx listening port. Defaults to `80`.

Internal variables, avoid changing:

* `ghost_fetch_url`: URL used for fetching Ghost. Defaults to `https://ghost.org/zip/ghost-latest.zip`.
* `ghost_fetch_dir`: Directory to store the Ghost zip. Defaults to `/tmp`.
* `ghost_nodejs_pin_priority`: Pin for `apt-preferences`. Defaults to `500`.
* `ghost_nodejs_path`: Nodejs binary path. Defaults to `/usr/bin/node`.
* `ghost_nginx_sites`: Nginx sites configuration passed to [jdauphant.nginx](https://galaxy.ansible.com/list#/roles/466) role. Check its README for more information. The defaults configure a reverse proxy listening on port 80 and denying access to Ghost's admin page except for requests originated from localhost.

Dependencies
------------

* [nodesource.node](https://galaxy.ansible.com/list#/roles/1488)
* [jdauphant.nginx](https://galaxy.ansible.com/list#/roles/466)

These roles can be installed by running `ansible-galaxy install -r requirements.yml`.

Local Testing
-------

Tests can be ran on Debian Wheezy and Ubuntu Trusty boxes by executing "vagrant up". There are the following ENV variables:

* `ANSIBLE_TAGS`: A list of tags, comma-separated, that will be ran by Ansible. Defaults to `all`.
* `ANSIBLE_VERBOSE`: Ansible's verbosity level. Defaults to `v`.

Example Playbook
----------------

    - hosts: servers
      roles:
         - mtpereira.ghost

License
-------

BSD

Author Information
------------------

Thanks to [nodesource](https://nodesource.com/) for the Nodejs packages repository and Ansible role.

Thanks to [jdauphant](https://github.com/jdauphant/) for the Nginx role.

[GitHub project page](https://github.com/mtpereira/ansible-ghost)

[Manuel Tiago Pereira](http://mtpereira.github.io)
