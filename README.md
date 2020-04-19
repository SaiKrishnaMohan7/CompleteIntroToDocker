# Docker Intro

Creates a new environment that's isolated by namespace and limited by cgroups and chroot'ing you into it.
`cat /etc/issue`: shows which os (container) you are in

## Containers

A combination of 3 kernel features, _chroot_, _cgroups_ and _namespace_

### chroot - change root

- [chroot-bholt](https://btholt.github.io/complete-intro-to-containers/chroot)
- Allows to set the root dir of a new process (linux jail)
- `docker run -it --name docker-host --rm --privileged ubuntu:bionic`, `-it`: `interactive`
- `chroot myNewRoot/ bash`: change root to myNewRoot and run bash in there right after
- Now, for `bash` to run in there it neeeds dependencies, `ldd bash` gives the list of these dependencies
- `mkdir myNewRoot/lib{,64}`: make 2 new dirs in myNewRoot call it lib and lib64
- [Shell scripts realted to this](https://github.com/btholt/projects-for-complete-intro-to-containers/blob/master/chroot/setup.sh)

### namespaces

Hide processes from other tenants/users

- [namespaces-bholt](https://btholt.github.io/complete-intro-to-containers/namespaces)
- [shell scripts](https://github.com/btholt/projects-for-complete-intro-to-containers/tree/master/namespaces) for creating a namespace for change rooted envs

### cgroups

Allocate resources to the chroot'd, namespaced environments so that tenants don't hog each others resources. Namespaces give you process level isolation in the chroot'd env, the tenants of another env can still hog up resources like CPU, RAM. Google came up with this.

- [cgroups-bholt](https://btholt.github.io/complete-intro-to-containers/cgroups)
- [shell scripts](https://github.com/btholt/projects-for-complete-intro-to-containers/tree/master/cgroups)

## Docker Image

pre-made containers are called images

- [docker images without docker-bholt](https://btholt.github.io/complete-intro-to-containers/docker-images-without-docker)
- [shell scripts](https://github.com/btholt/projects-for-complete-intro-to-containers/tree/master/docker-images-without-docker)

- [docker images with docker](https://btholt.github.io/complete-intro-to-containers/docker-images-with-docker)
- Kill all running containers: `docker kill $(docker ps -q)`

### Dockerfile

A set of instructions to docker on how to build your container

- `docker run --init --rm --publish 3000:3000 node-app:1.0.0`: node, doesn't understand SIGTERM or SIGINT, terminate and interrupt.
  - Problem: can't stop the server, docker listens but node deosn't
  - Solution: running with `--init` (handled by a package called tini) handles the termintaion/interrupt signals
  - `--publish`: maps the container port to the host's port so that the server can listen

- `EXPOSE` instruction, flag requred when using this, -P
  - `docker run --init --rm -P node-app:1.0.0`: randomnly pick ports from the conatiner and map it to port 3000
  - better run: `docker run --init --detach -p 3000:3000 node-app:1.0.0` (--detach: run container in the background, -p is same as --publish)

- `docker build -t <tagName> -f <dockerfileName>`: We can our custom dockerfile, named, *tagName.Dockerfile* ex: node-alpine.Dockerfile

### Layers

Docker is smart enough to see the your FROM, RUN, and WORKDIR instructions haven't changed and wouldn't change if you ran them again so it uses the same containers it cached from the previous but it can see that your COPY is different since files changed between last time and this time, so it begins the build process there and re-runs all instructinos after that.

- Here, some problems occur, the install step (RUN npm ci) takes the most time and dockdr reruns all instructions from what it thniks has chnaged but in reality we chnageds something in our index file, which docker knows but we did nto chnage any dependencies. So for build performance, split *COPY into two COPY* instructions!
- ! this makes sure that we make use of docker's LAYER caching and keep the LAYER of installed packages!

### Bind Mounts

Run a container, without building it using a dockerfile, like a pre-built container (nginx) or say, I have a contianerized server that I am actively developing and I want the chnages to reflect in the contianer as I change it. *Sahre data between host and container*

ex: `docker run --mount type=bind,source="$(pwd)"/build,target=/usr/share/nginx/html -p 8080:80 nginx`

### Volumes

For persisting state between runs. Bind mounts are file systems managed by the host and shared with the container while volumes are managed by docker and mounted INTO the container. They can be shared, ex: databases

- `docker run --env DATA_PATH=/data/num.txt --mount type=volume,src=volume-mount-data,target=/data volume-mount`: send env vars to docker
- `docker prune volumes`: removes all local volumes
- `docker prune images`: removes all exited images

### Dev Containers in VS Code

- [Direct link](https://btholt.github.io/complete-intro-to-containers/visual-studio-code)

## Source

- [Notes; bholt](https://btholt.github.io/complete-intro-to-containers)
- [Shell Scripts](https://github.com/btholt/complete-intro-to-containers)
- [Mini projects](https://github.com/btholt/projects-for-complete-intro-to-containers)
