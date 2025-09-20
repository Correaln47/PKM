---
status: in-progress
---
#Guide 
# Creating a simple cube
1. Add the cube
	- Using the create menu (Top left or right clicking in the viewport or right menu) Create a cube. This is just a [[mesh]] we see, not really an *object* to the simulation.
2. Initialize the Physics Scene
	- When you first press **Play**, nothing happens because no physics scene is active.
	- To enable physics, go to the **Create** menu. Navigate to the **Physics** options and select **Physics Scene**. This creates an entity that defines global parameters like gravity.
3. Add Physics Properties to the Cube
	- After adding the Physics Scene, the cube still won't react. This is because it doesn't have its own physics properties.
	 ==To make a mesh a physics-enabled entity, you must add a collision mesh.==
	- Select the cube in the stage tree and add the necessary physics property with the add menu (Below the create menu when right clicking). This allows it to interact with other physical objects in the scene. **Add > Physics > Rigid Body with colliders**
4. Observe Collision and Visualization
	- With both the **Physics Scene** and the object's **Physics Property** in place, press **Play**. The object will now react to gravity and other forces.
	- To see how the object collides with a surface, add a ground plane with the create menu. You'll observe the cube fall and collide with the ground.
	- To visualize the collision meshes, click the **eye icon** in the top-left of the view port. In the drop down menu, go to **Show by Type** > **Physics** > **Colliders** and select it to make the collision meshes visible.
5. Further tests
	- You can change the orientation of the cube to see how it reacts
	- You can try changing the gravity direction and magnitude to see the effects.

<iframe width="720" height="480" src="https://drive.google.com/file/d/1R9rijwzIm8Y6KyLx72-fupZT2zjCV8bd/preview" frameborder="0" allowfullscreen></iframe> 
<p style="color: grey; font-size: 16px; font-style: italic;">Creating a simple cube - reference video.</p>

# Adding Wheels

## Preparing the Chassis

1.  Position the Chassis
    Ensure the cube is at coordinates `(0, 0, 0)`. This simplifies the process of creating and aligning other robot parts.

2.  Scale the Chassis
    Scale the cube into a rectangular shape to accommodate the wheels.
    ![[image(5).png]]

3.  Create a Hierarchy
    Create a new **Xform** prim and drag your cube into it. This Xform will serve as the root container for the robot's hierarchical structure.
    > **Note:** The Xform acts as a container. If you select and move the Xform, all child prims (like the cube) will move along with it, as transformations are applied to everything inside.
    ![[image(4).png]]

---

## Creating and Attaching Wheels

1.  Create the Wheels
    -   Create a **Cylinder** prim and ensure it is inside the root Xform you created earlier.
    -   **Rotate**, **scale**, and **move** the cylinder to the desired position for the first wheel.
    -   Repeat this process to create the other three wheels.
    -   Don't forget to add **physics properties** to the chassis and all four wheels.

2.  **Initial Physics Test**
    -   You can verify the hierarchy by moving the root **Xform**; all parts should move together.
    -   When you press **Play**, all parts should fall due to gravity. You will notice they are not yet connected.

<iframe width="720" height="480" src="https://drive.google.com/file/d/17vkLRsd15BTGWIysslKVMHEkMhdyFGZg/preview" frameborder="0" allowfullscreen></iframe>
<p style="color: grey; font-size: 16px; font-style: italic;">Creating and positioning the wheels - reference video.</p>

---

## Connecting with Revolute Joints

1.  **Create Revolute Joints**
    To fix the disconnection, we need to define the physical connections using joints. For wheels, we use revolute joints.
    -   First, select the main body (the chassis cube).
    -   Hold `Ctrl` and select a wheel prim.
    -   From the top menu, go to **Create > Physics > Joint > Revolute Joint**.
    -   Repeat for the other three wheels.

2.  **Configure Joints**
    -   Select a newly created **Revolute Joint**.
    -   In its **Properties** panel, you can configure its settings, such as defining the correct **axis** of rotation and its **location**.

<iframe width="720" height="480" src="https://drive.google.com/file/d/1OJKXoo9JIbbJf4w8xzS-PVH9UdU_8shN/preview" frameborder="0" allowfullscreen></iframe>
<p style="color: grey; font-size: 16px; font-style: italic;">Adding and configuring revolute joints - reference video.</p>


# Making the Robot Move

## Adding Angular Drive

To make the robot move, the wheel joints need a driving force. We will apply this to the two rear wheels.

1.  Add Angular Drive Property
    -   Right-click on one of the rear wheel's **Revolute Joint** prims.
    -   Navigate to **Create > Physics > Angular Drive**. A new section will appear in the **Properties** panel on the right.

