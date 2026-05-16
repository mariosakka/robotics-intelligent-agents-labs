LAB 2 - ROS INDIGO - EASY STEP BY STEP
Use Ubuntu VM with ROS Indigo.
Keep terminals open for screenshots.
Do NOT close old terminals. Open new ones when needed.

==================================================
STEP 1 - TERMINAL 1 - LOAD ROS
==================================================

source /opt/ros/indigo/setup.bash

Optional, make it permanent:
echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc
source ~/.bashrc

Note:
This loads ROS Indigo in the terminal.

==================================================
STEP 2 - TERMINAL 1 - CREATE WORKSPACE
==================================================

mkdir -p ~/catkin_ws/src
cd ~/catkin_ws
catkin_make
source devel/setup.bash

Optional, make it permanent:
echo "source ~/catkin_ws/devel/setup.bash" >> ~/.bashrc
source ~/.bashrc

Note:
This creates the catkin workspace.

Screenshot:
Take screenshot after catkin_make succeeds.

==================================================
STEP 3 - TERMINAL 1 - CREATE PACKAGE 1
==================================================

cd ~/catkin_ws/src
catkin_create_pkg lab2_hello rospy std_msgs
cd lab2_hello
mkdir scripts

Note:
This package is for speaker + listener.

Screenshot:
Take screenshot after catkin_create_pkg command.

==================================================
STEP 4 - TERMINAL 1 - CREATE speaker.py
==================================================

nano scripts/speaker.py

Paste this:

#!/usr/bin/env python
import rospy
from std_msgs.msg import String

def talker():
    rospy.init_node('talker', anonymous=False)
    pub = rospy.Publisher('~chatter', String, queue_size=10)
    rate = rospy.Rate(10)

    while not rospy.is_shutdown():
        hello_str = "hello world %s" % rospy.get_time()
        rospy.loginfo(hello_str)
        pub.publish(hello_str)
        rate.sleep()

if __name__ == '__main__':
    talker()

Save:
Ctrl + O
Enter
Ctrl + X

Note:
The lab wants the publisher topic changed to ~chatter.

==================================================
STEP 5 - TERMINAL 1 - CREATE listener.py
==================================================

nano scripts/listener.py

Paste this:

#!/usr/bin/env python
import rospy
from std_msgs.msg import String

def callback(data):
    rospy.loginfo(rospy.get_caller_id() + " I heard %s", data.data)

def listener():
    rospy.init_node('listener', anonymous=False)
    rospy.Subscriber('/talker/chatter', String, callback)
    rospy.spin()

if __name__ == '__main__':
    listener()

Save:
Ctrl + O
Enter
Ctrl + X

Now make both executable:

chmod +x scripts/speaker.py
chmod +x scripts/listener.py

Check files:

ls -l scripts

Screenshot:
Take screenshot of ls -l scripts

==================================================
STEP 6 - TERMINAL 1 - CREATE PACKAGE 2
==================================================

cd ~/catkin_ws/src
catkin_create_pkg lab2_turtle rospy geometry_msgs turtlesim
cd lab2_turtle
mkdir scripts

Note:
This package is for turtle2 movement.

Screenshot:
Take screenshot after catkin_create_pkg command.

==================================================
STEP 7 - TERMINAL 1 - CREATE mover.py
==================================================

nano scripts/mover.py

Paste this:

#!/usr/bin/env python
import rospy
import geometry_msgs.msg
import turtlesim.srv
from turtlesim.msg import Pose

def pose_callback(msg):
    rospy.loginfo("turtle2 pose -> x=%.2f y=%.2f theta=%.2f", msg.x, msg.y, msg.theta)

if __name__ == '__main__':
    rospy.init_node('mover')

    rospy.wait_for_service('spawn')
    spawner = rospy.ServiceProxy('spawn', turtlesim.srv.Spawn)

    try:
        spawner(4, 2, 0, 'turtle2')
    except:
        pass

    turtle_vel = rospy.Publisher('/turtle2/cmd_vel', geometry_msgs.msg.Twist, queue_size=1)
    rospy.Subscriber('/turtle2/pose', Pose, pose_callback)

    rate = rospy.Rate(10.0)
    phase = 0
    phase_start = rospy.Time.now().to_sec()

    while not rospy.is_shutdown():
        now = rospy.Time.now().to_sec()
        elapsed = now - phase_start

        cmd = geometry_msgs.msg.Twist()

        if phase == 0:
            cmd.linear.x = 1.5
            cmd.angular.z = 1.0
            if elapsed > 3.0:
                phase = 1
                phase_start = now
        else:
            cmd.linear.x = 1.5
            cmd.angular.z = -1.0
            if elapsed > 3.0:
                phase = 0
                phase_start = now

        turtle_vel.publish(cmd)
        rate.sleep()

Save:
Ctrl + O
Enter
Ctrl + X

Make it executable:

chmod +x scripts/mover.py

Check file:

ls -l scripts

Screenshot:
Take screenshot of ls -l scripts

==================================================
STEP 8 - TERMINAL 1 - BUILD EVERYTHING
==================================================

