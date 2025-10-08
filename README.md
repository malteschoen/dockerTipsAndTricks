### what is docker? ###
We are using Docker as a rather hamfisted replacement for other virtualisation softwares (like virtualbox, proxxmoxx, etc.)

Docker can be installed on many types of host systems but reproduces the exact same guest behaviour anywhere.

We can exploit this 'virtualisation' to have many different versions of OpenFOAM installed on the same computer, e.g. both OF11 and OF2304. We can also use it to piggyback on the work of others, saving us the headache of configuring a complex software stack. Finally, the 'modular' (or 'layered' )design of Docker means that we can save on storage space - probably most relevant in highly scaling/highly parallel applications.

There are five important terms to consider:
* "host" - that is the computer/system on which docker is installed
* "guest container" - that is the instance of the virtual computer, usually running some version of OpenFOAM
* "image" - we might wish to have multiple containers with the same basic configuration (e.g. OF11). To save space, the basic configuration is stored in an image. Upon guest container start-up, a 'carbon copy' of the image is made and used for the new container.
* "interactive mode" - in this mode, the guest container behaves like a terminal session. Otherwise, the guest container will just try to execute a single command and shutdown afterwards.
* "compose" refers to the act of kludging together multiple layers to make our own images. We pretend that this doesn't exist (which we usually get away with).

### Docker, Podman or udocker? Desktop or not? ###
Docker Desktop used to be the natural starting point, but the license is troublesome:

`use of Docker Desktop in larger enterprises (more than 250 employees or more than $10 million USD in annual revenue) requires a paid subscription`.

