# LAB 3 - Ubuntu 16.04 + ROS Kinetic + e-puck + CoppeliaSim 4.1

Use Ubuntu 16.04 VM with ROS Kinetic.  
Keep old terminals open.  
Do **not** close terminals unless a step explicitly says to restart something.  
Use user **mario**.  
Use paths in the home folder.

This guide follows the same simple style as the Lab 2 template, but it is adapted to **everything we actually did**, including the troubleshooting in the correct sections.

## Important compatibility note

Original Lab 3 was made for:

- Ubuntu 14.04
- ROS Indigo
- old V-REP 3.x

This guide uses:

- Ubuntu 16.04
- ROS Kinetic
- CoppeliaSim Edu 4.1.0 for Ubuntu 16.04

That means:

- some names changed from **V-REP** to **CoppeliaSim**
- old model scripts may show compatibility warnings
- TF warnings may appear in RViz
- the setup can still be accepted if the practical parts work:
  - `roscore` runs
  - e-puck topics appear
  - the robot moves
  - proximity values change near obstacles

---

## Step 1 - Terminal 1 - Check basic prerequisites

Run:

```bash
whoami
lsb_release -a
echo $ROS_DISTRO
which roscore
which rosrun
which rviz
```

Then run:

```bash
source /opt/ros/kinetic/setup.bash
echo $ROS_DISTRO
which roscore
which rosrun
which rviz
```

### Expected

- `whoami` -> `mario`
- Ubuntu version -> `16.04.x`
- `ROS_DISTRO` -> `kinetic`
- `roscore`, `rosrun`, `rviz` should all have paths

### Why

This checks that Ubuntu 16.04 and ROS Kinetic are really present before doing anything else.

### Troubleshooting

- If `echo $ROS_DISTRO` is empty, run:

```bash
source /opt/ros/kinetic/setup.bash
```

- If `which roscore` or `which rviz` is empty, ROS Kinetic is not properly installed.
- If `lsb_release` is not 16.04, stop and adapt the guide for your actual system.

Keep **Terminal 1** open.

---

## Step 2 - Terminal 1 - Check lab files and simulator

Run:

```bash
source /opt/ros/kinetic/setup.bash
ls -lah ~
ls -lah ~/epuck_vrep_ros
find ~/epuck_vrep_ros -maxdepth 1 -type f | sort
which vrep
which coppeliaSim
which coppelia-sim
```

### What this checks

- whether the lab folder already exists
- whether `e-puck.ttm` and the RViz config exist
- whether the simulator is already installed on `PATH`

### Troubleshooting

- If `~/epuck_vrep_ros` does not exist, that is fine. We solve it in the next section.
- If all simulator commands are empty, that only means the simulator is not on `PATH` or not installed yet.
- Do **not** use `/root/...` paths. Use `/home/mario/...` paths only.

---

## Step 3 - Terminal 1 - Search for files you may have downloaded earlier

Run:

```bash
find ~ -maxdepth 3 \( -iname "epuck_vrep_ros" -o -iname "e-puck.ttm" -o -iname "vp_single_epuck.rviz" -o -iname "vrep" -o -iname "V-REP*" -o -iname "coppelia*" \) 2>/dev/null | sort
```

Then run:

```bash
find ~/Downloads -maxdepth 3 2>/dev/null | grep -Ei 'epuck|vrep|coppelia|rviz'
```

### Why

You may already have downloaded the repo or simulator somewhere else.

### Troubleshooting

- If both commands return nothing, that is okay. We download what we need manually.
- If you find an old repo in another folder, you can still use it, but this guide standardizes everything into `~/epuck_vrep_ros`.

---

## Step 4 - Terminal 1 - Download the e-puck lab repo

We used this repo:

```text
https://github.com/andrei91ro/epuck_vrep_ros
```

Run:

```bash
cd ~
git clone https://github.com/andrei91ro/epuck_vrep_ros.git
ls -lah ~/epuck_vrep_ros
find ~/epuck_vrep_ros -maxdepth 1 -type f | sort
```

### Expected files

