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