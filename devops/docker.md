

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


### docker roadmap

```bash
         +-----------------+
         |   Dockerfile    |
         +--------+--------+
                  |
                  v
          docker build
                  |
                  v
         +-----------------+
         |     Image       |
         +--------+--------+
                  |
          docker run / create
                  |
                  v
         +-----------------+
         |   Container     |
         +-----------------+
                  |
            docker compose up
                  |
                  v
         +-------------------------+
         |   multiple container    |
         +-------------------------+
```


## 1.  Docker commands

### Help
```bash
docker --version
docker --help
docker run --help                  # help of run       # docker ps --help
docker info                        # check docker is run or not
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
docker images                     # list all images installed 
docker image ls                   # same "docker images"
docker image prune -a              # remove all unused images
docker image rm ID                # remove an image from docker images
docker system prune                                    # remove all dangling images and clear caches
docker rmi myname              # rename an image
docker history ID
```

### docker lifecycle

```bash
docker pull nginx:latest                  # 1. download base image (dockerfile will downlaod this base image automatically)
docker build -t myimage .                  # 2. build image
docker create myimage                     # 3. create container but not started yet
docker start myimage                      # 4. start container
docker run myimage                       # 5. run container (create + start)
docker attach myimage                     # 5. attach container
docker rename myimage newimage            # 6. rename container
docker pause myimage                      # 6. pause container
docker unpause myimage                    # 7. unpause container
docker stop myimage                       # 8. stop container
docker kill myimage                        # 9. kill container
docker wait myimage                        # 10. wait container (block)
docker restart myimage                    # 11. restart container
docker rm myimage                         # 9. remove container (stop it first)
docker rmi myimage                        # 10. remove image
```

### Build an image

```bash
docker build .                             # Build from current directory
docker build /path/to/build/context          # Build from specific directory
docker build github.com/user/repo           # Build from Git repository
docker build - < context.tar.gz                # Build from tar archive
docker build -t my-app:latest .             # Build with a tag
docker build -f Dockerfile.dev .               # Build from specific Dockerfile
docker build --no-cache -t my-app              # Build without cache
docker build --memory=1g -t my-app .             # Set memory limits for build
docker build --build-arg NODE_ENV=production -t my-app .      # Set build arguments
```


### Cotainer

```bash
docker create busybox                       # create container id ==> copy this id for "docker start" 
docker start id                              # echo container id for us and run that command in background 
docker start -a id                              # start container with -a (attach) for run in front of us 
docker stop id                  # stop container
docker run = docker create + docker start     # container lifecycle
##################################################################################
docker run nginx                        # if I do not have nginx locally docker download it for me (docker pull)
docker run busybox:1.23                 # busybox is an image and 1.23 is a tag
docker run --name myname busybox  # run busybox and give it a name [no need to ps for get id]
docker run -d busybox         # run ls in busybox and keep it running in detached mode (not block terminal)
docker attach ID                                 # attach again to a running container
docker run busybox ls            # runs ls in busybox image (ls is executable inside the image)
docker run e3fq34f npm run test                                       # run command in container
docker run busybox ping 8.8.8.8  # ping google and continue running usefull for docker ps
# -i means attach to stdin and stdout and -t means  beatiful format on screen + auto complete + beatiful prompt
docker run -it busybox ls        # runs ls in busybox image in interactive mode (beatiful output)
docker run -it alpine                            # runs sh by default 
docker run -it busybox /bin/sh | sh  # run shell in busybox (isolated container)
docker run -e DB_DATABASE=foo -e DB_USERNAME=bar -e DB_PASSWORD=secret -d nodes-mysql  # -e means env variable (use these in your code)
docker logs id     # show logs of the container (usefull for detached container if you forget -a in start command)
docker logs container_name_or_id            # show 10 lines 
docker logs --follow container_name_or_id  # show --follow
docker rename a b                           # rename
docker run -rm busybox ls        # runs ls in busybox and delete it
docker rm ID                     # delete a container from docker ps
docker rm -f ID                     # delete a container evne if it is running
docker restart id
docker stop id                                 # send a SIGTERM signal to the container(anytime you can stop)
docker kill id                                  # send a SIGKILL signal to the container (kill immediately)
docker pause id                                  # pausing
docker unpause id                                 # unpause
docker wait id                                     # block the container
docker stop $(docker ps -a -q)                  # stop all containers
docker exec id redis-cli                             # execute a command in a running container (inside the container and kick us back)
docker exec -it id redis-cli                         # execute a command in a running container and stay interactive terminal
docker exec -it id sh | bash | zsh | powershell                      # getting a prompt   [ctrl + d = exit]
docker diff id                                       # list of changes
docker commit id                                   # save a container as an image
```