- `e-puck.ttm`
- `epuck_vrep_ros.lua`
- `README.md`
- `vrep_single_epuck.rviz`

### Important note

The PDF shows a config name like `vp_single_epuck.rviz`, but the actual repo file we used was:

```text
vrep_single_epuck.rviz
```

### Troubleshooting

- If `git clone` fails, first check internet connection.
- If `git` is missing, install it later with `apt`.
- If the folder already exists and you want a clean copy:

```bash
rm -rf ~/epuck_vrep_ros
```

then clone again.

---

## Step 5 - Terminal 1 - Search wider for a simulator install

Run:

```bash
find ~ /opt -maxdepth 3 \( -type f -o -type l \) \( -iname "vrep" -o -iname "vrep.sh" -o -iname "coppeliaSim" -o -iname "coppeliaSim.sh" -o -iname "coppelia-sim.sh" \) 2>/dev/null | sort
```

If nothing is found, search a bit wider:

```bash
sudo find /home /opt /usr/local /srv /media /mnt -maxdepth 4 \( -type f -o -type l \) \( -iname "vrep" -o -iname "vrep.sh" -o -iname "coppeliaSim" -o -iname "coppeliaSim.sh" -o -iname "coppelia-sim.sh" \) 2>/dev/null | sort
```

### Why

This checks whether the simulator is already on disk but just not on `PATH`.

### Troubleshooting

- If nothing is found, move on to the install section.
- If you find both old V-REP and new CoppeliaSim, prefer the one that matches the rest of your setup.
- We found nothing, so we installed CoppeliaSim manually.

---

## Step 6 - Terminal 1 - Install supporting packages

Run:

```bash
sudo apt update
sudo apt install -y git cmake python-tempita python-catkin-tools python-lxml default-jre xsltproc
```

Then verify:

```bash
which git
which cmake
which catkin
which xsltproc
```

### Why

These helper tools are useful for ROS-related compatibility and old simulator interface workflows.

### Troubleshooting

- If `sudo apt update` fails with a ROS key error such as:

```text
NO_PUBKEY F42ED6FBAB17C654
```

do **not** continue yet. Fix the key in the next step.

- If `apt install` says `python-catkin-tools` is unauthenticated, that usually also comes from the missing ROS key.
- `xsltproc` is important on Ubuntu 16.04 for older ROS/simulator build instructions.

---

## Step 7 - Terminal 1 - Fix the ROS apt key if apt update fails

If `apt update` showed:

```text
NO_PUBKEY F42ED6FBAB17C654
```

Run:

```bash
sudo apt install -y curl
curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key | sudo apt-key add -
sudo apt update
```

### Expected

- `apt-key add` should print:

```text
OK
```

- `apt update` should finish without the old ROS key error

Then retry the package install from Step 6:

```bash
sudo apt install -y git cmake python-tempita python-catkin-tools python-lxml default-jre xsltproc
```

### Troubleshooting

- If `apt-key add` does not print `OK`, check internet connectivity.
- If the key still does not work, re-run `sudo apt update` and carefully read whether the key error changed.
- If packages still fail, install them one by one to isolate the problem.

---

## Step 8 - Download the simulator

### What we wanted originally

- V-REP PRO EDU 3.4.0 Linux

### What happened

- the old official V-REP 3.4.0 direct links were no longer available
- SourceForge and archive checks were also unsuccessful
- we switched to the fallback:
  **CoppeliaSim Edu 4.1.0 for Ubuntu 16.04**

### Important warning

This fallback is newer than the original lab target, so compatibility warnings can appear.  
That is acceptable if the actual robot behavior works.

Use the official previous-versions page and download:

```text
CoppeliaSim Edu V4.1.0 Ubuntu 16.04
```

File name used:

```text
CoppeliaSim_Edu_V4_1_0_Ubuntu16_04.tar.xz
```

After downloading, verify:

```bash
ls -lah ~/Downloads | grep 'CoppeliaSim_Edu_V4_1_0_Ubuntu16_04.tar.xz'
```

### Troubleshooting