cd ~/catkin_ws
catkin_make
source devel/setup.bash

Note:
This builds both packages.

Screenshot:
Take screenshot after catkin_make succeeds.

==================================================
STEP 9 - TERMINAL 2 - START ROS MASTER
==================================================

Open TERMINAL 2 and run:

source /opt/ros/indigo/setup.bash
source ~/catkin_ws/devel/setup.bash
roscore

Note:
Leave Terminal 2 open.
Do NOT close it.

Screenshot:
Take screenshot of roscore running.

==================================================
STEP 10 - TERMINAL 3 - RUN SPEAKER
==================================================

Open TERMINAL 3 and run:

source /opt/ros/indigo/setup.bash
source ~/catkin_ws/devel/setup.bash
rosrun lab2_hello speaker.py

Note:
Leave Terminal 3 open.
Do NOT close it.

You should see hello world messages.

==================================================
STEP 11 - TERMINAL 4 - RUN LISTENER
==================================================

Open TERMINAL 4 and run:

source /opt/ros/indigo/setup.bash
source ~/catkin_ws/devel/setup.bash
rosrun lab2_hello listener.py

Note:
Leave Terminal 4 open.
Do NOT close it.

You should see received messages.

Screenshot:
Take screenshot with Terminal 3 and Terminal 4 visible if possible.

==================================================
STEP 12 - TERMINAL 5 - CHECK TOPICS
==================================================

Open TERMINAL 5 and run:

source /opt/ros/indigo/setup.bash
source ~/catkin_ws/devel/setup.bash
rostopic list

You should see:
/talker/chatter

Then run:

rostopic echo /talker/chatter

Note:
Leave Terminal 5 open too.
Do NOT close it.

Screenshot:
Take screenshot showing /talker/chatter

==================================================
STEP 13 - TERMINAL 6 - OPEN TURTLESIM
==================================================

Open TERMINAL 6 and run:

source /opt/ros/indigo/setup.bash
source ~/catkin_ws/devel/setup.bash
rosrun turtlesim turtlesim_node

Note:
A turtlesim window should open.
Leave Terminal 6 open.

Screenshot:
Take screenshot of turtlesim window.

==================================================
STEP 14 - TERMINAL 7 - RUN MOVER
==================================================

Open TERMINAL 7 and run:

source /opt/ros/indigo/setup.bash
source ~/catkin_ws/devel/setup.bash
rosrun lab2_turtle mover.py

Note:
Leave Terminal 7 open.
Do NOT close it.

Expected:
- turtle2 appears
- turtle2 moves in an S-like shape
- Terminal 7 prints pose values

Screenshot:
Take screenshot of turtlesim window with turtle2.
Take screenshot of Terminal 7 pose logs.

==================================================
STEP 15 - TERMINAL 8 - CHECK TURTLE TOPICS
==================================================

Open TERMINAL 8 and run:

source /opt/ros/indigo/setup.bash
source ~/catkin_ws/devel/setup.bash
rostopic list

You should see turtle topics including:
/turtle2/pose

Then run:

rostopic echo /turtle2/pose

Note:
Leave Terminal 8 open.
Do NOT close it.

Screenshot:
Take screenshot showing /turtle2/pose output.

==================================================
STEP 16 - TERMINAL 9 - SHOW FINAL FILE STRUCTURE
==================================================

Open TERMINAL 9 and run:

source /opt/ros/indigo/setup.bash
source ~/catkin_ws/devel/setup.bash
cd ~/catkin_ws/src
find . -type f

Note:
This shows your packages and scripts.

You should have files like:
./lab2_hello/scripts/speaker.py
./lab2_hello/scripts/listener.py
./lab2_turtle/scripts/mover.py

Screenshot:
Take screenshot of final file structure.

==================================================
QUICK SUMMARY OF OPEN TERMINALS
==================================================

TERMINAL 1 = workspace creation / package creation / build
TERMINAL 2 = roscore
TERMINAL 3 = speaker
TERMINAL 4 = listener
TERMINAL 5 = /talker/chatter check
TERMINAL 6 = turtlesim_node
TERMINAL 7 = mover.py
TERMINAL 8 = /turtle2/pose check
TERMINAL 9 = final file structure

Keep them all open for screenshots.

==================================================
WHAT TO SHOW THE TEACHER
==================================================

1. Workspace created with catkin_make
2. Package lab2_hello
3. speaker.py uses ~chatter
4. listener.py listens to /talker/chatter
5. Messages are published and received
6. rostopic list shows /talker/chatter
7. Package lab2_turtle
8. mover.py spawns turtle2
9. turtle2 moves in S-shape
10. pose is printed from /turtle2/pose
11. rostopic echo /turtle2/pose works
12. final file structure exists

==================================================
IMPORTANT
==================================================

Use:
#!/usr/bin/env python

NOT:
#!/usr/bin/env python3

because ROS Indigo usually uses Python 2.

Also:
Do not use ros-noetic commands.
Do not use Windows.
Use Ubuntu VM with Indigo.