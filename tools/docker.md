

# Docker
Docker is a virtualization software

## Why Docker?
Dockerized apps are completely portable and can run anywhere.

## Docker vs virtual machine
docker = Virtualise App layer
Virtual Machine = Virtualise kernel + App layer

1. Docker images are much smaller and faster
2. Virtual machine is more secure as it provides full OS-level isolation while docker has Process-level isolation.
3. Virtual Machine is a full os ( os kernel)

## Docker keywords
1. Image= a package that are built from a Dockerfile and stored in Docker hub. (contain files,folders, source code, env config)
2. Container= is a running instance of a Docker image (Created via `docker run`) [we can run multiple container from one image]
3. Docker Registery = Dockerhub is the biggest public docker registery. Cloud providers(AWS) provide personal private registery for companies.
4. Image tag = Each Image has a tag which is version. like Redis 6 which is for redis version 6. [latest tag]
5. volume = Volumes are a mechanism for storing data outside containers and share files between containers

## os
systemcall ==================> requests a service from the os
fs snapshot =================>the state of the file system at a specific moment, including all the files


namespacing =================> isolation resourses per process ===> python3 for chrome and python2 for firefox
control groups=cgroups   ===> limit amoint of resourses used per process ==> just 2 GB ram for chrome
docker container = namespacing + cgroups
docker image      = fs snapshot  + startup command



## commands

### Help
```bash
docker --version
docker --help
docker run --help                  # help of run       # docker ps --help
```

### Connect to dockerhub
```bash
docker search search_word	        # Search  in dockerhub for images.
docker login	                   # Authenticate to dockerhub
docker logout
docker pull user/image:tag	              # Downloads an image from dockerhub.
docker pull nginx                        # download by defaul "latest" tag
docker push user/image:tag	           # Uploads an image to dockerhub.
```

### Images

1. Unused Image: if you pull an image using the docker pull command but don’t start any containers using that image, the pulled image would be considered an unused image.

2. dngling image: A dangling image is an image that neither has a repository name nor a tag. It appears in Docker image listings as <none>:<none>.(if you use previous tag for new image, previous image lost that tag)


```bash
docker images -a                 # list all images including not taged    
docker images                     # list all images installed [docker image ls]
docker image prune -a              # remove all unused images
docker image rm ID                # remove an image from docker images
docker system prune                                    # remove all dangling images and clear caches
```

### Cotainer

```bash
docker ps                                    # list all containers
docker ps -a                                # list all containers including stopped
docker create busybox                       # create container id ==> copy this id for "docker start" 
docker start id                              # echo container id for us and run that command in background 
docker start -a id                              # start container with -a (attach) for run in front of us 
docker stop id                  # stop container
docker run = docker create + docker start     # container lifecycle
##################################################################################
docker run busybox:1.23                 # busybox is an image and 1.23 is a tag
docker run nginx                        # if I do not have nginx locally docker download it for me (docker pull)
docker run -d busybox         # run ls in busybox and keep it running in detached mode (not block terminal)
docker attach ID                                 # attach again to a running container
docker run --name myname busybox  # run busybox and give it a name [no need to ps for get id]
docker run busybox ls            # runs ls in busybox image (ls is executable inside the image)
docker run e3fq34f npm run test                                       # run command in container
docker run busybox ping 8.8.8.8  # ping google and continue running usefull for docker ps
# -i means attach to stdin and stdout and -t means  beatiful format on screen + auto complete + beatiful prompt
docker run -it busybox ls        # runs ls in busybox image in interactive mode (beatiful output)
docker run -it alpine                            # runs sh by default 
docker run -it busybox /bin/sh | sh  # run shell in busybox (isolated container)
docker logs id     # show logs of the container (usefull for detached container if you forget -a in start command)
docker logs container_name_or_id            # show 10 lines 
docker logs --follow container_name_or_id  # show --follow
docker run -rm busybox ls        # runs ls in busybox and delete it
docker rm ID                     # delete a container from docker ps
docker stop id                                 # send a SIGTERM signal to the container(anytime you can stop)
docker kill id                                  # send a SIGKILL signal to the container (kill immediately)
docker stop $(docker ps -a -q)                  # stop all containers
docker exec id redis-cli                             # execute a command in a running container (inside the container and kick us back)
docker exec -it id redis-cli                         # execute a command in a running container and stay interactive terminal
docker exec -it id sh | bash | zsh | powershell                      # getting a prompt   [ctrl + d = exit]
```

### Docker Network
+ Apps inside container runs in an isolated docker network by default, so we can run the same app running on the same port multiple times.

