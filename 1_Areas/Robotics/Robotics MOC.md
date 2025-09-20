# General Concept - High Level Diagram
Every Robot follows the same general structure:

![[HighLevelRoboticsDiagram.drawio.png]]
In the “World” where the robot lies it interacts retroactively with the, or in some cases, multiple environments. The robot internally has 3 main stages. Perception, Cognition and Action, analog to a human, where we perceive with our eyes, think with our brain and move and interact with our limbs.

1. Perception:
    In this stage the robot receives information from the environment. The way it receives information can vary greatly, from a visual image, audio, cloud points, or even environmental data like humidity, temperature, light, etc.
    The tools a robot uses for this stage are called sensors. A robot can also perceive the environment indirectly, meaning, remote sensors or information stored and shared externally.
2. Cognition:
    After receiving the information provided by the perception stage, this data, in whatever form it comes in, passes onto the cognition stage. This is where the robot interprets the data in order to take decisions over the actions it will perform.
    This task, depending on the computing power, setup and interfaces necessary can be as simple as using a [[Micro-controller]] or go up to heavy duty SBCs ([[Single Board Computer]]).
    This stage, on top of converting and analyzing data that enters, is the one in charge of sending the signals on what needs to “go out” to the environment.
3. Action:
    In this stage the signals that come form the cognition stage become real. This can range from complex tasks like manipulating objects, moving, or some more straightforward like sound or light.
    The elements in this stage are generally called [[Actuator]]s. Depending on their specific function they can enter whole different categories. Some systems can be conceptually simplified to be an actuator, but some of them are composed by many of them in order to perform complex tasks, like a robotic hand for example.
    These actions can change (or not) what happens in the environment. Either way, the robot would perceive the change or lack of, and start the loop of this interaction again.

# Robotic Systems and Architectures
## Integration
We can conclude from the analysis of the general structure that robotic systems relies greatly on collaboration between hardware and software components. The end goal is that the robot system has a cohesive structure where sensors, actuators which enable the robot to interact, work together with programs and algorithms that control the robots behavior.
## Simulation and Testing

Before deploying robotic solutions, they need to be validated. There are 2 main ways to do this. The evident one is to test it in the environment we want it to work in. However, this proves limiting and sometimes not viable. There are conditions that are difficult to recreate consistently in the real world, either because they are very specific, or we don't have the resources to do so. Also, real-life testing is risky, any error can damage the robot or its surroundings, including the person testing it.

This is where simulation comes in. The general main objective is to recreate the environment the robot would be in in order to validate the system, both mechanically and software wise. Some programs like [[Gazebo]], [[Webots]], Drake, [[Isaac SIM MOC|Isaac Sim]], [[MuJoCo]], etc. A powerful computer is necessary in order to reliably recreate the robots environment, however, depending on the task, some simulators skip the high fidelity visuals and focus on more complex/low-level physics.

