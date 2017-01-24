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
* `ghost_nginx_sites`: Nginx sites configuration passed to [jdauphant.nginx](https://galaxy.ansible.com/list#/roles/466) role. Check its README for more information. The defaults configure a reverse proxy listening on port 80 and denying access to Ghost's admin page except for requests originated from localhost.

Internal variables, avoid changing:

* `ghost_fetch_url`: URL used for fetching Ghost. Defaults to `https://ghost.org/zip/ghost-latest.zip`.
* `ghost_fetch_dir`: Directory to store the Ghost zip. Defaults to `/tmp`.
* `ghost_nodejs_pin_priority`: Pin for `apt-preferences`. Defaults to `500`.
* `ghost_nodejs_path`: Nodejs binary path. Defaults to `/usr/bin/node`.

Dependencies
------------

* [SimpliField.node](https://galaxy.ansible.com/SimpliField/node/)
* [jdauphant.nginx](https://galaxy.ansible.com/jdauphant/nginx/)

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

For multiple Ghost instances per host:

    - hosts: servers
      roles:
        - role: mtpereira.ghost
          ghost_install_dir: /var/www/ghost_two
          ghost_config_server:
            host: "127.0.0.1"
            port: "2369"
          ghost_nginx_sites:
            ghost_two:
              - listen 8080
              - server_name {{ ghost_config_server.host }}
              - proxy_set_header X-Real-IP $remote_addr
              - proxy_set_header Host $http_host
              - location / { proxy_pass {{ ghost_internal_url }}; }
              - location ~ ^/ghost/setup { allow {{ ghost_nginx_admin_allowed_cidr }}; deny all; }
        - role: mtpereira.ghost
          ghost_install_dir: /var/www/ghost_two
          ghost_config_server:
            host: "127.0.0.1"
            port: "2368"
          ghost_nginx_sites:
            ghost_one:
              - listen 80
              - server_name {{ ghost_config_server.host }}
              - proxy_set_header X-Real-IP $remote_addr
              - proxy_set_header Host $http_host
              - location / { proxy_pass {{ ghost_internal_url }}; }
              - location ~ ^/ghost/setup { allow {{ ghost_nginx_admin_allowed_cidr }}; deny all; }

If you already have you own webserver setup for multiple instances, you can skip
the `ghosts_nginx_sites` definition.

License
-------

BSD

Author Information
------------------

Thanks to [nodesource](https://nodesource.com/) for the Nodejs packages repository

Thanks to [SimpliField](http://www.simplifield.com/) for the Ansible role.

Thanks to [jdauphant](https://github.com/jdauphant/) for the Nginx role.

[GitHub project page](https://github.com/mtpereira/ansible-ghost)

[Manuel Tiago Pereira](http://mtpereira.github.io)
