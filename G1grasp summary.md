Project: G1Grasp - Debugging Simulation Instability

  Initial Problem:

  The user reported an issue where the simulated G1 robot would disappear when the simulation started. The robot model would load correctly, but it would vanish as soon as the
  simulation began. If the simulation was cancelled, the robot would reappear. This behavior was observed when running a trained model using the play.py script.

  Initial Investigation and `inotify` Issue:

  The initial investigation focused on the play.py script and the associated configuration files. However, the user provided error logs from the terminal that showed multiple
  instances of the error errno=28/No space left on device. This error indicated that the system was running out of resources, which was preventing the Isaac Sim simulation from
  starting correctly.

  Further investigation revealed that the user's system was hitting the inotify watch limit. The inotify subsystem in Linux allows applications to monitor files for changes,
  and there is a limit to the number of files that a user can watch. The Isaac Sim simulation was trying to watch a large number of files in the user's virtual environment,
  which was exceeding the default limit.

  Resolution: The user resolved this issue by increasing the inotify watch limit on their system. This allowed the simulation to start without the "No space left on device"
  errors.

  The `NaN` Hypothesis:

  After resolving the inotify issue, the original problem persisted: the robot was still disappearing. This led to the hypothesis that the robot's state was becoming invalid
  during the simulation, likely due to NaN (Not a Number) values. This would cause the renderer to fail to draw the robot, but the robot would still exist in the scene graph,
  which would explain why it reappeared when the simulation was stopped.

  Debugging the `NaN` Issue:

  A systematic debugging process was undertaken to identify the source of the NaN values.

   1. Logging: Logging statements were added to the play.py script to inspect the agent's actions and the robot's state during the simulation. The logs confirmed the NaN
      hypothesis: both the agent's actions and the robot's state (position and orientation) were becoming NaN from the very first step of the simulation.

   2. Action Clipping: The PPO agent's configuration file (skrl_ppo_cfg.yaml) was examined. The clip_actions parameter was set to True to prevent the agent from taking extreme
      actions that could destabilize the simulation. This did not resolve the issue.

   3. Quaternion Sanitization: The reward functions in rewards.py were reviewed. The move_to_goal_reward function contained a block of code to "sanitize" the robot's root
      quaternion. This sanitization was made more robust by adding an explicit check for NaN and inf values. This did not resolve the issue.

   4. Physics Stability: The robot's configuration file (g1_cfg.py) was examined. To improve the stability of the physics simulation, self-collisions were enabled, and the solver
      iteration counts were increased. This did not resolve the issue.

  The Ground Plane Issue:

  The user discovered that the ground plane in the simulation was too small. When a large number of environments were simulated, many of the robots were spawning outside the
  ground plane and falling out of the world.

  Resolution: The size of the ground plane was increased in the g1grasp_env_cfg.py file, and a redundant ground plane definition was removed. This resolved the issue for
  simulations with a smaller number of environments.

  Recurrence of the Problem:

  The NaN issue reappeared when the user trained the model with a larger number of environments (2000, as recommended in the configuration file). A model trained with a smaller
  number of environments (200) worked correctly. This indicated that the problem was related to the scale of the simulation and the complexity of the learned policy.

  Final Investigation and Unresolved Issue:

  The final investigation focused on the initial state of the simulation. Further logging revealed that the robot's body quaternions (body_quat_w) were becoming NaN from the
  very first step, even before the agent had taken any action. This confirmed that the problem was with the initial state of the physics simulation.

  Despite all efforts to stabilize the simulation, the NaN issue persisted when training with a large number of environments. The conclusion of the debugging process is that
  the problem is likely a bug in the Isaac Sim engine or a subtle issue with the robot's g1.usd asset file that only manifests at scale.

  Retrospective Analysis of Custom Code:

  A review of the user's custom code revealed several important design considerations.

   * Joint Locking: The user had previously made changes to lock some of the robot's joints using the locked_joints actuator in g1_cfg.py. This is a common and effective
     technique for simplifying a complex robotics task during the initial stages of development.

   * Reward and Observation Logic: The user had previously overhauled the reward and observation logic because the old logic was causing the simulation to crash. This highlights
     the critical importance of careful reward and observation design in reinforcement learning.

       * Reward Shaping: The environment uses a staged reward structure, which is excellent for guiding the agent through a complex task. The rewards are broken down into smaller,
         more manageable pieces (reaching, grasping, lifting, moving to goal).

       * Observation Space: The observation space is well-designed and provides the agent with the necessary information to solve the task, including joint states, the goal pose,
         and the pose of the cube relative to the end-effectors.

       * Sources of Instability: The previous "crashing" issue was likely caused by NaN values being generated in the reward or observation functions, particularly from the
         complex quaternion mathematics. The current code includes sanitization steps to mitigate these issues, but as we have seen, they are not sufficient to handle the
         instability that occurs at a large scale.


# Observations
If the model is trained with a small amount of agents (250) the result does work (but the model is very bad). It seems to be something to do with very high datasets

