---
status: in-progress
---

#Guide 
**For [[Isaac SIM MOC|Isaac Sim]] 5.0 in Ubuntu 24.04**

Official Installation Guide: [WorkStation Installation](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_workstation.html)

# Compatibility Checker
First its important to check if the system is compatible with Isaac Sim. The main restriction is to a have a RTX graphics card with at least 32GB of Ram, and 16GB VRam (It could work with less VRam but performance can be limited) 
For full Requirements [Check Here](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/requirements.html#isaac-sim-requirements)

# Download
1. Download [Latest Version](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/download.html#isaac-sim-latest-release), 
2. Create a folder called *isaacsim*
3. Unzip the download in the created folder
4. To create a symlink to the extension_examples run `./post_install.sh` 
5. To run isaac sim selector`./isaac-sim.selector.sh` 
		![[image.png]]
		Is possible that on first launch this appears:
			![[image(1).png]]
			To fix it, you need to disable virtualization in the BIOS
6. On the menu select Isaac-sim, and leave the rest as-is, like the above image. When launching Isaac sim would freeze, specially if launching for the first time. Let it do its thing.
	![[image(2).png]]
	On the right, the stage panel, you will find some default prims, World and defaultLight
	![[image(3).png]]
	On the top left is the create window. You can also access it by right clicking on the main visualizer and the stage panel
	On the top there is also a list of examples of some tasks you can perform with isaac sim
	To move thorugh the viewport, hold right click, and by moving the mouse youll move where the camera is looking, and you can move with wasd
	The wheel is for the zoom and using the middle button you can pan

# Next Steps
- [[Building a Simple Robot in Isaac Sim]]
- 