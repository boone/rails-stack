#rails-stack

Setup a RHEL/CentOS System to Host a Rails App

This playbook will setup a RHEL/CentOS server for hosting Ruby on Rails
applications hosted by Passenger Standalone. You can then deploy your apps with
Capistrano.

## Features

* Sets up a Rails-app-ready RHEL/CentOS server
* Firewall
* MySQL
* node.js installed for Rails asset compiling
* Passenger-standalone for hosting the app via nginx
* yum-cron notifications about package updates

## You Need

* A RHEL/CentOS 6.x server with root access
* A Rails app (MySQL-based) set up to deploy with Capistrano
* Ansible on your local machine (Macs can install using homebrew)
* A public SSH key defined in ~/.ssh/id_rsa.pub

## Install the Ansible Role

Install the Galaxy role. It will also install the
[rvm1-ansible](https://github.com/rvm/rvm1-ansible) dependency.

```
ansible-galaxy install boone.rails-stack
```

## Playbook Setup

In your Rails app directory, create a file named `production` with the IP
addresses of one or more production servers.

```
[all]
192.168.0.1
10.10.1.1
```

Add a `site.yml` playbook to your app:

```yaml
- hosts: all
  user: root
  vars:
    # app_name: defaults to Rails directory name, set here to override
    unix_password: "secret_unix_password!"
    mysql_root_password: "MySQL root Pass 42"
    mysql_user_password: "User Password for MySQL :)"

  roles:
    - { role: boone.rails-stack }
```

*Note*: you may want to use an
[Ansible vault](http://docs.ansible.com/ansible/playbooks_vault.html)
to securely store your passwords, especially if you want to commit your
Ansible playbook.

Run the playbook!

```
ansible-playbook -i production site.yml
```

If all goes well, it should totally configure the server for your app, and you
should see a placeholder site at the server's IP address.

IMAGE

Add a `Passengerfile.json` to your app (set the app name on the last line) and
commit it to your repo.

```json
{
  "environment": "production",
  "port": 80,
  "daemonize": true,
  "user": "app_name"
}
```

## Deploy Your Rails App

```
cap deploy:migrations
```

If this completes successfully, your Rails app should be running on your server,
just like that.

## Maintenance

All you should have to do to maintain this setup is:

* Keep the server up to date with system package updates.
* Keep your app up to date with Rails and other gem updates.

## Contributing

GitHub issues and especially pull requests welcome.

## TODOs

* Log rotation
* PostgreSQL support
* Tests

## Contact

[@boonedocks](https://twitter.com/boonedocks) on Twitter