##### Network types
1. bridge --->	Default network (private internal network) --->	Isolated container communication
2. host   --->	Shares host's network stack ---->	High-performance apps needing host networking
3. overlay --->	Multi-host networking (Swarm mode) --->	Docker Swarm clusters
4. macvlan --->	Assigns MAC addresses to containers --->	Legacy apps needing MAC addresses
5. none	---> No networking --->	For testing	Specialized security use cases

```bash
docker run -p 8080:8080 imagenmae                                     # expose port local:container
docker run -p 3000:3000 -v $(pwd):/var/www/myproject imagnename       #set voulme and map a reference from currnet dir in local to container
docker run -p 3000:3000 -v /app/node_modules imagename                 # set voulme and map the node_module from container to current dir in local
docker network ls                                                  # list all networks
docker network inspect bridge                                      # inspect a network
docker inspect ID                                                  # inspect a container
docker network connect mynetwork ID                                # connect a container
docker network disconnect mynetwork ID                             # disconnect a container
docker network create mynetwork                                                 # create a network (bridge)
docker network rm mynetwork                                        # remove a network
docker network create --driver bridge --subnet 244.178.44.111/24 mynetwork   # create a network
docker exec -it web ping db                                       # ping between containers
```

### docker volume
```bash
docker run busybox -v /mylocaldir:/mycontainerdir ls # set a volume
```

## create an Image

1. create a folder with "myimage"
2. write a file with name "Dockerfile"
3. create an image with command in that folder ```docker build .``` ==> gives an id ===> docker run id
4. or: `docker build -t imagename .` ==> tag for name
5. `docker build -t username/imagename:latest .`   your dockerid/repo or projectname/version

```bash
docker build -t write/imagename .   # build an image from a folder
docker build -f Dockerfile.dev -t writer/imagename .   # build an image from a custome Dockerfile
```


```bash
# 1. Base Image-------
FROM alpine                                      # use an existing image as a base (will download)
FROM node:16-alpine as builder                  # set a name for the new image node:<version>-<os>

# 2. Dependencies-------
RUN apk add --update redis                       # install a package as a dependency inside the image with "Alpine Package Keeper"
RUN echo "hello world" > /hello.txt              # run a command inside the image [RUN command will create a tmp container,exect that command, save that as a new container (snapshot) and remove it]
COPY ./index.js /usr/src/app/index.js            # copy a file from your local into the image
COPY ./ ./                                        # copy whole of currnet folder from your local into the image
WORKDIR /usr/src/app                              # creates all the directories. set the default directory in the start so you can use "COPY ./ ./"


# 3. Run in startup-----------
CMD ["cat", "/hello.txt"]                        # run a command when it starts [will run on the brand our new container]
FROM nginx                                        #we can set multiple base images
COPY --from=builder /app/build /usr/share/nginx/html            #copy from another image
```

**create manually with no dockerfile**

```bash
docker run -it alpine sh                                             # run a base image
apk add -- update redis                                              # run manaully your commands inside alpine
# keep open the container. run a new termial and run `docker ps` for getting ID 
docker commit -c 'CMD ["redis-cli"]' ID                               # in new terminal run this commnd for create a new container for ruunig ID 
```

## docker-compose

```bash
docker-compose up                                      # start all containers with interactive
docker-compose up -d                                    # start all containers with no interactive
docker-compose up --build                              # its better if you changed the docker-compose.yml
docker-compose down                                      # stop all containers
docker-compose ps                                       # read the docker-compose.yml file in current directory 
```

## docker-compose.yml

```bash
version: '3'
services:                                 # list of containers
    redis-service:
        image: redis:latest
    node-app:
        build: .                         # use default build from current folder (Dockerfile in current folder)
        build:
            dockerfile: Dockerfile.dev  # specify the name not the path
            context: .                 # specify the path not the name
        restart: always                 # no, always, on-failure, unless-stopped
        ports:
            - "8080:8080"               # - means array in yaml
        volumes:
            - /app/node_modules         # do not map node_modules
            - .:/app                     # map current folder into: /app in container
        environment:
            - REDIS_HOST=redis-service     # name of container
            - REDIS_PORT=6379
    tests:                               # create  a container for running tests
        build:
            context: .
            dockerfile: Dockerfile.dev
        volumes:
            - .:/app
            - /app/node_modules
        command: ["npm", "run","test"]        # override default command
```


## tags
```bash
"bullseye", "buster", or "stretch",   ===> debian
```
## Facts
Docker Compose creates a bridge network by default
subnet: IP addresses available for containers within a specific Docker network.
gateway: The gateway acts as the router for the Docker network. It directs traffic between containers within the same network and to external networks 

## Troubleshooting
Networking
docker run --name netshoot --rm -it nicolaka/netshoot /bin/bash
