Lab 3: Interfacing with a simulated e-puck
===========================================

Robotics and Virtual Reality  
Andrei G. Florea

Contents
========

1 Rviz - ROS Vizualization  
2 V-REP simulated e-puck

Rviz - ROS Vizualization
========================

Rviz
----

After ROS is running (roscore),  
you can issue the following  
command in a new terminal:

```bash
rosrun rviz rviz
```

The main components of the user  
interface are:

1 Displays: a tree of  
  visualization options

2 Teleop window: sends Twist  
  messages on the specified  
  topic using a click-and-drag  
  motion

3 3D visualization window

RVIZ example video

Figure: E-puck in Rviz

Rviz - ROS Vizualization
========================

Rviz — displays
---------------

Rviz displays (wiki)

Features:

- Can be added/removed using the  
  corresponding buttons

- A new value is set by double cliking, entering  
  the value then pressing ENTER.

- The Fixed Frame is the reference coordinate  
  system that is used for visualization

- TF stands for Transform Frame, a library that:

  1 is visualized as a colored coordinate  
    frame

  2 shows how an object moves/rotates

  3 Has its own internal tree structure

Figure: Rviz displays used for  
the e-puck

V-REP simulated e-puck
======================

1 Rviz - ROS Vizualization  
2 V-REP simulated e-puck

V-REP simulated e-puck
======================

Usage steps
-----------

1 ensure that roscore is running

2 start V-REP and load the modified e-puck model

```bash
vrep /root/epuck_vrep_ros/e-puck.ttm
```

3 start the V-REP simulation

4 start Rviz using:

```bash
rosrun rviz rviz -d /root/epuck_vrep_ros/vp_single_epuck.rviz
```

V-REP simulated e-puck
======================

Topics
------

1 /ePuck0/camera  
2 /ePuck0/cmd_led  
3 /ePuck0/cmd_vel  
4 /ePuck0/imu  
5 /ePuck0/proximity0  
6 /ePuck0/proximity1  
7 /ePuck0/proximity2  
8 /ePuck0/proximity3  
9 /ePuck0/proximity4  
10 /ePuck0/proximity5  
11 /ePuck0/proximity6  
12 /ePuck0/proximity7

Figure: V-REP simulated e-puck topics

V-REP simulated e-puck
======================

Assignment
----------

1 Construct a scene

2 Move the epuck around that scene; Place it on top of another object

3 Study the sensor values as the robot approaches obstacles
