# Docker Cheatsheet

## 1. Login to docker hub
```
docker login
```
Authentication key will be stored in .docker/config

```
docker logout
```

## 1. Containers
Create and start a container
```
docker container run -d --publish 80:80 --name webhost nginx
```
The first port is the port on the host. The second after the colon is the port in the container.
Opening a bash to container
```
docker container exec -it webhost bash
```

If container has no bash
```
docker container exec -it webhost sh
```

Containers executed with bash and sh will be automatically stopped upon exit.

To remove containers automatically upon stop:
```
docker container run -it --rm --name webhost nginx
```

## 2. Networking
Create network
```
docker network create my_network
```

Connect container to network on run
```
docker container run -d --name new_nginx --network my_app_net nginx
```

Inspect network
```
docker network inspect my_app_net
```

Connect a running container to new network
```
docker network connect my_app_net webhost
```

Remove an old network from a running container
```
docker netword disconnect bridge webhost
```

Inspect the configurations of the container
```
docker container inspect webhost
```

Default bridge network does not have built-in DNS service. It is always better to setup your own network for applications.

Create another DNS alias for DNS round-robin on two containers of the same service:
```
# Do this twice and we will have 2 elasticsearch instance with the dns alias search
docker container run -d --net elasticnet --net-alias search elasticsearch:2
docker container run -d --net elasticnet --net-alias search elasticsearch:2
```
Test that DNS round-robin is working:
```
docker container run --rm --net elasticnet alpine nslookup search
```

## 3. Building an Image

Image is an application binary and dependencies. No kernel, kernel drivers.

To tag an existing image:
```
docker image tag <old tag> <new tag>
```

To build a image, we need to write a Dockerfile. There is a sample Dockerfile in this folder.
Run the following on the folder containing the Dockerfile to build the image:
```
docker image build -t <dockername> path/to/Dockerfiledir
```

## 4. Using volumes and bind mount to persist data
To run a container with a specific volume
```
docker container run -d --name <container-name> -v <volume-name>:<data-path-in-container> <image-name>
```

Or we can create the following volume in dockerfile
```
VOLUME <volume name>:<data path in container>
```

To use a bind mount instead of volumes:
```
docker container run -d --name <container-name> -v <data-path-in-local-fs>:<data-path-in-container>
```

## 5. Docker Swarm:

To init swarm functionality:
```
docker swarm init
```