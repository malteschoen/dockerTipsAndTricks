### what is docker? ###
We are using Docker as a rather hamfisted replacement for other virtualisation softwares (like virtualbox, proxxmoxx, etc.)

Docker can be installed on many types of host systems but reproduces the exact same guest behaviour anywhere.

We can exploit virtualisation to have many different versions of OpenFOAM installed on the same computer, e.g. both OF11 and OF2304.

There are three important terms to consider:
* "host" - that is the computer/system on which docker is installed
* "guest container" - that is the instance of the virtual computer, usually running some version of OpenFOAM
* "image" - we might wish to have multiple containers with the same basic configuration (e.g. OF11). To save space, the basic configuration is stored in an image. Upon guest container start-up, a 'carbon copy' of the image is made and used for the new container.

### workflow instruction for the example of a docker containing rheoTool ###

1. if host system is Windows, install WSL (Linux-on-Windows subsystem)
2. install docker (see <https://docs.docker.com/engine/install/>)
3. For a linux host:
   * docker run -dit --rm -v "/home/schoen/rheoToolSharedFolder/:/app" "rvelseg/rheotool:of70_0.1" 
   * \-dit means "detached with interactive terminal"
   * \--rm means "docker container will remove itself once we exit out"
   * \-v is responsible for the shared folder between host and docker container. Two parameters are given, one before the ':', one after it.
     * in our example, the folder on the host is /home/schoen/rheoToolSharedFolder
     * in our example, the folder on the guest is /app
   * "rvelseg/rheotool:of70_0.1" is the name of the author and the name of the image 
   * before entering the docker container, use "sudo chmod 777 -R /home/schoen/rheoToolSharedFolder" to fix any problems with access/write/delete permissions
4. For a windows host, the command would be
     * docker run -dit --rm -v "C:/Users/schoen/rheoToolSharedFolder/:/app" "rvelseg/rheotool:of70_0.1"
5. docker ps -a
   * shows all currently running containers
6. docker attach [hier name der butze eintragen]
7. once inside the docker container
   * cd /app
8. check whether stuff actually works
   * rheoFoam
  
### Incomplete list of pre-made docker containers ###

* FoamExtend5.0 based on Ubuntu 22.04 by bgschaid
	* docker run -it --volume="$(pwd):/foamdata"   "bgschaid/openfoam_by_ansible:ubuntu2204_with_fe50"
* Solids4Foam based on Ubuntu 18.04
	* docker run -it --volume="$(pwd):/shared"  "solids4foam/solids4foam-v2.0-foam-extend-4.1"
	* docker run -it --volume="$(pwd):/shared"  "solids4foam/solids4foam-v2.0-openfoam-v2012"
	* docker run -it --volume="$(pwd):/shared"  "solids4foam/solids4foam-v2.0-openfoam-9"
* OpenFOAM versions 4 through 11 provided by OpenFOAM Foundation Ltd., London, United Kingdom - based on various Ubuntu versions
	* docker run -it --volume="$(pwd):/home/openfoam" "openfoam/openfoam4-paraview50"
	* docker run -it --volume="$(pwd):/home/openfoam" "openfoam/openfoam5-paraview54"
	* docker run -it --volume="$(pwd):/home/openfoam" "openfoam/openfoam6-paraview54"
	* docker run -it --volume="$(pwd):/home/openfoam" "openfoam/openfoam7-paraview56"
	* docker run -it --volume="$(pwd):/home/openfoam" "openfoam/openfoam9-paraview56"
	* docker run -it --volume="$(pwd):/home/openfoam" "openfoam/openfoam10-paraview56"
	* docker run -it --volume="$(pwd):/home/openfoam" "openfoam/openfoam11-paraview510"	
* OpenFOAM versions 20.12 through 24.06 provided by OpenCFD Ltd. and thus by ESI Group, Bagneux, France - based on various Ubuntu versions
	* docker run -it --volume="$(pwd):/home/openfoam" "opencfd/openfoam-run:2012"
	* docker run -it --volume="$(pwd):/home/openfoam" "opencfd/openfoam-run:2106"
	* docker run -it --volume="$(pwd):/home/openfoam" "opencfd/openfoam-run:2112"
	* docker run -it --volume="$(pwd):/home/openfoam" "opencfd/openfoam-run:2206"
	* docker run -it --volume="$(pwd):/home/openfoam" "opencfd/openfoam-run:2212"
	* docker run -it --volume="$(pwd):/home/openfoam" "opencfd/openfoam-run:2306"
	* docker run -it --volume="$(pwd):/home/openfoam" "opencfd/openfoam-run:2312"
	* docker run -it --volume="$(pwd):/home/openfoam" "opencfd/openfoam-run:2406"
* rheoTool under OF7 based on Ubuntu 18.04 by rvelseg
	* docker run -it --volume="$(pwd):/app" "rvelseg/rheotool:of70_0.1"

### drop-in replacement podman

podman pull docker.io/bgschaid/openfoam_by_ansible:ubuntu2204_with_fe50

make sure to replace the 65536 in /etc/subgid and /etc/subuid with 200000

### Cheat sheet of commands
 
|  |  |
|--|--|
| docker ps -a | shows containers that ran recently |
| docker attach [name] | ssh into named container (that must have been started detached) |
| docker attach --latest | ssh into the container last launched |
| docker container prune | removes all stopped containers |
| docker stop/start/remove foo | container foo stoppen/starten/removen |
| docker images | shows installed images |
| docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [CONTAINER_ID] | find container ip |
| docker image rm foo | image namens "foo" entfernen |
| sudo docker commit [CONTAINER_ID] [new_image_name] | commit changes |
| ctrl + p + q while inside the container | detach session |
| "docker run --rm -it -v "$(pwd):/downloads:rw" ghcr.io/jauderho/yt-dlp:latest" -x --audio-format opus https://www.youtube.com/watch?v=dQw4w9WgXcQ | passes "-x"  and "--audio-format opus" parameters, downloads opus file extracted from youtube video to current directory |


