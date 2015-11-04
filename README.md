# Dbox

Docker based Nginx, PHP, MariaDB and Redis linked container development platform.

This is a nicer and more dedicated script than using docker-compose.  This lets you have a nice web development set of machines.

* One MariaDB (mysql) container for ALL your mysql databases
* One Redis container for ALL your redis database needs
* One PHP-FPM container for all your php needs (composer, laravel, symfony...)
* One Nginx container that can host ALL your [PHP] websites, nginx uses the PHP-FPM container

With these 4 containers, you can run unlimited number of nginx sites, which is really great for a development box.



# Install

Dbox is only tested on a linux host machine.  Should work on mac too, who cares about Windows.  All my localhost machines are Linux Debian 8.

On your local machine, install docker the recommended way for your operating system, see https://docs.docker.com/engine/installation/

Install dbox as your main user, NOT root:

	curl -sS https://raw.githubusercontent.com/mreschke/dbox/master/dbox | bash -s init

This will create a ~/.dbox folder and git clone this dbox code into it + run dbox init which will pull in my containers and start them up.

Each container will store its persistent data in its own folder in ~/.dbox like

	~/.dbox/mysql
	~/.dbox/redis
	~/.dbox/web

I prefer to always work from /var/www, so I simply symlink on my localhost

	sudo ln -s ~/.dbox/web/www /var

Both the php and the web (nginx) contains can see the web/www folder, and they both have the /var/www symlink already in place.


## Upgrade

To upgrade all the containers, simply destroy them all, pull, then init.  Your data is safe in ~/.dbox

	dbox destroy
	dbox pull
	dbox init



# Usage - Basic

Remember, all containers use local mounted volumes at ~/.dbox so your data is safe and persistent even if you run dbox destroy.

During the install above, dbox is initialized and already running (all 4 containers running).

To stop all containers (but not destroy them), just run `dbox down`.  To start them back up just run `dbox up`.

To destroy the containers run `dbox destroy`, note, your actual code, databases (redis + mysql) data is perfectly save in your localhost ~/.dbox.
This simply destroys the containers, not your data.

You can pull in new containers (in case I make updates) with `dbox destroy` then `dbox pull` then `dbox init`

You can jump into the terminal (shell) of any container like so

	dbox mysql console
	dbox mysql shell

	dbox redis console
	dbox redis shell

	dbox php shell

	dbox web shell

Example: to run all your composer commands, just `dbox php shell` and do any php work required.  NOTE: each container has the non root user
called 'toor' setup.  So if you are working in the PHP container, and want to composer install, it is recommended to run

	dbox php shell
	su - toor

first.  That way all the files compose creates are as the toor user (UID 1000), which hopefull maps nicely to your localhost main user UID 1000.  If not, then all composer activity 
will be done as root, and your localhost ~/.dbox/web/www/project/vendor directory will be owned as root..which is not ideal. 


## Usage - Laravel Up and Running

All shared data on localhost is in ~/.dbox, so your www code would live in ~/.dbox/web/www but I like to keep everything in my local /var/www, so symlink it

	sudo ln -s ~/.dbox/web/www /var/

Now, since your localhost may or may not have PHP and composer, just use the PHP container to do all your PHP work

	dbox php shell

This should put you in the PHP container as the toor user (not root).  This container ALSO has the /var/www symlink to /data/www for consistency

Now pull in a fresh laravel

	composer self-update
	su - toor
	cd /var/www/
	composer create-project laravel/laravel --prefer-dist

Now in a new terminal (or exit the PHP container) log into the nginx (web) container and add a new nginx site.  Note the web container also has the symlink into /var/www

	dbox web shell
	nginx-new-site laravel.app /var/www/laravel/public

Back at your localhost, edit your hosts file and add

	127.0.0.1	laravel.app

Then visit http://laravel.app in your browser

For laravel configuration, the database hostname is 'mysql', user is 'root', password is 'techie'. The redis server hostname is 'redis'





