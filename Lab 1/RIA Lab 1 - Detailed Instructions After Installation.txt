ROS Turtlesim Lab — Step-by-Step Commands After Installation
===========================================================

Important:
Keep track of terminal windows. Some commands must stay running.

WINDOW 1: Start ROS master
--------------------------
Purpose:
ROS needs the master running before turtlesim and other nodes can communicate.

Open terminal 1 and run:
roscore

What to do next:
Leave this terminal open. Do not close it.

WINDOW 2: Start turtlesim
-------------------------
Purpose:
This opens the TurtleSim simulation window.

Open a new terminal:
Ctrl + Alt + T

Run:
rosrun turtlesim turtlesim_node

What to do next:
A blue TurtleSim window should appear.
Leave this terminal open.

WINDOW 3: Start keyboard control
--------------------------------
Purpose:
This lets you move the turtle using arrow keys.

Open another new terminal:
Ctrl + Alt + T

Run:
rosrun turtlesim turtle_teleop_key

How to use:
Click inside this terminal and use the arrow keys.

When to stop it:
If you want to control the turtle with rostopic or rqt_publisher, stop keyboard teleop first.
Press:
Ctrl + C


SECTION A: Basic inspection commands
====================================

WINDOW 4: Inspect nodes, topics, messages, and services
-------------------------------------------------------
Open another new terminal:
Ctrl + Alt + T

1) List all running nodes
Purpose:
See the active ROS programs.
Command:
rosnode list

2) Get info about turtlesim node
Purpose:
See what turtlesim publishes, subscribes to, and serves.
Command:
rosnode info /turtlesim

3) List all topics
Purpose:
See communication channels in ROS.
Command:
rostopic list

4) Inspect command velocity topic
Purpose:
See who publishes and who subscribes to turtle movement commands.
Command:
rostopic info /turtle1/cmd_vel

5) Watch live turtle pose
Purpose:
See x, y, theta, linear_velocity, and angular_velocity change live.
Command:
rostopic echo /turtle1/pose

Note:
Press Ctrl + C to stop the live output when finished.

6) Show the Twist message format
Purpose:
Understand the movement message fields.
Command:
rosmsg show geometry_msgs/Twist

7) List services
Purpose:
See available service calls such as reset, spawn, and set_pen.
Command:
rosservice list

8) Inspect teleport service
Purpose:
See how the teleport service works.
Command:
rosservice info /turtle1/teleport_absolute

9) Show teleport service structure
Purpose:
See the input fields x, y, theta.
Command:
rossrv show turtlesim/TeleportAbsolute


SECTION B: GUI tools
====================

Each of these should be opened in a new terminal window if you want to use them.

WINDOW 5: Graph view
--------------------
Purpose:
Show which nodes talk to each other.

Open a new terminal and run:
rosrun rqt_graph rqt_graph

WINDOW 6: Topic monitor
-----------------------
Purpose:
View topic rates and values.

Open a new terminal and run:
rosrun rqt_topic rqt_topic

WINDOW 7: Publisher GUI
-----------------------
Purpose:
Publish values manually to a topic.

Open a new terminal and run:
rosrun rqt_publisher rqt_publisher

WINDOW 8: Service caller GUI
----------------------------
Purpose:
Call ROS services using a GUI.

Open a new terminal and run:
rosrun rqt_service_caller rqt_service_caller

WINDOW 9: Plot x and y
----------------------
Purpose:
Plot turtle position as graphs.

Open a new terminal and run:
rosrun rqt_plot rqt_plot /turtle1/pose/x /turtle1/pose/y


SECTION C: Reset before assignment
==================================

WINDOW 4 or any free terminal
-----------------------------
Purpose:
Reset the turtle to the center and clear the drawing.

Command:
rosservice call /reset


SECTION D: Assignment tasks
===========================

Task 1: Move turtle1 in a square
--------------------------------
Best method:
Use terminal commands instead of rqt_publisher if GUI publishing is unreliable.

Before starting:
Stop keyboard teleop if it is still running.
Go to the teleop terminal and press:
Ctrl + C

Use any free terminal.

Move forward:
rostopic pub -r 10 /turtle1/cmd_vel geometry_msgs/Twist '{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}'

What to do:
Let it run for about 2 seconds, then press Ctrl + C

Turn:
rostopic pub -r 10 /turtle1/cmd_vel geometry_msgs/Twist '{linear: {x: 0.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.57}}'

What to do:
Let it run for about 1 second, then press Ctrl + C

Repeat pattern:
1. Run Forward
2. Stop with Ctrl + C
3. Run Turn
4. Stop with Ctrl + C

Repeat 4 times to make a square.

Task 2: Move turtle1 in a circle
--------------------------------
Use any free terminal.

Command:
rostopic pub -r 10 /turtle1/cmd_vel geometry_msgs/Twist '{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.0}}'

What to do:
Let it move in a circle.
Press Ctrl + C when done.

Task 3: Change pen color
------------------------
Use any free terminal.

Red pen:
rosservice call /turtle1/set_pen 255 0 0 3 0

Green pen:
rosservice call /turtle1/set_pen 0 255 0 5 0

What to do:
After calling set_pen, move the turtle again so the new color appears.

Task 4: Spawn a second turtle
-----------------------------
Use any free terminal.

Command:
rosservice call /spawn 2.0 2.0 0.0 turtle2

What to expect:
A second turtle named turtle2 should appear.

Task 5: Control turtle2
-----------------------
Use any free terminal.

Command:
rostopic pub -r 10 /turtle2/cmd_vel geometry_msgs/Twist '{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.0}}'

What to do:
Let it move for a few seconds.
Press Ctrl + C when done.

Task 6: Plot linear and angular velocity
----------------------------------------
Open a new terminal for plotting.

For turtle1:
rosrun rqt_plot rqt_plot /turtle1/pose/linear_velocity /turtle1/pose/angular_velocity

For turtle2:
rosrun rqt_plot rqt_plot /turtle2/pose/linear_velocity /turtle2/pose/angular_velocity


SECTION E: Useful checks
========================

Use any free terminal.

Check who publishes and subscribes to turtle1 cmd_vel:
rostopic info /turtle1/cmd_vel

Check topics after spawning turtle2:
rostopic list

Check services:
rosservice list

If a live command keeps printing and you want to stop it:
Ctrl + C

If ROS commands do not work in a new terminal:
source /opt/ros/indigo/setup.bash
