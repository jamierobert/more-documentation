## Docker Commands

`docker ps` - List running containers, the same as `docker container ls`

`docker exec <container-name> ps` - Run a command on a already running container. `ps` just an example

`docker run <image-name> bash` - Run a command on a new container. if we use an image name that we do not have locally this will pull the image too.

`docker stats <container-name>` - Shows a containers stats.

`docker top <container name>` - See all running processes on a container.

`docker update <container-name>` -  Updates config. This requires different flags depending on the config that you are updating. You can control low level features related to Memory,CPU and IO. Including ram reservation, limiting swap space and kernel memory limit.

`docker build` - build an image from a docker file.

`docker cp <path/to/file> <container name>:<path/to/file>` - Copy files to a container. - To go back the other way use `docker container cp`

`docker kill <container-name>` - Kill a contianer. 

`docker diff <container name>` - Shows a history of file/directory changes since container creation.

`docker export` - Export a containers filesystem as a tar archive. This can be reversed with import.

`docker info` - System wide docker info.

`docker port <container name>` List all port info for a container

`docker logs` - Get logs, `-f` works as with tail.

`docker attach` - See all container IO in your terminal

`docker rm $(docker ps --filter "status=exited" -q)` - Bin all stopped containers !!

`docker volume prune` - remove all Docker Volumes

`docker container ls -f 'status=exited' -f 'status=dead' -f 'status=created'`


If you are integrating this with an automatic cleanup script, you can chain one command to another with some bash syntax, output just the container id's with -q, and you can also limit to just the containers that exited successfully with an exit code filter:

`docker container rm $(docker container ls -q -f 'status=exited' -f 'exited=0')`

All statuses;

- created
- restarting
- running
- removing
- paused
- exited
- dead



We also have loads for hub that are fairly git esque including `push` `pull` `tag` `login` `logout` - also the obligatory `stop` `start` and `restart` - Also some that are more interesting like `pause` and `unpause`.


[Docker Docs](https://docs.docker.com/engine/reference/commandline/cli/)


## Docker Compose Commands

We have most of the same commands with compose as we do with docker - some are different/exclusive to compose.


`docker-compose down` - Removes all containers

`docker-compose down -v` - Removes all containers and volumes


`docker-compose up` - Bring up containers

`docker-compose up -d` - Bring up containers as a detached process
	
`docker-compose logs -f` - like tail -f - ticks over logs as received


[Docker Compose Docs](https://docs.docker.com/compose/)