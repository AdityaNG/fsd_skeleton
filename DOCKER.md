# Creating the docker container from scratch

The following set of commands differ as they are either executed on
1. Your machine                 \[METAL\]
2. Inside the Docker Container  \[DOCKER\]

The above labels will signify the same

## With VNC

Download and run the following ros-kinetic docker with vnc enabled on port 5900 as follows
```bash
# [METAL]
sudo docker run -it --rm -p 6080:80 -p 5900:5900 ct2034/vnc-ros-kinetic-full
```

If you opted to use the container with VNC, use a VNC Viwer to login to localhost:5900. You should be able to open a shell via the GUI

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
# [METAL]
sudo docker run -it --rm -p 6080:80 -p 5900:5900 [REPOSITORY[:TAG]]
```