2.  Configure the Drive
    -   In the **Angular Drive** properties, input a **Target Velocity**.
    -   Set a high **Damping** value (e.g., `10000`).
    > **Why high damping?**
    > Damping represents the joint's resistance to changes in angular velocity. A high value helps the wheel converge to the target velocity faster and with greater stability.

3.  Repeat for the other rear wheel's joint.

4.  Test the Robot
    -   Press **Play**. The robot should now move forward as the wheels spin at their target velocities.

> ⚠️ **Callout: Potential Bouncing**
> Depending on the speed, the robot may bounce. This often occurs when physics properties (like materials and friction) are not yet defined for the wheels or the ground plane.

<iframe width="720" height="480" src="https://drive.google.com/file/d/1VdLNlsmrQjA8Y8c57EuTSCxnm0iVITl8/preview" frameborder="0" allowfullscreen></iframe>
<p style="color: grey; font-size: 16px; font-style: italic;">Applying angular drive to the wheels - reference video.</p>

---

## Implementing a Differential Controller

A differential drive system provides more sophisticated control over the robot's movement and direction.

1.  Add Articulation Root
    -   Select the robot's root **Xform** and add an **Articulation Root** physics property to it.

2.  Create the Controller
    -   From the top menu, go to **Tools > Robotics > Omnigraph Controllers > Differential Controller**.
    -   In the pop-up window, configure the following:
        -   **Robot Prism**: Select the robot's root **Xform**.
        -   **Wheel Radius**: `0.375`
        -   **Distance Between Wheels**: `1.5`
        -   **Joint Names**: Input the names of the two rear wheel **Revolute Joints**.
    > **Note**: For the controller to work, the specified joints must already have the **Angular Drive** property applied.

### Understanding the Controller Graph

On the right panel, a new **Differential Controller** omnigraph is created. You can right-click it and select **Open Graph** to see its structure.
-   The **On Playback Tick** node sends a signal at each step of the simulation.
-   The **Differential Controller** node takes desired linear and angular velocities and calculates the required wheel speeds.
-   The **Make Array** node contains the names of the target joints.
-   The **Articulation Controller** node receives the tick signal and the calculated velocities, then sends the commands to the actual joints.

You can test this by playing the simulation and manually changing the **linear velocity** and **angular velocity** inputs on the **Differential Controller** node.

<iframe width="720" height="480" src="https://drive.google.com/file/d/1FGU8f4Dng0fqshZsFSVw1LTtVfCwFw5f/preview" frameborder="0" allowfullscreen></iframe>
<p style="color: grey; font-size: 16px; font-style: italic;">Setting up a differential controller - reference video.</p>

### Adding Keyboard Control

For direct control, you can check the **"Keyboard"** option when creating the Differential Controller. This adds nodes to the graph that receive user input from the **WASD keys** and convert them into the linear and angular velocity commands that were previously entered manually.

<iframe width="720" height="480" src="https://drive.google.com/file/d/15YDAS5S0CraIKPb-tensoZGHQOr6VKSu/preview" frameborder="0" allowfullscreen></iframe>
<p style="color: grey; font-size: 16px; font-style: italic;">Adding keyboard control to the graph - reference video.</p>

---
# Adding Sensors

## General Sensor Setup

It's good practice to create a dedicated container for all sensors.

1.  Create a new **Xform** on the robot to nest all sensor components.
2.  Add a **Rigid Body** physics property to this sensor Xform.

---

## Adding a Camera 📸

1.  Create the Camera Body
    -   Create a **Cube** prim, scale it to your desired camera size, and move it to the correct position on the robot.
    -   Depending on your use case, you may or may not want to add physics properties to this mesh.

2.  Attach the Camera Body
    -   Create a [[Fixed Joint]] to rigidly attach the camera to the main chassis.
    -   First, select the main chassis cube.
    -   Hold `Ctrl` and select the camera cube.
    -   Right-click and go to **Create > Physics > Joint > Fixed Joint**.

3.  Create the Camera Sensor
    -   Right-click the camera cube prim and select **Create > Camera**.

4.  Position the Sensor
    -   Adjust the new camera prim's position. Use the four perspective lines as a reference for its field of view.
    -   **Important**: Ensure the intersection point of the four lines is **outside** the camera mesh; otherwise, the view will be obstructed.

5.  Set up a Viewport
    -   In the top-left menu, go to **Window** and add a second viewport.
    -   In the new viewport, click the camera icon at the top and select your newly created camera from the list. You can now see what your robot's camera sees.

6.  Test the Camera
    -   With the keyboard controller active, press **Play** and move the robot. The second viewport will show the camera feed staying attached to the robot.

> ⚠️ **Callout: Viewport Control**
> Be careful not to use standard navigation controls (like orbiting or panning) while the second viewport is selected, as this will change the position and properties of the robot's camera sensor itself.

