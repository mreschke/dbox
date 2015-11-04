# Dbox
Docker based Nginx, PHP, MariaDB and Redis linked container development platform.


# Usage

All containers use local mounted volumes at ~/.dbox so your data is safe and persistent even if you run dbox destroy.

	dbox up
	dbox down
	dbox destroy

	dbox mysql console
	dbox mysql shell

	dbox redis console
	dbox redis shell

	dbox php shell

	dbox web shell


## Example - Working Laravel

All shared data on localhost is in ~/.dbox, so your www code would live in ~/.dbox/web/www but I like to keep everything in my local /var/www, so symlink it

	sudo ln -s ~/.dbox/web/www /var/

Now, since your localhost may or may not have PHP and composer, just use the PHP container to do all your PHP work

	dbox php shell

This should put you in the PHP container as the toor user (not root).  This container ALSO has the /var/www symlink to /data/www for consistency

Now pull in a fresh laravel

	composer self-update
	cd /var/www/
	composer create-project laravel/laravel --prefer-dist

Now in a new terminal (or exit the PHP container) log into the nginx (web) container and add a new nginx site.  Note the web container also has the symlink into /var/www

	dbox php web
	nginx-new-site laravel.app /var/www/laravel/public

Back at your localhost, edit your hosts file and add

	127.0.0.1	laravel.app

Then visit http://laravel.app in your browser

For laravel configuration, the database hostname is 'mysql', user is 'root', password is 'techie'. The redis server hostname is 'redis'




# Install

This assumes a fresh linux system with a kernal capable of running Docker like Debian Jessie+ or Ubuntu 14.04+

Linux box does NOT need to have anything, no PHP, no Nginx, no Mysql or redis.  Just docker.io!


## Install Docker

	apt-get install -y docker.io

## Install Dbox

Run as your main user account, not root

	curl -sS https://raw.githubusercontent.com/mreschke/dbox/master/dbox  | bash -s init

Your local ~/.dbox/web/www folder will be available in the nginx and php containers.  I prefer to use /var/www so on your local box just create a symlink

	sudo ln -s ~/.dbox/web/www /var/www

Now copy or download your code into your local /var/www as usual



# Upgrade

To upgrade all the containers, simply destroy them all, pull, then init.  Your data is safe in ~/.dbox

	dbox destroy
	dbox pull
	dbox init
