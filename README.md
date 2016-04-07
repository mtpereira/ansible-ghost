Ghost [![Build Status](https://travis-ci.org/mtpereira/ansible-ghost.svg)](https://travis-ci.org/mtpereira/ansible-ghost)
=========

Installs Ghost, a blogging platform. By default it'll install the latest Ghost
version available from [Ghost's homepage](https://ghost.org)
but you can specify a git repository if you have Ghost under version control.

This role also takes care of Ghost's [issue #2639](https://github.com/TryGhost/Ghost/issues/2639) 
on hosts using libc 2.13 or older, making sure that npm's sqlite3 is compiled
beforehand. This has been tested and verified to work on Debian Wheezy.

Requirements
------------

This role takes care of installing only a Ghost blog. You need to have a server
prepared for that with:

**Nodejs. v.0.10.x** must be installed in the node.
(You can use for example [this role](https://github.com/Oefenweb/ansible-nodejs.git) to install node)

If you want to forward the port, use **Nginx**.
(You can use this [Nginx role](https://github.com/geerlingguy/ansible-role-nginx.git))

Quick (Full) Use
---------

`git clone https://github.com/Oefenweb/ansible-nodejs.git  {{ inventory_dir }}/roles/`
`git clone https://github.com/geerlingguy/ansible-role-nginx.git  {{ inventory_dir }}/roles/`
`git clone https://github.com/Tinker-Ware/ansible-ghost-blog.git  {{ inventory_dir }}/roles/`

`{{ inventory_dir }}/playbook.yml`


    - hosts: ghost_server
      roles:
         - { role: nodejs, sudo: yes }
         - role: ghost
         - { role: nginx, sudo: yes }


Role Variables
--------------

Required variables:

* `ghost_repo`: URL of your Ghost blog files. Here's an [example](https://github.com/Tinker-Ware/ghost-blog-site) If this variable is not define, A clean ghost blog will be installed.
* `ghost_install_dir`: Directory where Ghost will be installed. It is also the home of the user that runs Ghost. Defaults to `/var/www/ghost`
* `ghost_user_name`: Username for the user that runs Ghost. Defaults to `ghost`.
* `ghost_user_group`: Group for the user that runs Ghost. Defaults to `ghost`.
* `ghost_config_mail`: Ghost's [mail configuration](http://support.ghost.org/mail/). It expects a YAML dictonary. Defaults to `{}`.
* `ghosts_config_database`: Ghost's [database configuration](http://support.ghost.org/config/#database). It expects a YAML dictionary. Defaults configure a sqlite3 database, which is Ghost's default.
* `ghosts_config_server`: Ghost's [server configuration](http://support.ghost.org/config/#server). It expects a YAML dictionary. Defaults to localhost on port 2368.

Internal variables, avoid changing:

* `ghost_fetch_url`: URL used for fetching Ghost. Defaults to `https://ghost.org/zip/ghost-latest.zip`.
* `ghost_fetch_dir`: Directory to store the Ghost zip. Defaults to `/tmp`.

Local Testing
-------

Tests can be ran on Debian Wheezy and Ubuntu Trusty boxes by executing "vagrant up". There are the following ENV variables:

* `ANSIBLE_TAGS`: A list of tags, comma-separated, that will be ran by Ansible. Defaults to `all`.
* `ANSIBLE_VERBOSE`: Ansible's verbosity level. Defaults to `v`.

Example Playbook
----------------

`{{ inventory_dir }}/playbook.yml`


    - hosts: ghost_server
      roles:
         - { role: nodejs, sudo: yes }
         - role: ghost
         - { role: nginx, sudo: yes }

`{{ inventory_dir }}/host_vars/ghost_server`

    # Variables for Nodejs Role
    nodejs_version: 'nodejs-v010'

    # Variables for Ghost role
    ghost_nodejs_enabled: no
    ghost_nginx_enabled: no
    ghost_install_dir: '/opt/tinker/shared_files/ghost'
    ghost_user_name: 'ghost'
    ghost_user_group: 'ghost'
    ghost_repo: 'https://github.com/Tinker-Ware/ghost-blog.git'

    # Variables for nginx Role
    nginx_remove_default_vhost: true
    nginx_vhosts:
        - server_name: "blog.tinkerware.io"
        listen: "80"
        extra_parameters: |
          location / {
            proxy_set_header    Host $http_host;
            proxy_set_header    X-Real-IP $remote_addr;
            proxy_pass          http://127.0.0.1:2368;
          }


Author Information
------------------

This role is provided by the [Tinkerware](http://tinkerware.io) project
under a **GNU GPLv3** Licence.

Forked a while ago from [mtpereira](https://github.com/mtpereira/ansible-ghost)
