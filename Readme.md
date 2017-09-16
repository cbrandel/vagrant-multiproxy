multiproxy
==========

Vagrant configuration for a 3 box environment with which a multi-Reverse-Proxy setup can be tested.

# Requirements
Vagrant Plugins:
- vagrant-hostmanager
- vagrant-bindfs

# Machines
Architecture is:

    lb -> web -> app

## lb
Loadbalancer machine

https://mysite/

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
- ServerTester Webapp (https://github.com/cbrandel/ServerTester.git)
