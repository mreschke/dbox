# Dbox
Docker based Nginx, PHP, MariaDB and Redis linked container development platform.  Similar to Laravels homestead Vagrant dev box, but built using Docker for blazing fast local development.



# Install

This assumes a fresh linux system with a kernal capable of running Docker like Debian Jessie+ or Ubuntu 14.04+

## Install Docker

	apt-get install -y docker.io

## Install Dbox

	Run as your main user account, not root

	cd /tmp
	wget https://raw.githubusercontent.com/mreschke/dbox/master/dbox
	chmod a+x dbox
	./dbox up