- Get the **Edu** version, not **Pro**, unless you specifically need a Pro license.
- Do **not** try to convert a Windows simulator package into a Linux one.
- A Windows package can provide models or scripts, but not a native Linux simulator executable.

---

## Step 9 - Terminal 1 - Extract CoppeliaSim

Run:

```bash
cd ~
tar -xf ~/Downloads/CoppeliaSim_Edu_V4_1_0_Ubuntu16_04.tar.xz
ls -lah ~ | grep CoppeliaSim
find ~/CoppeliaSim* -maxdepth 1 \( -type f -o -type l \) | sort
```

### Expected

- folder:

```text
~/CoppeliaSim_Edu_V4_1_0_Ubuntu16_04
```

- launcher:

```text
~/CoppeliaSim_Edu_V4_1_0_Ubuntu16_04/coppeliaSim.sh
```

### Very good sign

This folder also contained:

```text
libsimExtROSInterface.so
```

That means the ROS interface plugin is present in the simulator build.

### Troubleshooting

- If `tar` says permission denied on the download, run:

```bash
chmod +r ~/Downloads/CoppeliaSim_Edu_V4_1_0_Ubuntu16_04.tar.xz
```

then extract again.

- If no `coppeliaSim.sh` is present, you probably downloaded the wrong package.

---

## Step 10 - Terminal 1 - Verify CoppeliaSim starts

Run:

```bash
cd ~/CoppeliaSim_Edu_V4_1_0_Ubuntu16_04
./coppeliaSim.sh
```

### Expected

- the CoppeliaSim window opens
- terminal stays occupied while it runs

### Troubleshooting

- If it crashes immediately, read the terminal output for missing library messages.
- If the window opens, this step is done.
- Close it again only when the guide tells you to.

---

## Step 11 - Terminal 2 - Start ROS master

Open **Terminal 2** and run:

```bash
source /opt/ros/kinetic/setup.bash
roscore
```

### Expected

- `roscore` starts
- terminal remains busy
- you should leave it open

### Why

Lab 3 requires `roscore` first, then simulator, then RViz.

### Troubleshooting

- If `roscore` says command not found, source ROS Kinetic again.
- If `roscore` cannot start because another ROS master is already running, either use the existing one or stop it first.

Keep **Terminal 2** open.  
Do **not** close it.

---

## Step 12 - Terminal 1 - Start CoppeliaSim again

Run:

```bash
cd ~/CoppeliaSim_Edu_V4_1_0_Ubuntu16_04
./coppeliaSim.sh
```

### Expected

- CoppeliaSim opens again

Keep **Terminal 1** open.  
Do **not** close it while the simulator is running.

---

## Step 13 - CoppeliaSim window - Load the e-puck model

In the CoppeliaSim window:

**File -> Open model...**

Then select:

```text
/home/mario/epuck_vrep_ros/e-puck.ttm
```

### Expected

- e-puck model appears in the scene

### Warning we saw

```text
compatibility fix in script ePuck@childScript ...
```

This is normal here because:

- the model was originally written with old CoppeliaSim/V-REP 3.04
- we are running it in CoppeliaSim 4.1

### Troubleshooting

- If the model does not load, confirm the file exists:

```bash
ls -lah ~/epuck_vrep_ros/e-puck.ttm
```

- If you get a compatibility warning only, you can continue.
- If you get a hard script error and the model does not appear, stop and inspect the console.

---

## Step 14 - CoppeliaSim window - Start the simulation

Click the **Play / Start Simulation** button.

### Expected

- simulation starts
- robot may stand still, which is normal until commands are sent

### Important confusion we had

At one point the console showed:

- `Simulation started`
- `simulation stopping...`
- `Simulation stopped.`

That turned out to be because the simulation had been manually stopped, not because it crashed.

### Troubleshooting

- If it stops immediately, carefully check whether you clicked stop yourself.
- If it truly stops on its own, watch the console for red errors or plugin/script failures.
- If it keeps running, continue.

---

## Step 15 - Terminal 3 - Check whether the ROS topics exist