### info
```bash
docker ps                                    # list all containers
docker ps -a                                # list all containers including stopped
docker port id                                                   # show port of container
docker logs id                                                   # show logs
docker inspect id                                                # show info
docker top id                                                      # list of process
docker events id                                                   # list of evnets
docker stats id                                                     # resourse
docker diff id                                                     # list of changes
```

### cleanup
```bash
docker system prune                            # Cleans up dangling images, containers, volumes, and networks
docker system prune -a                          # plus stopped containers and all unused images
docker container prune                         # delete stopped containers
docker image prune                           # delete dangling images
docker image prune -a                           # plus not used as a container
docker volume prune                            # not used
```

### Registry & Repository

```bash
docker login
docker logout
docker search
docker pull
docker push
```

### docker prune

```bash
docker system prune
docker volume prune
docker network prune
docker container prune
docker image prune
```


### Docker Network
+ Apps inside container runs in an isolated docker network by default.
+ If you want to share the network, you need to connect the containers to the same network. For this, create a custome network for connecting containers.

##### Network types
1. bridge --->	Default network (private internal network) --->	Isolated container communication
2. host   --->	Shares host's network of local to container(no isolation) ---->	Increase performance
3. overlay --->	Multi-host networking (Swarm mode) --->	Docker Swarm clusters
4. macvlan --->	Assigns MAC addresses to containers --->	Legacy apps needing MAC addresses
5. none	---> No networking --->	For testing	Specialized security use cases

+ bridge
    + In the dockerfile of any image some ports are exposed.
    + for example we expose port 8080 in dockerfile
        ```dockerfile
        # In the Dockerfile
        EXPOSE 8080
        EXPOSE 8081
        ```
    + It means this application inside the container is designed to be accessed on ports 8080 and 8081
    + but it does not accessible from public internet, its just a metadata, a hint to other developers, tools, or Docker Compose
    + If you want to access them from the outside you have to map the ports with -p 8080:8080 to the host
    + with `-p <host_port>:<container_port>`  the host acts as a proxy
    + who can access?
        +  You, on the host machine: http://localhost:8080
        +  Anyone on your network: http://<your-host-ip>:8080
        +  other containers can use its private IP: http://172.17.0.2:8080 (e.g., http://192.168.1.100:8080)
