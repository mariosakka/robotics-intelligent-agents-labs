DOWNLOAD VMWARE WORKSTATION PRO AND THEN UBUNTU 14 ON IT

ROS Indigo on Ubuntu 14.04.6 — Installation Guide with Commands
==============================================================

Use this file from a fresh Ubuntu terminal.

WINDOW 1: Open your first terminal
----------------------------------
You can open a terminal from the left sidebar icon or press:
Ctrl + Alt + T

Step 1: Check internet
----------------------
Purpose:
Make sure the VM has internet before trying to install ROS.

Command:
ping -c 4 google.com

What to expect:
You should see 4 replies. If not, fix internet first.

Step 2: Add the ROS repository
------------------------------
Purpose:
This tells Ubuntu where to download ROS Indigo from.

Command:
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu trusty main" > /etc/apt/sources.list.d/ros-latest.list'

Step 3: Add the repository key
------------------------------
Purpose:
This lets Ubuntu trust packages from the ROS repository.

Command:
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116

Step 4: Update package list
---------------------------
Purpose:
Refresh Ubuntu so it can see the ROS packages.

Command:
sudo apt-get update

Step 5: Install ROS Indigo desktop-full
---------------------------------------
Purpose:
Install the full ROS desktop version used in the lab.

Command:
sudo apt-get install ros-indigo-desktop-full

What to expect:
It may take a while.
If asked:
Do you want to continue? [Y/n]
type:
Y

Step 6: Initialize rosdep
-------------------------
Purpose:
rosdep helps ROS manage package dependencies.

Commands:
sudo rosdep init
rosdep update

Step 7: Add ROS setup to every terminal automatically
-----------------------------------------------------
Purpose:
This makes ROS commands work every time you open a terminal.

Commands:
echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc
source ~/.bashrc

Step 8: Install rosinstall
--------------------------
Purpose:
This installs a ROS utility often used in setup.

Command:
sudo apt-get install python-rosinstall

Step 9: Test ROS
----------------
Purpose:
Check that ROS is installed correctly.

Command:
roscore

What to expect:
You should see ROS startup messages and the terminal will stay busy.
Leave this terminal open while ROS is running.

Useful fix if ROS commands are not recognized later
---------------------------------------------------
Run this in any terminal where ROS commands do not work:
source /opt/ros/indigo/setup.bash