Open **Terminal 3** and run:

```bash
source /opt/ros/kinetic/setup.bash
rostopic list | grep ePuck
```

### Expected topics

- `/ePuck0/camera`
- `/ePuck0/cmd_led`
- `/ePuck0/cmd_vel`
- `/ePuck0/imu`
- `/ePuck0/proximity0`
- `/ePuck0/proximity1`
- `/ePuck0/proximity2`
- `/ePuck0/proximity3`
- `/ePuck0/proximity4`
- `/ePuck0/proximity5`
- `/ePuck0/proximity6`
- `/ePuck0/proximity7`

### Important troubleshooting

- If `rostopic list` returns nothing for `ePuck`, first make sure the simulation is actually running.
- We once checked while the simulation was **not** running and got no topics.
- When the simulation was running, the topics appeared correctly.
- If the simulation is running and topics still do not appear, then inspect whether the ROS interface plugin loaded.

Keep **Terminal 3** open.

---

## Step 16 - Optional plugin check if no topics appear

Only do this if topics still do not show up while the simulation is running.

In **Terminal 1**, relaunch CoppeliaSim with a saved log:

```bash
cd ~/CoppeliaSim_Edu_V4_1_0_Ubuntu16_04
./coppeliaSim.sh 2>&1 | tee ~/coppelia_ros_log.txt
```

Then after launching and running the model, inspect the log:

```bash
grep -Ei 'ROSInterface|simExtROSInterface|plugin|error|warning' ~/coppelia_ros_log.txt
```

### Why

This helps verify whether the simulator ROS plugin actually loaded.

### Troubleshooting

- We did **not** need this in the end because the real issue was only that the simulation was not running.
- Use this section only if you truly have no topics with a running simulation.

---

## Step 17 - Terminal 4 - Open RViz with the lab config

Open **Terminal 4** and run:

```bash
source /opt/ros/kinetic/setup.bash
rosrun rviz rviz -d ~/epuck_vrep_ros/vrep_single_epuck.rviz
```

### Expected

- RViz opens
- robot-related displays, TF, sensor cones, and teleop panel appear

### Troubleshooting

- If RViz says the config file is missing, confirm:

```bash
ls -lah ~/epuck_vrep_ros/vrep_single_epuck.rviz
```

- If you accidentally use `vp_single_epuck.rviz` from the PDF and it does not exist, switch to:

```text
vrep_single_epuck.rviz
```

- If RViz opens but some displays are red, continue to the next troubleshooting section.

Keep **Terminal 4** open.

---

## Step 18 - RViz - Handle TF_OLD_DATA warnings

### What we saw

- `TF_OLD_DATA` warnings for frames like:
  - `ePuck0/left_wheel`
  - `ePuck0/right_wheel`
  - `ePuck0/body_top`

### What it means

RViz/TF is receiving old timestamps compared with what it expects.

### Why it likely happens here

- old e-puck model
- newer simulator version
- stale TF state from earlier runs

### What we tried

- clicking **Reset** in RViz
- fully restarting `roscore`, CoppeliaSim, and RViz in the correct order

### Result

- the warnings could still appear

### Important decision

We ignored the warnings because the actual lab behavior still worked:

- the robot moved
- the topics existed
- the sensor values changed near obstacles

### When you can ignore it

- if the robot is controllable
- if the data updates
- if RViz is still usable

### When you should not ignore it

- if the robot does not move
- if all displays are frozen
- if sensor values never change
- if TF itself is unusable for the lab

---

## Step 19 - RViz - Test Teleop control

In RViz, use the **Teleop** square at the bottom-left.  
Drag slightly upward.

### Expected

- the robot moves in CoppeliaSim

This proves:

- `/ePuck0/cmd_vel` is working
- the robot can be controlled from RViz

### Troubleshooting

If the robot does not move, check:

1. Is `roscore` running?
2. Is the simulation running?
3. Do ePuck topics exist?
4. Does `/ePuck0/cmd_vel` exist?

If the robot moves perfectly fine, you can continue even if TF warnings remain.

---