You do not really need it, though. Everything 'mission critical' runs just fine with the command line interface (CLI). The CLI version is licensed far more permissively, see e.g. [here](https://metadata.ftp-master.debian.org/changelogs//main/d/docker.io/docker.io_26.1.5+dfsg1-9_copyright)

Then there's Podman, which behaves almost identically to Docker. It has no license problems plus some security and conceptual advantages (more linux-y), but the community is smaller and some odd bugs exist.

There is a Podman Desktop software: <https://github.com/podman-desktop/podman-desktop>. You might not need it though, since we're going into command line country anyway.

Finally, running Docker 'on hostile territory' (HPC cluster, Android phone) requires udocker
<https://github.com/indigo-dc/udocker>


### workflow instruction for the example of a docker containing rheoTool ###

1. if host system is Windows, install WSL (Linux-on-Windows subsystem) and start a session in it.
2. install docker (see <https://docs.docker.com/engine/install/>). If you find this troubling, go for Podman instead (see below).
3. On a terminal on the linux host / the linux subsystem
   * docker run -dit --rm -v "/home/schoen/rheoToolSharedFolder/:/shared" "guiguitcho/openfoam9-rheotool:latest"
   * \-dit means "detached with interactive terminal"
   * \--rm means "docker container will remove itself once we exit out"
   * \-v is responsible for the shared folder between host and docker container. Two parameters are given, one before the ':', one after it.
     * in our example, the folder on the host is /home/schoen/rheoToolSharedFolder. On Linux systems it is often convenient to use $(pwd) which is the directory at which our terminal is pointed.
     * in our example, the folder on the guest is /shared
   * "guiguitcho/openfoam9-rheotool:latest" is the 
     * name of the author (guiguitcho)
     * and the name of the image (openfoam9-rheotool)
     * and the version (latest)
   * before entering the docker container, use "sudo chmod 777 -R /home/schoen/rheoToolSharedFolder" to fix any problems with access/write/delete permissions
4. For a windows host, the command would be
     * docker run -dit --rm -v "C:/Users/schoen/rheoToolSharedFolder/:/shared" "guiguitcho/openfoam9-rheotool"
5. docker ps -a
   * shows all currently running containers
6. docker attach --latest
   * attaches your session to the most recently started container 
7. once inside the docker container
   * cd /shared
8. check whether stuff actually works - if you get an OpenFOAM-styled error, you're good
   * rheoFoam
  
### I wanna use podman instead ###
Easy enough, just pay attention to those steps:
- Installation instructions are here <https://podman.io/docs/installation>
- Also install podman-docker if offered by your distro
- If you cannot do that, simply replace "docker" with "podman" in any command
- in any case, you must pre-pend "docker.io" to images, so "docker run -dit --rm -v "/home/schoen/rheoToolSharedFolder/:/shared" "docker.io/guiguitcho/openfoam9-rheotool:latest"

### Incomplete list of pre-made docker containers ###

#### FoamExtend 5.0 based on Ubuntu 22.04 ####
* docker run -it --volume="$(pwd):/foamdata"   "bgschaid/openfoam_by_ansible:ubuntu2204_with_fe50"

#### Solids4Foam version 2.0 based on Ubuntu 18.04 ####
* docker run -it --volume="$(pwd):/shared"  "solids4foam/solids4foam-v2.0-foam-extend-4.1"
* docker run -it --volume="$(pwd):/shared"  "solids4foam/solids4foam-v2.0-openfoam-v2012"
* docker run -it --volume="$(pwd):/shared"  "solids4foam/solids4foam-v2.0-openfoam-9"
 
####  OpenFOAM versions 4 through 11 provided by OpenFOAM Foundation Ltd., London, United Kingdom - based on various Ubuntu versions ####

* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam4-paraview50"
* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam5-paraview54"
* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam6-paraview54"
* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam7-paraview56"
* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam9-paraview56"
* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam10-paraview56"
* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam11-paraview510"

#### OpenFOAM versions 12/13 provided by OpenFOAM Foundation Ltd., London, United Kingdom supplied through https://github.com/gerlero/docker-openfoam and https://hub.docker.com/u/microfluidica ####

* docker run -it --volume="$(pwd):/shared" "microfluidica/openfoam:12"
* docker run -it --volume="$(pwd):/shared" "microfluidica/openfoam:13"
  
####  OpenFOAM versions 20.12 through 24.06 provided by OpenCFD Ltd. and thus by ESI Group, Bagneux, France - based on various Ubuntu versions #### 
* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2012"
* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2106"
* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2112"
* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2206"
* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2212"
* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2306"
* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2312"
* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2406"

#### ~~rheotool 4.0 by Mr. Pimenta provided by rvelseg (Ubuntu 18.04, OF7)  - no longer recommended since it lacks features from rheoTool 5.0 such as the film casting solver~~####
	* ~~docker run -it --volume="$(pwd):/shared" "rvelseg/rheotool:of70_0.1"~~
	
#### rheotool 5.0 by Mr. Pimenta provided by rvelseg (Ubuntu 20.04, OF9)####
	* docker run -it --volume="$(pwd):/shared" "guiguitcho/openfoam9-rheotool

#### GOMA libraries (you still have to download and compile goma) ####
* docker run -it --volume="$(pwd):/shared"  "westonortiz/goma-libs"
 
###  podman bugs?

make sure to replace the 65536 in /etc/subgid and /etc/subuid with 200000

### Cheat sheet of commands
 
|  |  |
|--|--|
| docker ps -a | shows containers that ran recently |
| docker attach [name] | remote access into named container (that must have been started detached) |
| docker attach --latest | remote access into the container last launched |
| docker container prune | removes all stopped containers |
| docker stop/start/remove foo | container named "foo" is stopped/started/removed |
| docker images | shows installed images |
| docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [CONTAINER_ID] | find container ip |
| docker image rm foo | image named "foo" is removed from your hard-drive |
| sudo docker commit [CONTAINER_ID] [new_image_name] | commit changes made during your session to an image, creating a new image in the process |
| ctrl + p + q while inside the container | detach session - leaves container running in the background and returns you to the host linux machine |
| "docker run --rm -it -v "$(pwd):/downloads:rw" ghcr.io/jauderho/yt-dlp:latest" -x --audio-format opus https://www.youtube.com/watch?v=dQw4w9WgXcQ | passes "-x"  and "--audio-format opus" parameters, downloads opus file extracted from youtube video to current directory |


