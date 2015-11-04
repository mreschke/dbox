# Dbox

Docker based Nginx, PHP, MariaDB and Redis linked container development platform.

This is a nicer and more dedicated script than using your own docker-compose.
This lets you have a nice web development set of machines.

* One MariaDB (mysql) container for ALL your mysql databases
* One Redis container for ALL your redis database needs
* One PHP-FPM container for all your php needs (composer, laravel, symfony...)
* One Nginx container that can host ALL your [PHP] websites.

With these 4 containers, you can run unlimited number of nginx sites, which is really great for an instant and consistent docker based development setup.


This is a super quick easy setup.  Install docker, one line dbox installer, few lines to install laravel or whataver else and your up and running.  Dbox is a simple and single bash file, nothing to it.




# Install

Dbox is only tested on a linux host machine.  Should work on mac too, who cares about Windows.  All my localhost machines are Linux Debian 8.

On your local machine, install docker the recommended way for your operating system, see https://docs.docker.com/engine/installation/

Install dbox as your main user, NOT root:

	curl -sS https://raw.githubusercontent.com/mreschke/dbox/master/dbox | bash -s init

This will create a `~/.dbox` folder and `git clone` this dbox repo into it and run `dbox init` which will pull in my docker containers and start them up.

Each container will store its persistent data in its own folder in `~/.dbox` like

	~/.dbox/mysql
	~/.dbox/redis
	~/.dbox/web

I prefer to always work from `/var/www` (old habits), so I simply symlink on my localhost, but up to you...

	sudo ln -s ~/.dbox/web/www /var

Both the `php` and the `web` (nginx) containers can see the localhost shared `~/.dbox/web/www` folder which maps into the containers `/data/www` folder.  Both containers also have the `/var/www` symlink already in place.


## Upgrade

To upgrade all the containers, simply destroy them all, pull, then init.
Remember, your data (code, databases) are perfectly save inside your local ~/.dbox folder thanks to dockers persistent volumes!  We are simply destroying the containers and pulling in new images.

	dbox destroy
	dbox pull
	dbox init



# Usage - Basic

Remember, all containers use local mounted volumes at ~/.dbox so your data is safe and persistent even if you run dbox destroy.

During the install above, dbox is initialized and already running (all 4 containers running).
You can use basic docker commands as usual like `docker ps -a` or `docker images -a` as needed, but use dbox to start/stop/destroy them my containers.

To stop all containers (but not destroy them), just run `dbox down`.  To start them back up just run `dbox up`.  Remember your data is persistent in ~/.dbox.

To destroy the containers run `dbox destroy`, note, your actual code, databases (redis + mysql) data is perfectly save in your localhost ~/.dbox.
This simply destroys the containers, not your data.

You can pull in new containers (in case I make updates) with `dbox destroy` then `dbox pull` then `dbox init`

You can jump into the terminal (shell) or database console of any container like so

	dbox mysql console
	dbox mysql shell

	dbox redis console
	dbox redis shell

	dbox php shell

	dbox web shell

NOTE, the `php` container is where all your actual PHP code is executed via PHP-CLI or PHP-FPM.  While the `web` container is the actual
nginx server, which if hosting PHP files, interacts with the `php` container via nginx php-fpm port 9000.

Example: to run all your composer commands, just `dbox php shell` and do any php work required.  NOTE: each container has the non root user
called 'toor' setup.  So if you are working in the PHP container, and want to `composer install` or `composer update`... it is recommended to run

	dbox php shell
	su - toor

That way all the files composer creates are as the toor user (UID 1000), which hopefull maps nicely to your localhost main user UID 1000.  If not, then all composer activity 
will be done as root, and your localhost `~/.dbox/web/www/project/vendor` directory will be owned as root..which is not ideal.
There will always be misc UID problems with container shared volumnes like this, manage wisely.


## Usage - Laravel Up and Running

All shared data on localhost is in ~/.dbox, so your www code would live in ~/.dbox/web/www but I like to keep everything in my local /var/www, so symlink it

	sudo ln -s ~/.dbox/web/www /var/

Now, since your localhost may or may not have PHP and composer, just use the PHP container to do all your PHP work

	dbox php shell

Note, `dbox php shell` drops you into the `php` containers shell as the `root` user.  Sometimes it is ideal to run commands as the primary `toor` user which uses UID 1000.
Simply switch to toor with `su - toor` when needed.

This should put you in the PHP container as the root.  This container ALSO has the /var/www symlink to /data/www for consistency

Now pull in a fresh laravel

	composer self-update
	su - toor
	cd /var/www/
	composer create-project laravel/laravel --prefer-dist

Now in a new terminal window, log into the nginx `web` container and add a new nginx site with my easy `nginx-new-site` helper script.
Note the web container also has the symlink into `/var/www` for consistency (I like `/var/www`)

	dbox web shell
	nginx-new-site laravel.app /var/www/laravel/public

Back at your local host, edit your hosts (`/etc/hosts`) file and add

	127.0.0.1	laravel.app

Now visit http://laravel.app in your local browser

For laravel configuration, the linked database hostname is `mysql`, user is `root`, password is `techie`. The linked redis server hostname is 'redis'.

Super quick, easy Laravel.  And, uh, 100x faster than homestead because no virtualization (unless your dumb enough to run docker on a mac, boo...nah, love macs but dockers for linux else run zones or jails)