<iframe width="720" height="480" src="https://drive.google.com/file/d/1TNYySPHHKz1952PBMrQxW_lzQWKo7Tob/preview" frameborder="0" allowfullscreen></iframe>
<p style="color: grey; font-size: 16px; font-style: italic;">Adding and configuring a camera - reference video.</p>

---

## Adding a Lidar

1.  Create the Lidar Sensor
    -   Right-click the main sensor **Xform**.
    -   Navigate to **Create > Isaac > Sensors > PhysX Lidar > [[Rotating Lidar]]**.

2.  Position the Lidar
    -   Adjust its height and position as desired.

3.  Visualize the Output
    -   To see what the lidar "sees," select the lidar prim.
    -   Go to its **Raw USD Properties** panel and enable the **Draw Lines** option.

> **Note**: The lidar relies on the physics scene. It will only detect objects that have a collider component.

<iframe width="720" height="480" src="https://drive.google.com/file/d/1zXCebmi9eOPFhqpmxSm0aM2qPOutQKpQ/preview" frameborder="0" allowfullscreen></iframe>
<p style="color: grey; font-size: 16px; font-style: italic;">Adding and visualizing a Lidar sensor - reference video.</p>

# Connecting Sensor Data to ROS 2

How do we publish Lidar data from Isaac Sim to a ROS 2 topic and visualize it in a program like RViz?

> **Note**: Ensure the simulation is paused before you begin these steps.
## Setting up the Action Graph

First, you need an Action Graph to process and publish the sensor data.

1.  Create an Action Graph
    -   In the **Stage** or graph tree panel on the right, right-click in an empty area.
    -   Navigate to **Create > Visual Scripting > Action Graph**.

2.  Add the Required Nodes
    -   Open the new Action Graph by double-clicking it.
    -   Add the following nodes to the graph:
        -   `On Playback Tick`
        -   `Isaac Read Lidar Beams`
        -   `ROS 2 Publish Laser Scan`
        -   `Isaac Read Simulation Time`

3.  Connect the Nodes
    -   Connect the `execOut` pin of the **On Playback Tick** node to the `execIn` pin of the **Isaac Read Lidar Beams** node.
    -   Connect the `execOut` of **Isaac Read Lidar Beams** to the `execIn` of **ROS 2 Publish Laser Scan**.
    -   Connect the data output pins from the Lidar node (like `azimuthRange`, `intensitiesData`, etc.) to the corresponding input pins on the **ROS 2 Publish Laser Scan** node.
    -   Connect the `simulationTime` output from **Isaac Read Simulation Time** to the `timeStamp` input on the **ROS 2 Publish Laser Scan** node.

    ![[image(6).png]]
    >*The completed action graph connecting the Lidar sensor to a ROS 2 publisher.*

> ⚠️ **Troubleshooting: Nodes Not Found?**
> If you can't find the ROS 2 nodes, make sure the ROS 2 Bridge extension is enabled. You can check this in **Window > Extensions**.
> ![[image(7).png]]
> *The ROS 2 Bridge extension must be enabled for the nodes to be available.*

---

## Configuring the Nodes

Proper configuration is necessary for the system to work correctly.

1.  Configure the Lidar Reader
    -   Select the **Isaac Read Lidar Beams** node.
    -   In its **Property** panel, click **Add Target** for the `lidarPrim` input and search for your Lidar prim.

2.  Configure the ROS 2 Publisher
    -   Select the **ROS 2 Publish Laser Scan** node.
    -   In its properties, set the **Topic Name** to something descriptive, like `/scan_lidar`. This is the topic the data will be published to.
    -   Take note of the **Frame ID** (e.g., "lidar"), as you will need this exact name in RViz.

    ![[image(8).png]]
    >*Configuring the topic name and frame ID in the publisher node's properties.*

---

## Visualizing in ROS 2

Now you can run the simulation and view the data in RViz.

1.  Verify the ROS 2 Topic
    -   Press **Play** in the Isaac Sim simulation.
    -   In a ROS 2 sourced terminal, run the command `ros2 topic list`. You should see your topic (e.g., `/scan_lidar`) in the list.

2.  Launch and Configure RViz
    -   In the terminal, launch RViz with the command `rviz2`.
    -   In the **Global Options** panel, change the **Fixed Frame** to match the **Frame ID** you noted earlier (e.g., "lidar").
    -   At the bottom-left, click **Add**, then select the **By topic** tab. Find your topic (`/scan_lidar`) and select the **LaserScan** message type.
    
    ![[image(9).png]]
    >*Adding the LaserScan topic for visualization in RViz.*

You should now see the Lidar's data points visualized in RViz. The red dots correspond to the sensor lines shown in the Isaac Sim viewport.

![[image(10).png]]
>*The red dots in RViz represent the Lidar's detected points.*

![[image(11).png]]
>*Isaac Sim Simulation - For reference*
# Next Steps
- [[Importing URDF Assets - Isaac Sim]]
