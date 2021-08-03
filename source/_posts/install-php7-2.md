--------
title: install php7.2
date: 2018-06-14 10:39:49
categories: linux
tags:
    - php7
    - linux
    - lnmp
    - complie
--------


## How to Install PHP 7.2, 7.1 on CentOS/RHEL 7.4 & 6.9

### Install Yum Repository


First of all, you need to enable Webtatic and EPEL yum repositories on your system. Use the following command to install EPEL repository on your CentOS and Red Hat 7/6 systems

Use this command to install epel-release yum repository.

```
yum install epel-release
```

and now execute one of the following commands as per your operating system version to install Remi repository.

```
### For CentOS/RHEL 7 ###
rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm

### For CentOS/RHEL 6 ###
rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
```

### Install PHP 7.2

Use the following command to install PHP 7.2 on your CentOS system. This will enable remi-php72 yum repository and install the required packages.

```
yum --enablerepo=remi-php72 install php
```

Now use the following command to check installed PHP version on your system.

```
php -v


PHP 7.2.0 (cli) (built: Nov 28 2017 20:22:21) ( NTS )
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2017 Zend Technologies
```

### Install PHP Modules

You may also need to install additional PHP modules based on your application requirements. Below command will install some more useful PHP modules.

```
### For PHP 7.2 ###
yum --enablerepo=remi-php72 install php-xml php-soap php-xmlrpc php-mbstring php-json php-gd php-mcrypt

### For PHP 7.1 ###
yum --enablerepo=remi-php71 install php-xml php-soap php-xmlrpc php-mbstring php-json php-gd php-mcrypt
```

To find out other available PHP modules use the following command. Change the PHP version to search packages for different PHP version.

```
yum --enablerepo=remi-php72 search php | grep php72


php72.x86_64 : Package that installs PHP 7.2
php72-php.x86_64 : PHP scripting language for creating dynamic web sites
php72-php-bcmath.x86_64 : A module for PHP applications for using the bcmath
php72-php-brotli.x86_64 : Brotli Extension for PHP
php72-php-cli.x86_64 : Command-line interface for PHP
php72-php-common.x86_64 : Common files for PHP
php72-php-dba.x86_64 : A database abstraction layer module for PHP applications
php72-php-dbg.x86_64 : The interactive PHP debugger
php72-php-devel.x86_64 : Files needed for building PHP extensions
php72-php-embedded.x86_64 : PHP library for embedding in applications
php72-php-enchant.x86_64 : Enchant spelling extension for PHP applications
php72-php-fpm.x86_64 : PHP FastCGI Process Manager
php72-php-gd.x86_64 : A module for PHP applications for using the gd graphics
php72-php-geos.x86_64 : PHP module for GEOS
php72-php-gmp.x86_64 : A module for PHP applications for using the GNU MP
php72-php-imap.x86_64 : A module for PHP applications that use IMAP
php72-php-interbase.x86_64 : A module for PHP applications that use
php72-php-intl.x86_64 : Internationalization extension for PHP applications
php72-php-json.x86_64 : JavaScript Object Notation extension for PHP
php72-php-ldap.x86_64 : A module for PHP applications that use LDAP
php72-php-libvirt.x86_64 : PHP language binding for Libvirt
php72-php-libvirt-doc.noarch : Document of php-libvirt
php72-php-litespeed.x86_64 : LiteSpeed Web Server PHP support
php72-php-lz4.x86_64 : LZ4 Extension for PHP
php72-php-mbstring.x86_64 : A module for PHP applications which need multi-byte
php72-php-mysqlnd.x86_64 : A module for PHP applications that use MySQL
php72-php-oci8.x86_64 : A module for PHP applications that use OCI8 databases
php72-php-odbc.x86_64 : A module for PHP applications that use ODBC databases
php72-php-pdo.x86_64 : A database access abstraction module for PHP applications
```