## Step 20 - Terminal 3 - Check proximity sensor output

Run:

```bash
source /opt/ros/kinetic/setup.bash
rostopic echo /ePuck0/proximity0
```

Then move the robot toward a wall or obstacle.

### What we first observed

- the value stayed at about `0.05`
- only the timestamp changed

### Interpretation

- `0.05` was the max range
- that means no object was detected within the useful range of that sensor

### Troubleshooting

- If one sensor seems unchanged, that does **not** mean all sensors are broken.
- The robot may be facing a direction where that specific sensor does not see the obstacle.
- Test a different sensor and change the robot placement.

---

## Step 21 - Terminal 3 + CoppeliaSim - Test a better sensor

Stop the old echo with `Ctrl+C`.

Then run:

```bash
source /opt/ros/kinetic/setup.bash
rostopic echo /ePuck0/proximity2
```

In CoppeliaSim:

- stop or pause the simulation
- place the robot with the front-side sensor close to the wall
- restart the simulation

### What we observed

- `/ePuck0/proximity2` changed from about `0.05` to about `0.011`

### Interpretation

- the sensor was detecting the nearby wall correctly

This proved the assignment requirement:
study the sensor values as the robot approaches obstacles.

### Troubleshooting

- If `proximity0` does not change, try `proximity2` or another front/side sensor.
- Make sure the robot is actually very close to the wall.
- Make sure the simulation is running while you echo the topic.

---

## Step 22 - RViz - Understand which cone belongs to which sensor

In RViz, each cone belongs to a sensor frame/topic pair.

### Mapping rule

- `ePuck0/base_prox0`  <->  `/ePuck0/proximity0`
- `ePuck0/base_prox1`  <->  `/ePuck0/proximity1`
- ...
- `ePuck0/base_prox7`  <->  `/ePuck0/proximity7`

### To make labels easier to read

- expand **TF** in Displays
- enable **Show Names**
- enable **Show Axes**
- increase **Marker Scale** to about `0.15` or `0.2`
- zoom in with **Move Camera**

### Troubleshooting

- If `Prox0..Prox7` are red in RViz, that does **not** automatically mean the sensors are broken.
- In our setup, even with red displays and TF warnings, the robot still moved and the sensor topic values still changed correctly.

---

## Step 23 - Save a working state checkpoint

Run:

```bash
printf '%s
' 'Lab 3 working state:' '- Terminal 1: CoppeliaSim running with e-puck.ttm loaded' '- Terminal 2: roscore running' '- Terminal 3: ROS topic checks / rostopic echo' '- Terminal 4: RViz with vrep_single_epuck.rviz' '- Robot moves from RViz teleop' '- /ePuck0/proximity2 changes near wall' > ~/lab3_working_state.txt

cat ~/lab3_working_state.txt
```

### Why

This gives you a simple checkpoint file proving you reached a working state.

---

## Quick summary of open terminals

- **Terminal 1** = CoppeliaSim launcher
- **Terminal 2** = `roscore`
- **Terminal 3** = `rostopic` checks / sensor echo
- **Terminal 4** = RViz

Keep them open while demonstrating the lab.

---

## What to show the teacher

1. Ubuntu 16.04 + ROS Kinetic environment works
2. `epuck_vrep_ros` repo exists in `~/epuck_vrep_ros`
3. `e-puck.ttm` loads in CoppeliaSim
4. simulation runs
5. `rostopic list` shows the ePuck topics
6. RViz opens with `vrep_single_epuck.rviz`
7. robot moves from RViz Teleop
8. proximity topic values change near a wall
9. you understand that one unchanged sensor value at `0.05` can simply mean no detection
10. you can identify or test individual sensors such as `/ePuck0/proximity2`

---

## Important

- Use **ROS Kinetic** commands, not Indigo or Noetic ones
- Use **Ubuntu 16.04**
- Use `/home/mario/...` paths, not `/root/...`
- The simulator fallback used here is **CoppeliaSim 4.1.0 Edu for Ubuntu 16.04**
- Some warnings are acceptable as long as the actual lab functions work
