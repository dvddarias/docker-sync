# Docker Sync

A script to synchronize docker images between hosts with emphasis on reducing the amount of data transfered. It does not depend on docker registry.

Lets assume you want to synchronize your local machine docker images with the ones on your `myamazingweb.com` server. You would run:

	>> python3 docker-sync user@myamazingweb.com
	Connecting to user@myamazingweb.com.
	Listing user@myamazingweb.com images...............................DONE
	Listing local images........................................DONE
	--> Report:
	current: ['squid:latest', 'ubuntu:latest', 'debian:8.0']
	need update: ['redis:latest']
	new: ['mongodb:latest', 'gogs:latest']


Nothing was synchronized yet, this is just to see the status of all the images:

* **Current** means the images have the exact same ID.
* **Need Update** means that the two images have the same name:tag but different ID.
* **New** means you dont have a local image with the listed tag.

To sync the redis image run

	>> python3 docker-sync user@myamazingweb.com redis:latest

Please run the script with `--help` for further details.

##How does it work

When synchronizing two images it connects via ssh to the remote server and:
 1. Dumps both images (local & remote) to the hard drive (`docker save`).
 2. Runs `rsync` over the ssh connection to synchronize the content of both files
 3. Loads the new image (`docker load`)
 4. Removes the image dumps on both computers.

Note: When synchronizing an image on the **New** list the script will try to look for the highest common parent on your local machine to run the `rsync` command, so it pays off **a lot** to use images with the same base image. If no common parent is found the whole image is compressed and transfered.

##Dependencies & Configuration

You **need** python > 3.4 on your local machine and both the user running the script and the user on the remote host most have permissions to run `docker` commands. It uses `ssh` to connect to the host so you should also have the the appropriate permissions. There are **no dependencies on the remote host** other than the commands: bash, ls, rsync, ssh that are already installed on most linux distributions.




