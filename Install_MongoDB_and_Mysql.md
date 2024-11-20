# How to install mongodb and mysql on macOS and Linux

## On mac using Homebrew service

### To install Homebrew, open Terminal and run:

```sh
$ /usr/bin/ruby -e \
"$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

### Then install MySQL using Homebrew:

```sh
$ brew install mysql
```


    1.3 Install brew services:

        $ brew tap homebrew/services
    
    1.4 Load and start the MySQL service:

        $ brew services start mysql

    1.5 Set the root password:

        $ mysqladmin -u root password 'yourpassword'
        
    Now your MySQL server is ready.

1. On Linux / Debian and Ubuntu

    2.1 Setup MySQL PPA

        $ wget http://repo.mysql.com/mysql-apt-config_0.8.9-1_all.deb
        $ sudo dpkg -i mysql-apt-config_0.8.9-1_all.deb

    2.2 Install mysql

        $ sudo apt update 
        $ sudo apt install mysql-server

    2.3 Secure MySQL Installation

        $ sudo mysql_secure_installation

