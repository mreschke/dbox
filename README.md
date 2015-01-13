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



# Install

This assumes a fresh linux system with a kernal capable of running Docker like Debian Jessie+ or Ubuntu 14.04+

Linux box does NOT need to have anything, no PHP, no Nginx, no Mysql or redis.  Just docker.io!


## Install Docker

	apt-get install -y docker.io

## Install Dbox

Run as your main user account, not root

	cd /tmp
	wget https://raw.githubusercontent.com/mreschke/dbox/master/dbox
	chmod a+x dbox
	./dbox init
	rm /tmp/dbox
	sudo ln -s ~/.dbox/www /var/www

Now copy or download your code into your local /var/www as usual



# Upgrade

To upgrade all the containers, simply destroy them all, pull, then init.  Your data is safe in ~/.dbox

	dbox destroy
	dbox pull
	dbox init
