IA / RIA LAB 5 - NETLOGO SETUP GUIDE

The PDF is titled Lab 2-IA: The First Model, but this is your current Lab 5. It uses NetLogo, not ROS.

Required:
- NetLogo
- RIA Lab 5.pdf
- lab2.nlogo.txt starter file

Not needed:
- ROS, RViz, turtlesim, CoppeliaSim
- Ubuntu 14.04 or 16.04 specifically

Install NetLogo:
Windows: download NetLogo, install normally, open it.
macOS: download NetLogo, open .dmg, drag to Applications. If blocked, use Privacy & Security -> Open Anyway.
Ubuntu:
cd ~
mkdir -p tools
cd tools
tar -xf ~/Downloads/NetLogo-*.tgz
cd ~/tools/NetLogo*/
./NetLogo

Prepare starter file:
1. Make a copy of lab2.nlogo.txt.
2. Rename it to lab2.nlogo.
3. Open NetLogo.
4. File -> Open -> lab2.nlogo.

The starter already has:
- turtles-own [energy]
- patches-own [regeneration-timer]
- setup and go
- setup-turtles and setup-patches
- skeleton move, feed, reproduce, death
- partially implemented grow-food

Recommended workflow:
1. Add sliders and monitors.
2. Complete Part I first.
3. Save as lab5_part1_basic_behaviour.nlogo.
4. Complete Part II.
5. Save as lab5_final.nlogo.
