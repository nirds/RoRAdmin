Setting up a new Ubuntu Rails Server
=============================

Make sure machine is up-to-date
----------------
We don't want to install outdated packages, so
```
sudo apt-get update
```

Also check the date, make sure its set correctly:
```
date
```


RVM
-----------
```
curl -L get.rvm.io | sudo bash -s stable
source ~/.rvm/scripts/rvm
```
Note: The Multi-User install instructions must be prefixed with the 'sudo' command. However, once the install is complete, and the instructions to add users to the rvm group is followed, the use of either sudo or rvmsudo is no longer required. The 'sudo' command is only to temporarily elevate privileges so the installer can complete it's work. If you need to use 'sudo' or 'rvmsudo' after the install is complete, some part of the install directions were not properly followed. This usually is because people execute the install as root, rather than executing the installation instructions from a non-privileged user account. See: https://rvm.io/support/troubleshooting/#sudo

Check to make sure that all RVM dependencies are in place:
```
rvm requirements
```

It'll list anything that needs to be installed via apt-get

```
Additional Dependencies:
# For Ruby / Ruby HEAD (MRI, Rubinius, & REE), install the following:
  ruby: /usr/bin/apt-get install build-essential openssl libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake libtool bison subversion
```      


Ruby
----
```
rvm install 1.9.3
rvm use 1.9.3 --default
```


RubyGems
--------
If RubyGems isn't installed, follow these instructions: http://rubygems.org/pages/download


Passenger
---------
```
gem install passenger 
```

nginx
-----
```
rvmsudo passenger-install-nginx-module
```
If you're missing any dependencies, passenger will complain and prompt you to ```apt-get install``` 

Apache
------
If you want to use Apache
```
sudo apt-get install apache2 apache2-mpm-prefork apache2-prefork-dev
rvmsudo passenger-install-apache2-module
```

Update ```/etc/apache2/apache2.conf```:
```
LoadModule passenger_module /usr/lib/ruby/gems/1.8/gems/passenger-2.2.2/ext/apache2/mod_passenger.so
PassengerRoot /usr/lib/ruby/gems/1.8/gems/passenger-2.2.2
PassengerRuby /usr/bin/ruby1.8
```

Enable ```mod_rewrite```

```
sudo a2enmod rewrite
```

Create a new conf for the test_app in ```/etc/apache2/sites-available/```
```
<VirtualHost :*80>
   ServerName test_app
   DocumentRoot /var/www/rails_apps/test_app/public
</VirtualHost>
```

```
sudo /etc/init.d/apache2 restart
```


Start nginx!
------------
```
sudo service nginx start
```

Confirm at http://localhost - should see the nginx "hello world" screen.




Node.js
-------
Likely need nodejs installed for Rails.

```
sudo apt-get install nodejs
```

MySQL
-----
```
sudo apt-get install mysql-server mysql-client libmysqlclient-dev
```



Create a test Rails app
-------------
```
cd /var/www/rails_apps/
rails new test_app
```

Point nginx at test_app
------------------------------
```
sudo vi /opt/nginx/conf/nginx.conf
```

```
server { 
  listen 80; 
  server_name example.com; 
  passenger_enabled on; 
  root /var/www/rails_apps/test_app/public; 
}
```
Restart nginx and visit ```http://localhost``` - should get a Rails welcome page (or at least an interesting error)

Note that this root may change if/when you start cap deploying and do deploy versioning.


Capistrano
----------


Add deploy user
---------------
```
adduser deploy
adduser deploy www
su - deploy
ssh-keygen -t rsa
exit
```







