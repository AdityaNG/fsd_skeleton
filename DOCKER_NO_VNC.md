# Creating the docker container from scratch

The following set of commands differ as they are either executed on
1. Your machine                 \[METAL\]
2. Inside the Docker Container  \[DOCKER\]

The above labels will signify the same

# Running as a Docker Container

You can download and run the adityang5/fsd:vnc container using the following command
With VNC
```bash
sudo docker run -it --rm -p 6080:80 -p 5900:5900 adityang5/fsd:vnc
```
Without VNC 
```bash
sudo docker run -it \
    --env="DISPLAY" \
    --env="QT_X11_NO_MITSHM=1" \
    --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" \
    adityang/fsd:novnc \
    rqt
export containerId=$(docker ps -l -q)

```

## Install Without VNC [Better Frame Rates]

Alternatively, you can use this ros-kinetic container which does not use VNC.

```bash
# [METAL]
sudo docker run -it \
    --env="DISPLAY" \
    --env="QT_X11_NO_MITSHM=1" \
    --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" \
    osrf/ros:kinetic-desktop-full \
    rqt
export containerId=$(docker ps -l -q)
```

Above, we made the container's processes interactive, forwarded our DISPLAY environment variable, mounted a volume for the X11 unix socket, and recorded the container's ID. This will fail at first and look something like this, but that's ok:
```bash
No protocol specified
rqt: cannot connect to X server unix:0
```

To fix the above, refer to : http://wiki.ros.org/docker/Tutorials/GUI
A quick unsafe fix for the above is the following command:

```bash
xhost +local:root # for the lazy and reckless
```


If you opted to use the contianer without VNC in another terminal, use the ps command to find the container you ust launched and use it to login
```bash
# [METAL]
sudo docker ps 		# To get Container ID [CID]
sudo docker container exec -it [CID] bash
```


## Getting the container setup

The above should log you in as root into the container. Now get setup using the following

```bash
# [DOCKER]
sudo apt update
sudo apt upgrade

sudo apt-get install checkinstall ros-kinetic-catkin python-catkin-tools
```
Now clone the repos and run `./update_dependencies.sh -f`. After sourcing setup.bash you can run `catkin build`

```bash
# [DOCKER]
cd ~
git clone https://github.com/AdityaNG/fsd_skeleton.git
git clone https://github.com/AMZ-Driverless/fssim.git
cd ~/fsd_skeleton

./update_dependencies.sh -f

echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
source ~/.bashrc

catkin build
```

## Launching 

In two seperate terminals : 

Launch the fssim_interface
```bash
# [DOCKER]
source ~/fsd_skeleton/fsd_environment.sh
roslaunch fssim_interface fssim.launch
```

Next launch the control_meta
```bash
# [DOCKER]
source ~/fsd_skeleton/fsd_environment.sh
roslaunch control_meta trackdrive.launch
```

## Saving the container

Use docker commit to save the container so you can get back to it later

```bash
# [METAL]
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```

To list out the saved images use 
```bash
# [METAL]
sudo docker images
```

To run your image again, use 
```bash
# [METAL] without VNC
sudo docker run -it \
    --env="DISPLAY" \
    --env="QT_X11_NO_MITSHM=1" \
    --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" \
    [REPOSITORY[:TAG]] \
    rqt
export containerId=$(docker ps -l -q)
```