Depending on the specific task and objectives the simulator used could change. For complex AI training environments, that need high visual fidelity (like to simulate cameras) [[Isaac SIM MOC|Isaac Sim]] and [[Unity Simulation]] are the go to. For General purpose simulation, user friendly GUI and straight forward integration with ROS ([[Robot Operating System]], Webots and Gazebo are the most used and documented. For Low-Level Physics and advanced control [[MuJoCo]] and Drake and for lightweight systems [[PyBullet]].

There are 2 main frameworks followed for this type of testing: [[Software in the loop]] (SIL) and [[Hardware in the loop]] (HIL)

# Development Frameworks
Development in robotics often grows complex, and comes to rely on specialized platforms or frameworks. The most known and common example is ROS (Robot operation system).

A framework provides a structure for an application, meaning that it defines how components should be organized, as well as their naming and communication. A middleware framework just focuses on the communication aspect, like the glue of the application.

Each [[Framework|framework]] or [[Middleware|middleware]] has its own use. _**ROS**_ is a complete and open source framework able to manage modular and complex systems. It counts with a vast ecosystem of tools and libraries for specific applications or robots, this is what makes it the most popular choice. _**OROCOS**_ and _**YARP**_ are middleware frameworks focused on low-latency for industrial robots. _**PyBullet**_ is a fast python library used for rapid prototyping, motion planning, used mainly for research and machine learning due to its performance. _**RoboDK**_, _**RobotStudio**_ and _**ROBOGUIDE**_ are specialized platforms for industrial robots. _**MATLAB/Simulink**_ are tools used to design control systems, model and simulate them before they are implemented.
* [[Off-line Programming]] 

## Autonomy Software Architecture
![[SoftwareArchitecture.drawio.png]]
This diagram provides the general structure followed in an autonomous robot, for example a robotaxi, or delivery robot. Other applications or robots may use more or less elements.

Perception/Localization: The main stage are the sensors, like lidars, cameras, GPS, etc receive the information from the environment of the robot. This information goes to 2 elements. The first one is Localization, which defines where the robot is specifically, and the other to perception, where the robot actually understands what's around it. Localization and Perception complete a loop between the two, where Localization can tell the robot what to look for, while perception, understating what is around it, can tell specifically where it is. A mapping database is created with the information perception understands, and this information also helps to define the localization of the robot.

Next, in the planning stage. It starts with a global planning of where it is, and what the goal is, like a localization to get to. The actual localization, as well as the kwon or unknown places feed this planning stage. Next, the behavioral planning determines the actions, like what object it needs to manipulate and how, or if there is an obstacle to avoid, that's why is necessary that whatever it percepts is taken into account. Finally a local planning is what creates specific trajectories or tasks.

The final planning information is fed into the control system, which makes the actuators work. And in turn, the simulation is updated. The changes in the simulation (or real life) change what the sensors detect, completing the loop.

# How to make robots understand their surroundings
### Sensing
- [[Sensors MOC#Cameras|Cameras]]
- [[Sensors MOC#Lidars|Lidars]]
- [[Sensors MOC#Other|Other]]
### Perception
Perception is vital for enabling autonomous robots understand their environment. Just as we use our senses to understand what we have in front of us, robot use something called sensor fusion, as well as other tools to do the same.

Sensor fusion is the process in which different sensors are combined to have a more complete view of the surroundings of the robot. Object detection and recognition is used to identify and classify specific objects in the environment. Object tracking is a compliment to that last part, useful if the environment is in constant change. Scene Understanding is the interpretation of the overall context, used for the robot to take “informed decisions”

RGo perception Engine and Isaac Perceptor are some collection of libraries that help robots understand their environment. Packages like Moveit, ROSNav, OpenCV, OpenSLAM, PyTorch, TensorFlow, also help to integrate multiple sensors into specific perception.

## Localization and Mapping

Knowing where itis, and saving that information is essential for efficient movement. GPS/GNSS, Visual odometry and Lidar Based Localization are the tools more common for these tasks. Leveraging SLAM and occupancy grids, a robot what estimate where it is, and build maps of what they “see”

## Navigation and Path Planning

Knowing where the robot is important, but knowing how to get where it needs to go is the most important part in autonomous robotics. Typical navigation systems use a routing/mission planning using algorithms like A*, D* and RRT. Motion planning is an additional layer, which creates the sequence of movements that allows the robot to follow a planned path. The first component is Decision making, which focuses on behavior planning and interactions, like deciding to avoid an obstacle or yield into a moving group. Then Trajectory generation involves creating the exact trajectory, taking into account speed, accel, and heading angles. Sometimes this isn't enough, so obstacle avoidance is needed. This is usually divided between Real-time detection, which detects objects and obstacles in real time and changes paths accordingly. Reactive Navigation is the last protective wall, that using sensors makes immediate adjustments in case the last stages fails.

## Control Systems

Once a robot defines the trajectory and target, the control system is the one in charge of sending the commands to the robots actuators, but also the one to ensure they follow that desired path.

Tools like feedback control help achieve this. Feedback control uses sensor input, usually methods like PID control are used to minimize the error by continuously adjusting movements. It works by comparing the actual position of an actuator with the expected one, and depending on the parameters of the loop, adjusts accordingly.

Trajectory tracking is a more complex way to ensure the correct movement, a common method used is MPC, where a predictive model of the behavior is used to do the corrections. Is usually used in more complex systems that have intrinsic interactions that can be somewhat predicted.