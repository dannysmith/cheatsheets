# Docker

To run a docker container:

```bash
docker run imagename:tag command
```

This a few things:
1. It downloads the docker image if it isn't available locally.
2. It builds a container from the image.
3. It runs the supplied command inside the new container.

NB: The most revent version of an image is always tagged with "latest".

Eg:

```bash
docker run ubuntu:latest echo "Hello World"
```

We can see the local images using the `docker images` command.

If we want to enter the container and interact with it, we can run bash inside of it, providing some flags:

```bash
docker run -it ubuntu:latest /bin/bash
```

This drops us into the container and allows us to work with it.

If we want to run a container in the background, we can pass the `-d` flag.

```bash
docker run -d ubuntu:10.04 ping google.com -c 500
```

(This will ping google 500 times, from inside the container, but we won't see the output dince it's running in the background).

## Viewing our containers

We can view any containers that are currently running with the `docker ps` command. If we want to see all our local containers (even those which have stopped running), we can use the `-a` flag: `docker ps -a`.

To view the logs from a particular container, we can use the short ID provided from `ps -a`:

```
docker logs 30f18970e8bf
```

## Mapping Ports

To run a webserver, we need to map the ports from the docker container to those on our host machine. We can do that by passing the `-P` flag to `docker run`.

```bash
docker run -d -P tomcat:7
```

This will run a container with tomcat 7 (a webserver) in detatched mode, and map the ports to those of the host machine. **Notice that there is no command specified**. Most images have *default* command specified. In the case of tomcat, it will run a tomcat process.

If we want to see which port has been mapped, we can run `docker ps` to see the current processes:

```
CONTAINER ID  IMAGE    COMMAND           CREATED         STATUS         PORTS         NAMES
93587f4ccc5a  tomcat:7 "catalina.sh run" 49 seconds ago  Up 48 seconds          0.0.0.0:32768->8080/tcp   compassionate_shockley

```

This shows us that port 8080 of the container has been mapped to port 32768 of the host machine. If we visit http://machine.com:32768, we should see tomcat running.

## Creating an Image 1: Commiting to a Container

If we make changes to a container, perhaps by installing some software, we can commit those changes to a new image:

```bash
$> docker commit 2901dbd15710 dannysmith/my_new_app:1.0

$> docker images
REPOSITORY            TAG   IMAGE ID      CREATED        VIRTUAL SIZE
dannysmith/my_new_app 1.0   21a50dd90b38  7 seconds ago  199.3 MB
```

I can then use that new image: `docker run -it dannysmith/my_new_app:1.0 bash`

## Creating an Image 2: Using a Dockerfile

```shell
FROM ubuntu:14.04 # Use this image and tag as the base. # Base image
RUN apt-get update && apt-get install -y vim rbenv ruby 
RUN git clone http://xxx
RUN useradd k -m -s /bin/bash -c "Danny Smith" danny
CMD pry # Default command to run if none is passed
```

(Note that each RUN command causes a new commit to be made, so it's a good idea to chain commands together with `&&` if they are related.)

Once we have a Dockerfile, we can build an image using the build command:

```shell
docker build [options] [path] # The path is the build context. This is where docker looks for your Dockerfile and the root of your packaged stuff.

docker build -t dannysmith/myapp
```


## Getting terminal access to  running container

We can shell into an already running container with `docker exec`. This executes the given command inside the specified container. I this case, we'll execute bash:

```bash
docker exec -it 682436daef6e bash
```


## Starting, Stoping and Deleting Containers

```bash
docker start my_container
docker stop my_container
docker rm my_container
```

We can also delete images:

```bash
docker images #List all images stored locally
docker rmi my_image:1.0 # Takes the form image:tag
```

## Using Dockerhub

We can push images to dockerhub using the push command: `docker push dannysmith/my_newapp:1.0`.


## Volumes

A Volume is a designated directory in a container, which is designed to persist data, independant of the container's lifecycle. Changes to volumes aren't included when we update a container and they persist when a container is deleted. They're particularly useful because:

1. We can map a folder on the host machine to a volume.
2. We can share vlumes between containers.

```bash
docker run -d -P -v /myvolume tomcat:7 # Mount /myvolume inside the container
docker run -it -v /data:/test tomcat:7 # Mount the /data folder on the host machine as /test in the container.
```

We shoudn't mount folder from a host machine in any production containers, since they're reliant on the underlying system, which defeats the whole point of docker.

We can also specify them in a Dockerfile

```shell
VOLUME /myvolume /anothervolume # Mount two voumes
```


## Container Networking

Remember, containers have their own network stack. We can map ports using `-P` or `-p` flags. An uppercase P will auto-map, whereas lowecase requires the ports to be specified:

```bash
docker run -d -p 12345:80 nginx:1.7 # Maps port 80 on the container to port 12345 on the host machine, making nginx available on port 12345 of the host.
```

But how does `-P` know which ports in the container to automatically map? We specify them in the Dockerfile:

```
EXPOSE 80 443
```

This tells docker to expose ports 80 and 44 when the `-P` flag is passed.

### Linking Containers

```bash
docker run -d --name dbms postgres:latest # Create a container called dbms
docker run -d --name website --link dbms:db ubuntu:latest tomcat
```

The second line creates a container called "website" which is linked to the dbms container. If we go into the `/etc/hosts` file on the second container, we'll see an entry mapping the IP address of the dbms container to "db". We can use `db` inside the website container to refer to the dmbs container.


## Running Docker on OSX

Docker uses core KErnal features of Linux to run, so we have to use an VM when on OSX. If we've installed the Toolbelt, we'll have the `docker-machine` tool available for this:

```bash
docker-machine ls # view list of available VMs. Ours is called "default".
docker-machine env default # get the environment variables for a given VM
eval "$(docker-machine env default)" # Configure the environment
```

## Using Docker Machine

As well as creating virtualbox VMs on OSX, docker-machine can also provision on clound providers like AWS and DigitalOcean.

To create a host, we can use the create command:

```bash
docker-machine create --driver virtualbox testhost # Create a virtualbox VM
docker-machine create --driver digitalocean --digitalocean-access-token xxx --digitalocean-size 2gb testhost
```

We can ssh into a machine with ssh if we need to:

```bash
docker-machine ssh testhost
```

## Using Docker Swarm

It's a tool that clusters Docker hosts and schedules containers.


