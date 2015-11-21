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

### Viewing our containers

We can view any containers that are currently running with the `docker ps` command. If we want to see all our local containers (even those which have stopped running), we can use the `-a` flag: `docker ps -a`.

To view the logs from a particular container, we can use the short ID provided from `ps -a`:

```
docker logs 30f18970e8bf
```

# Mapping Ports

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
