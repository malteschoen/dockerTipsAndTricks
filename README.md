### workflow instruction ###

1. if host system is windows, install WSL (Linux-on-Windows subsystem)
2. install docker (z.B. <https://docs.docker.com/engine/install/ubuntu>) - windows-nutzer müssen vorher WSL installieren
3. docker pull rvelseg/rheotool:of70_0.1
4. docker run -dit --rm -v "/home/schoen/rheoToolSharedFolder/:/app" "rvelseg/rheotool:of70_0.1"
   * \-dit bedeutet "detached interactive terminal"
   * \--rm bedeutet "instanz des gasts löscht sich selbst bei beendigung"
   * \-v steuert den sharedFolder und bekommt zwei parameter vor und hinter dem ":" übergegen
     * pfad auf dem host ist hier /home/schoen/rheoToolSharedFolder
     * pfad auf dem gast ist hier /app
     * entsprechend sähe es für einen windows-host so aus:
       * docker run -dit --rm -v "C:/Users/schoen/rheoToolSharedFolder/:/app" "rvelseg/rheotool:of70_0.1"
5. docker ps -a
   * zeigt alle aktuell laufenden images, man merke sich den lustigen namen am ende der zeile
6. docker attach [hier name der butze eintragen]
7. im docker-container dann:
   * cd /app
8. ausprobieren, ob's geklappt hat
   * rheoFoam
  
### Incomplete list of pre-made docker containers ###

* FoamExtend5.0 based on Ubuntu 22.04 by bgschaid
	* docker run -it --volume="$(pwd):/foamdata"   "bgschaid/openfoam_by_ansible:ubuntu2204_with_fe50"
	* Solids4Foam based on Ubuntu 18.04
	* docker run -it --volume="$(pwd):/shared"  "solids4foam/solids4foam-v2.0-foam-extend-4.1"
	* docker run -it --volume="$(pwd):/shared"  "solids4foam/solids4foam-v2.0-openfoam-v2012"
	* docker run -it --volume="$(pwd):/shared"  "solids4foam/solids4foam-v2.0-openfoam-9"
* OpenFOAM versions 4 through 11 provided by OpenFOAM Foundation Ltd., London, United Kingdom - based on various Ubuntu versions
	* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam4-paraview50"
	* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam5-paraview54"
	* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam6-paraview54"
	* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam7-paraview56"
	* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam9-paraview56"
	* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam10-paraview56"
	* docker run -it --volume="$(pwd):/shared" "openfoam/openfoam11-paraview510"	
* OpenFOAM versions 20.12 through 24.06 provided by OpenCFD Ltd. and thus by ESI Group, Bagneux, France - based on various Ubuntu versions
	* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2012"
	* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2106"
	* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2112"
	* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2206"
	* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2212"
	* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2306"
	* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2312"
	* docker run -it --volume="$(pwd):/shared" "opencfd/openfoam-run:2406"
* rheoTool under OF7 based on Ubuntu 18.04 by rvelseg
	* docker run -it --volume="$(pwd):/app" "rvelseg/rheotool:of70_0.1"

### Cheat sheet of commands
 
|  |  |
|--|--|
| docker ps | shows running container |
| docker ps -a | shows containers that ran recently |
| docker attach [name] | ssh into named docker (that must have been started detached) |
| `docker container prune` | removes all stopped containers |
| docker stop/start/remove foo | container foo stoppen/starten/removen |
| docker images | shows installed images |
| docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [CONTAINER_ID] | find container ip |
| docker image rm foo | image namens "foo" entfernen |
| sudo docker commit [CONTAINER_ID] [new_image_name] | commit changes |
| ctrl + p + q | detach session |
| `docker run --rm -it -v "$(pwd):/downloads:rw" ghcr.io/jauderho/yt-dlp:latest` -x --audio-format opus https://www.youtube.com/watch?v=dQw4w9WgXcQ | passes "-x"  and "--audio-format opus" parameters, downloads to current directory |

```

```
