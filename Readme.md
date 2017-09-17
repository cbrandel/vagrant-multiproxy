multiproxy
==========

Vagrant configuration for a 3 box environment with which a multi-Reverse-Proxy setup can be tested.

This is just a playground to fiddle around with a few Vagrant boxes, haproxy, httpd, and tomcat.

# Requirements
Vagrant Plugins:
- vagrant-hostmanager
- vagrant-bindfs

# Machines
Architecture is:

    lb -> web -> app

## lb
Loadbalancer machine

http://mysite/

Installed packages:
- haproxy

## web
Web layer machine

Installed packages:
- apache httpd
- php 7.1
- PHP ServerTester ()

## app
Appserver machine

Installed packages:
- apache httpd
- apache tomcat
- Java 1.8
- webapp-tester Webapp (https://github.com/cbrandel/webapp-tester)

### TODO
add SSL to loadbalancer

### License
MIT License

### Acknowledgements

Inspired by the following fine projects, which use Vagrant/ansible:

- https://github.com/ansible/ansible-examples
- https://roots.io/trellis/
- https://github.com/geerlingguy/ansible-role-haproxy
- https://devops.stackexchange.com/questions/1237/how-do-i-configure-ssh-keys-in-a-vagrant-multi-machine-setup