+ host
    + 'host network' mode allows a container to share the host's networking namespace (uses the host's IP address)
    + Ports are Directly Bound (there is no need to specify -p in host network?)
    + who can access?
        + You, on the host machine: http://localhost:8080
        + Anyone on your network: http://<your-host-ip>:8080 (e.g., http://192.168.1.100:8080)
        + other containers cannot use a bridge network IP to reach it (172.17.0.2:8080 doesn't exist). 
        + other containers must use the host's IP (192.168.1.100:8080), just like any external machine.

```bash
docker run -p 8080:8080 imagenmae                                     # expose port local:container
docker run --network=host nginx                                   #
docker network ls                                                  # list all networks
docker inspect ID                                                  # inspect a container for details
docker network inspect bridge                                      # find all containers that have bridge network
docker network connect network_name ID                                # connect a container to a netowrk
docker network disconnect network_name ID                             # disconnect a container from its network
docker network create mynetwork                                     # create a custome network (bridge by default)
docker network rm mynetwork                                        # remove a network
docker network create --driver bridge --subnet 244.178.44.111/24 mynetwork   # create a network
docker exec -it web ping db                                       # ping between containers
```

### docker volume
+ Volumes separete data from the container, so if a container delete, the data remains.
+ Allows multiple containers to share the same data
+ Docker volumes are stored in a specific directory on the host system
    - Linux (/var/lib/docker/volumes/)
    - windows (\\wsl$\docker-desktop-data\data\docker\volumes\)

```bash
docker volume ls                       # List all volumes  (there are some volumes created by default)
docker volume create volume_name            # Create a named volume
docker volume inspect volume_name        # Inspect volume details
docker volume rm volume_name             # Remove a volume
docker volume prune               # Remove unused volumes
docker run -v volume_name:/path/in/container image_name    # use a volume for container
# if you delete the container, and then run a new container with the same volume_name the data is ramined in the same path in the new container
# /path/in/container should be the storage folder of db
docker run -v volume_name:/app/data:ro nginx         # :ro means readonly (useful for data sharing)
docker run -p 3000:3000 -v :/var/www/myproject imagne_name       #set voulme and exposing port
docker run -v vol1:/data1 -v vol2:/data2 nginx     # multiple volumes
```

### docker volume in docker compose

##### 1.
##### 1.


## 2. Create an Image

1. create a folder with "myimage" in your project(nodejs folder).
2. write a file with name "Dockerfile"
3. create an image with command in that folder ```docker build .``` ==> gives an id ===> docker run id
4. or: `docker build -t imagename .` ==> tag for name
5. `docker build -t username/imagename:latest .`   your dockerid/repo or projectname/version


#### step 0: dockerfile instruction

```bash
# 1. FROM
FROM ubuntu:20.04
FROM python:3.9-slim
FROM node:14-alpine
FROM scratch            # Empty base image

# 3. COPY                                    # COPY source destination
COPY ./index.js /usr/src/app/index.js
COPY . /app
COPY requirements.txt /tmp/

# 4. ADD                                      # Similar to COPY but with additional features like URL download and tar extraction
ADD https://example.com/file.tar.gz /tmp/
ADD source.tar.gz /app/                         # Automatically extracts
ADD . /app                                   # same as COPY

# 2. RUN
RUN apt-get update && apt-get install -y python3
RUN npm install
# Exec form (recommended for complex commands)
RUN ["/bin/bash", "-c", "echo 'Hello World'"]

# 5. CMD
CMD ["node", "index.js"]                 # Exec form (recommended for simple commands)
CMD ["npm", "start"]
CMD ["python", "app.py"]
CMD npm start                           # Shell form

# 6. ENTRYPOINT                                     # run as an executable
ENTRYPOINT ["python", "app.py"]                    # Exec form (recommended)
ENTRYPOINT python app.py                              # Shell form

# 7. WORKDIR
WORKDIR /path/to/directory
RUN pwd  # Output: /path/to/directory

# SHELL                                    # Changes the default shell
SHELL ["/bin/bash", "-c"]
RUN echo "Using bash now"
SHELL ["powershell", "-command"]

# 8. ENV                                  # Sets environment variables
ENV NODE_ENV production
ENV APP_HOME /app

# 9. ARG                                 # Defines build-time variables
ARG VERSION=latest
ARG USERNAME

# 10. LABEL                                 # Adds metadata to the image
LABEL version="1.0"
LABEL description="My application"
LABEL maintainer="dev@example.com"


# 11. EXPOSE                               # Documents which ports the container listens on.
EXPOSE 443
EXPOSE 3000/tcp

# 12. VOLUME
VOLUME /data
VOLUME ["/var/log", "/var/db"]

# 13. USER
USER nobody
USER 1000:1000
USER appuser:appgroup


# 14. AS (for multi-stage builds)
FROM node:14 As builder                    # first stage 
WORKDIR /app                               # Later stages in your Dockerfile can reference files or results from it.
COPY package.json .                       # builder is the name of the stage
RUN npm install                           # use --from=<name> to copy files or artifacts
COPY . .
RUN npm run build

FROM nginx:alpine                            # second stage
COPY --from=builder /app/dist /usr/share/nginx/html         # copy only the final built artifacts into a clean, minimal runtime image.
```


#### step 1: create a dockerfile
+ Each instruction in the docker file creates a layer
+ these layers are usefull for cashing
+ CMD runs at the time of `running` but RUN happens at the time of `building`.


```bash
# 0 . meta data
LABEL author="Jane <jane@example.com>"

# 1. Base Image-------
FROM alpine:latest                                      # use an existing image:tag as a base (will download)
FROM node:16-alpine as builder                  # set a name with 'as' for the new image 

# 2. Dependencies-------
COPY ./index.js /usr/src/app/index.js            # copy a file from your local into the image
COPY ./ ./                                        # copy whole of currnet folder from your local into the image
COPY src /app                                     # this gets error if /app folder doesnt exist in container ---> use slash at the end
COPY src /app/                                    # with a "/" at the end it means if this folder does not exist, create it
WORKDIR /usr/src/app                              # set the default directory for running your commands
RUN apk add --update redis                       # install a package as a dependency inside the image with "Alpine Package Keeper"
RUN apt-get update                                # update packages (debian base)
RUN npm install                                  # run this command
RUN echo "hello world" > /hello.txt              # run a command inside the image [RUN command will create a tmp container,exect that command, save that as a new container (snapshot) and remove it]


# 3. Run in startup-----------
CMD ["cat", "/hello.txt"]                        # CMD runs a command when our docker container starts [only one CMD in docker file]

# 4. another image
FROM nginx                                        #we can set multiple base images
COPY --from=builder /app/build /usr/share/nginx/html            #copy from another image
```

#### step2: build your docker file

```bash
docker images                                        # see your builded image
docker build -t imagename:tag .                      # -t for name:tag  # at the end specify the location of dockerfile
docker build -t imagename:tag --label "author= Jane" .     # meta data
docker build -f Dockerfile.dev -t writer/imagename .   # build an image from a custome Dockerfile
docker commit -c 'CMD ["redis-cli"]' ID                               # Create a New Image with redis-cli as Default Command
```

### sample nodejs dockerfile
```bash
FROM node:12-alpine           # Use small Alpine Linux image (npm is presenet in this image)
ENV PORT=5000                  # Set environment variables
ARG CLIENT_ID
COPY . /user/app/
WORKDIR /user/app/               # set default folder  (where `npm install` should run?)
RUN apk add --no-cache \         # install linux dependencies
  autoconf \
  bash \
  g++ \
  make 
RUN npm install                         # install packages
EXPOSE $PORT                              # Expose port for Node
CMD ["node", "server.js"]               # run server
```



## 3. docker-compose
+ Docker Compose is a tool for defining and running multi-container applications.
+ In windows and Mac it will install with "docker desktop", in linux you should install it separately.
+ with yaml file you can define and run multiple containers with one command
+ we define all the options of `docker run` like `-p 80:80`, `-e DB_HOST=89.0.142.86`, `-v /app/node_modules` in yaml file.
+ with docker-compose we do not need to use `docker run` multiple times for multiple containers.

```bash
# folder_name + service_name + index                # formula for name "docker compose ps" (we can change the first part)
docker-compose                                       # see all the commmands available
docker-compose up                                      # run yaml file (start all containers with interactive)
docker-compose up -d                                    # start all containers with detached mode
docker-compose up --build                              # its better if you changed the docker-compose.yml
docker-compose down                                      # stop all containers
docker-compose ps                                       # list of running services with docker-compose
docker-compose logs                                      # show logs
docker-compose top
docker-compose build                                     # rebuild all images
docker-compose exec <service> <cmd>
docker-compose logs -f <service>
docker-compose restart <service>
docker-compose stop <service>
docker-compose pause <service>
docker-compose unpause <service>
docker update --restart=no my-container                  # stop containers from starting automatically
```

### docker-compose instructions

```bash
# 1. version                                       # Specifies the Compose file format version.
version: '3.8'                            # Supported versions: '2', '3', '3.8', '3.9' etc.

# 2. services
services:
  web:
    # web service configuration
  database:
    # database service configuration

# 
```


### docker-compose.yml

```bash
version: '3'                               # 
services:                                 # list of containers
    redis-service:
        image: redis:latest
    node-app:
        build: .                         # use default build from current folder (Dockerfile in current folder)
        build:
            dockerfile: Dockerfile.dev  # specify the name not the path
            context: .                 # specify the path not the name
        restart: always                 # no, always, on-failure, unless-stopped
        ports:                           # use for services needing external access
            - "8080:8080"               # - means array in yaml
        expose: 8080                    # expose port for  inter-container communication
        volumes:
            - /app/node_modules         # do not map node_modules
            - .:/app                     # map current folder into: /app in container
        environment:                      # like -e in "docker run"
            - REDIS_HOST=redis-service     # name of container
            - REDIS_PORT=6379             # we can read environment variavles in progrmaing languages like `.env file`
    tests:                               # create  a container for running tests
        build:
            context: .
            dockerfile: Dockerfile.dev
        volumes:
            - .:/app
            - /app/node_modules
        command: ["npm", "run","test"]        # override default command
```
### docker-compose network
+ By default docker-compose sets a netowrk, and all the services joins to this network.
+ whenever we  start our docker-compose, it create a new network. you can see them in `docker network ls`. name of the network would be `folder_name+ default`

```yml
services:
    frontend:
        image: nginx
        ports:
            - 80:80
        networks:
            - my-network

networks:                     # list of custome networks
    my-network:             # now services can talk by container name
        name: my-network         # prefix name, by default is the name of folder [or project_name]
        driver: bridge          # type of network
networks:                         # if we had created a netowrk with command line
    my-network:                  # docker network create --driver bridge my-network
        external:               # do not create a new network, use an existing one
            name: my-network      
```

### docker file structre

```bash
project-root/
├── src/                        # Your main application code
│   ├── app/                    
│   ├── requirements.txt
│   └── ...
│
├── Dockerfile                  # Main (default) Dockerfile, usually for production
├── Dockerfile.dev              # Development version (with hot reload, debug tools, etc.)
├── docker-compose.yml          # Default compose (often production-ready)
├── docker-compose.dev.yml      # Compose file for development
├── docker-compose.test.yml     # Optional: for CI/testing pipelines
│
├── .dockerignore               # Ignore unnecessary files during image build like node_modules
├── .env                        # Environment variables (used by docker-compose)
│
├── scripts/                    # Optional: helper scripts for building, cleaning, etc.
│   ├── build.sh
│   ├── run_dev.sh
│   └── ...
│
└── README.md
```

### dockerignore

```bash
# Ignore Git files
.git
.gitignore

# Python
__pycache__
*.pyc
*.pyo
*.pyd
.env

# Node modules
node_modules

# Local configs
*.log
.vscode
.idea
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






