[[URDF]]s, or Universal Robot Definition files are essential to describe a robot to a simulation program, like Isaac Sim in this case. They are what tells the program how the robot **looks**, **is connected**, and **physics properties of its parts**

# Importing a URDF Robot (Carter Bot)

## 1. Initiating the Import

1.  **Navigate to the Import Menu**
    * From the top menu bar, go to **File > Import**.

2.  **Select the URDF File**
    * In the pop-up window that appears, browse to and select the `.urdf` file for your robot. For this example, we'll use the [Carter Bot](https://learn.learn.nvidia.com/asset-v1:DLI%2BS-OV-29%2BV1%2Btype@asset%2Bblock@carter.zip).

---

## 2. Configuring the Importer Settings

In the importer window's right-side panel, configure the following settings before finalizing the import. For further details on all options, see [[URDF Importer Settings - Isaac Sim]].
![[image(12).png]]

### Base Configuration
1.  **Set the Base Type**
    * Under the *Links* section, check the **moveable base** option.
    * > **Note:** This option is for mobile robots that move around. For stationary robots like manipulator arms, you would typically select **static base**.

### Joints and Drives
1.  **Set Drive Properties**
    * Under the *Joints and Drives* section, make sure **Natural Frequency** is selected.
    * In the table listing the robot's joints, find the `left_wheel` and `right_wheel` joints.
    * Change their **Target Type** to **Velocity**. This allows us to send speed commands to the wheels.
    * Set the **Target Type** for all other joints to **None**, as they are passive.

2.  **Finalize the Import**
    * Click the **Import** button to load the robot into the stage.
![[image(13).png]]

---

## 3. Setting Up the Scene

1.  **Add a Ground Plane**
    * To prevent the robot from falling into the void, add a surface for it to stand on.
    * Go to **Create > Physics > Ground Plane**.

2.  **Ensure a Physics Scene Exists**
    * A physics scene is required to simulate gravity and collisions. This is usually created automatically when importing a URDF.
    * If one isn't present in the Stage tree, you can add it manually via **Create > Physics > Physics Scene**.
![[image(14).png]]

---

## 4. Making the Robot Move

Now that the robot is imported, you can make it move. You can either change the wheel drive speeds directly or implement a more advanced controller.

### Implementing a Differential Drive
For more realistic control, you can add a differential drive controller, similar to the one in [[Building a Simple Robot in Isaac Sim]]. The key parameters for the Carter Bot are:
* **Wheel Radius**: `0.24` meters
* **Distance Between Wheels**: `0.62` meters