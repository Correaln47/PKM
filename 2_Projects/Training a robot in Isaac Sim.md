Setting up the robot: https://docs.isaacsim.omniverse.nvidia.com/latest/robot_setup_tutorials/tutorial_import_assemble_manipulator.html#connect-the-ur10e-robot-with-the-robotiq-2f-140-gripper

to create a proeyct run ./isaaclab.sh --new on the IsaacLab folder

When  locally installed use  `/home/user/IsaacLab/isaaclab.sh -p` Instead of python when launching commands

To create an RL task use the External project

For the Reach example

Task type: External
Project path: Any path
Project nameL Any name 
[[Isaac Lab Workflow]]: Manager-based | single-agent
RL library: skrl 
RL algorithm: [[Proximal Policy Optimization]]

Inside the folder where the projct was created run `python -m pip install -e source/ProyectName`
To confirm installation: `python scripts/list_envs.py`

Important: The example proyect created comes with the cartpole project, so some things can have the cartpole specific libraries or functions

under `Reach/source/Reach/Reach/tasks/manager_based/reach`create a file called **ur_gripper.py.**

This code holds  an [Articulation Configuration](https://isaac-sim.github.io/IsaacLab/main/source/api/lab/isaaclab.assets.html#isaaclab.assets.ArticulationCfg) which can be reused in other projects. Change the usd path
``` python
 # Copyright (c) 2022-2025, The Isaac Lab Project Developers (https://github.com/isaac-sim/IsaacLab/blob/main/CONTRIBUTORS.md).
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

"""Configuration for the Universal Robots.
Reference: https://github.com/ros-industrial/universal_robot
"""

import isaaclab.sim as sim_utils
from isaaclab.actuators import ImplicitActuatorCfg
from isaaclab.assets.articulation import ArticulationCfg

UR_GRIPPER_CFG = ArticulationCfg(

# Where is the USD file for this robot?
spawn=sim_utils.UsdFileCfg(       
    usd_path=f"/home/ubuntu/Reach/UR-with-gripper.usd", 
        activate_contact_sensors=False,
        rigid_props=sim_utils.RigidBodyPropertiesCfg(
            rigid_body_enabled=True,
            max_linear_velocity=1000.0,
            max_angular_velocity=1000.0,
            max_depenetration_velocity=5.0,
        ),
        articulation_props=sim_utils.ArticulationRootPropertiesCfg(
            enabled_self_collisions=True, 
            solver_position_iteration_count=8, 
            solver_velocity_iteration_count=0
        ),
    ),
# What is its initial position of the robot, and its joints?
    init_state=ArticulationCfg.InitialStateCfg(
        joint_pos={
            "shoulder_pan_joint": 0.0,
            "shoulder_lift_joint": -1.712,
            "elbow_joint": 1.712,
            "wrist_1_joint": 0.0,
            "wrist_2_joint": 0.0,
            "wrist_3_joint": 0.0,
        },
    ),
# What parts of the robot move, and how stiff / damped are they?
    actuators={
        "arm": ImplicitActuatorCfg(
            joint_names_expr=[".*"],
            effort_limit=87.0,
            stiffness=800.0,
            damping=40.0,
        ),
        "gripper": ImplicitActuatorCfg(
            joint_names_expr=["finger_joint"],
            stiffness=280,
            damping=28
        ),
    }
)
 ```

It first defines where the file that defines the robot is.
Then whats the initial position. Usually, the initial position is a mid range position for each joint. As for training it provides the best variation capabilities. 
And finally what parts of the robot move and how damped are them. the `.*` expression in the arm implies that every joint moves

Stiffness is like the proportional part in a PD controller. More stiffness means a faster and more agressive movement to the positon. with values too high the robot can overshoot and oscilate. 
Damping is as the derivative, so too high values makes it hard to achieve the position fast but give smoother movement. 
More info about [Joint Tuning](https://docs.isaacsim.omniverse.nvidia.com/latest/robot_setup/ext_isaacsim_robot_setup_gain_tuner.html)

Open the file `source/Reach/Reach/tasks/manager_based/reach/reach_env_cfg.py`

change from `isaaclab_assets.robots.cartpole import CARTPOLE_CFG` for `from .ur_gripper import UR_GRIPPER_CFG`
and 
```python
import isaaclab.sim as sim_utils
from isaaclab.assets import AssetBaseCfg
from isaaclab.envs import ManagerBasedRLEnvCfg
from isaaclab.managers import ActionTermCfg as ActionTerm
from isaaclab.managers import CurriculumTermCfg as CurrTerm
from isaaclab.managers import EventTermCfg as EventTerm
from isaaclab.managers import ObservationGroupCfg as ObsGroup
from isaaclab.managers import ObservationTermCfg as ObsTerm
from isaaclab.managers import RewardTermCfg as RewTerm
from isaaclab.managers import SceneEntityCfg
from isaaclab.managers import TerminationTermCfg as DoneTerm
from isaaclab.scene import InteractiveSceneCfg
from isaaclab.utils import configclass
from isaaclab.utils.noise import AdditiveUniformNoiseCfg as Unoise
from isaaclab.sim.spawners.from_files.from_files_cfg import GroundPlaneCfg


from isaaclab.utils.assets import ISAAC_NUCLEUS_DIR
from . import mdp
```


Update the ReachSceneCfg
```python
@configclass
class ReachSceneCfg(InteractiveSceneCfg):
    """Configuration for a scene."""

    # world
    ground = AssetBaseCfg(
        prim_path="/World/ground",
        spawn=sim_utils.GroundPlaneCfg(),
        init_state=AssetBaseCfg.InitialStateCfg(pos=(0.0, 0.0, -1.05)),
    )

    # robot
    robot = UR_GRIPPER_CFG.replace(prim_path="{ENV_REGEX_NS}/Robot")
    
    # lights
    dome_light = AssetBaseCfg(
        prim_path="/World/DomeLight",
        spawn=sim_utils.DomeLightCfg(color=(0.9, 0.9, 0.9), intensity=5000.0),
    )

    table = AssetBaseCfg(
        prim_path="{ENV_REGEX_NS}/Table",
        spawn=sim_utils.UsdFileCfg(
            usd_path=f"{ISAAC_NUCLEUS_DIR}/Props/Mounts/SeattleLabTable/table_instanceable.usd",
        ),
        init_state=AssetBaseCfg.InitialStateCfg(pos=(0.55, 0.0, 0.0), rot=(0.70711, 0.0, 0.0, 0.70711)),
    )

    # plane
    plane = AssetBaseCfg(
        prim_path="/World/GroundPlane",
        init_state=AssetBaseCfg.InitialStateCfg(pos=[0, 0, -1.05]),
        spawn=GroundPlaneCfg(),
    )

```

We are defining a world, the robot, ligths, the table where the robot will stand, and a plane
We dont define things like the table and light in the USD file as that woudl mean there is no flexibility if we want to change the position the robot is in the table (Useful also to manage terrains in other trainings), or that woudl mean we woudl have hundreds of ligths, when we can have just one for the whole scene

Inthe manager-based workflow we represent the core parts of the markov decision process. 
- Rewards
- Actions
- Observations
- Curriculum
- Events

In the same file we have been working:

We will configure the managers for the task

There are 2 types of fucntions we will work

built in [[MDP Functions]], learn more [MDP functions for observation terms](https://isaac-sim.github.io/IsaacLab/main/source/api/lab/isaaclab.envs.mdp.html)
Custom functions. This is common practice, and we create them inside the mdp folder of the project. 

Actions
They define what the agent can do. In this case it is moving the 6 revolute joints of the robot. The built in `JointPositionActionCfg` class will be used. The names of the joints are the ones present in the USD files, nothing else is necessary. The names can be called in groups with joint_.* for joint_1 joint_2 etc. 

```python
@configclass
class ActionsCfg:
    """Action specifications for the MDP."""

    arm_action: ActionTerm = mdp.JointPositionActionCfg(
        asset_name="robot", 
        joint_names=["shoulder_pan_joint", "shoulder_lift_joint", "elbow_joint", "wrist_1_joint", "wrist_2_joint", "wrist_3_joint"], 
        scale=1, 
        use_default_offset=True, 
        debug_vis=True
    )
```

Commands:
Related to actions. It specifies what to achieve, instead of how to achieve it. 
```python
@configclass
class CommandsCfg:
    """Command terms for the MDP."""

    ee_pose = mdp.UniformPoseCommandCfg(
        asset_name="robot",
        body_name="ee_link", # This is the body in the USD file
        resampling_time_range=(4.0, 4.0),
        debug_vis=True,
# These are essentially ranges of poses that can be commanded for the end of the robot during training
        ranges=mdp.UniformPoseCommandCfg.Ranges(
            pos_x=(0.35, 0.65),
            pos_y=(-0.2, 0.2),
            pos_z=(0.15, 0.5),
            roll=(0.0, 0.0),
            pitch=(math.pi / 2, math.pi / 2),
            yaw=(-3.14, 3.14),
        ),
    )
```

Observations
Things important for the policy that need to be extracted

```python
@configclass
class ObservationsCfg:
    """Observation specifications for the MDP."""

    @configclass
    class PolicyCfg(ObsGroup):
        """Observations for policy group."""

        # observation terms (order preserved)
        joint_pos = ObsTerm(func=mdp.joint_pos_rel, noise=Unoise(n_min=-0.01, n_max=0.01))
        joint_vel = ObsTerm(func=mdp.joint_vel_rel, noise=Unoise(n_min=-0.01, n_max=0.01))
        pose_command = ObsTerm(func=mdp.generated_commands, params={"command_name": "ee_pose"})
        actions = ObsTerm(func=mdp.last_action)

        def __post_init__(self):
            self.enable_corruption = True
            self.concatenate_terms = True

    # observation groups
    policy: PolicyCfg = PolicyCfg()

```

Terminations
What defines the limit of the training episode. Too much time if time sensitive, positions of the robot, out of bounds, etc

```python
@configclass
class TerminationsCfg:
    """Termination terms for the MDP."""

    time_out = DoneTerm(func=mdp.time_out, time_out=True)
```

Events
In this case a reset event happens when the robot ends.

In this case a random position is set, for the starting position. multiplying the default position value used. Thats one of the reasons is useful to define the robot starting position in the center, appart from training benefits. Still in case the value surpasses the defined limits in the usd, the simualtion would cap it

```python
@configclass
class EventCfg:
    """Configuration for events."""

    reset_robot_joints = EventTerm(
        func=mdp.reset_joints_by_scale,
        mode="reset",
        params={
            "position_range": (0.75, 1.25),
            "velocity_range": (0.0, 0.0),
        },
    )

```

Reward
```python
@configclass
class RewardsCfg:
    """Reward terms for the MDP."""

    # task terms
    end_effector_position_tracking = RewTerm(
        func=mdp.position_command_error,
        weight=-0.2,
        params={"asset_cfg": SceneEntityCfg("robot", body_names=["ee_link"]), "command_name": "ee_pose"},
    )
    end_effector_position_tracking_fine_grained = RewTerm(
        func=mdp.position_command_error_tanh,
        weight=0.1,
        params={"asset_cfg": SceneEntityCfg("robot", body_names=["ee_link"]), "std": 0.1, "command_name": "ee_pose"},
    )

    # action penalty
    action_rate = RewTerm(func=mdp.action_rate_l2, weight=-0.0001)
    joint_vel = RewTerm(
        func=mdp.joint_vel_l2,
        weight=-0.0001,
        params={"asset_cfg": SceneEntityCfg("robot")},
    )

```

Here we reward closeness to the position in 2 ways. Linear way for a general distance with a high reward. But also, to improve precision, an exponential reward that grows high in the very close cases
Also we penalize sudden changes, and high speeds.

Curriculum


```python
@configclass
class CurriculumCfg:
    """Curriculum terms for the MDP."""

    action_rate = CurrTerm(
        func=mdp.modify_reward_weight, params={"term_name": "action_rate", "weight": -0.005, "num_steps": 4500}
    )

    joint_vel = CurrTerm(
        func=mdp.modify_reward_weight, params={"term_name": "joint_vel", "weight": -0.001, "num_steps": 4500}
    )
```



Instatiating Managers
This is the place were we reference the functions we created and sepcifiy some parameters, like number of enviroments, the spacing between them, etc. 


```python
@configclass
class ReachEnvCfg(ManagerBasedRLEnvCfg):
    """Configuration for the reach end-effector pose tracking environment."""

    # Scene settings - how many robots, how far apart?
    scene = ReachSceneCfg(num_envs=2000, env_spacing=2.5)
    # Basic settings
    observations = ObservationsCfg()
    actions = ActionsCfg()
    commands: CommandsCfg = CommandsCfg()
    # MDP settings
    rewards = RewardsCfg()
    terminations = TerminationsCfg()
    events = EventCfg()
    curriculum = CurriculumCfg()

    def __post_init__(self):
        """Post initialization."""
        # general settings
        self.decimation = 2
        self.sim.render_interval = self.decimation
        self.episode_length_s = 3.0
        self.viewer.eye = (3.5, 3.5, 3.5)
        # simulation settings
        self.sim.dt = 1.0 / 60.0
```

Configuration for play (Trained model view)\
at the end add
```python
@configclass
class ReachEnvCfg_PLAY(ReachEnvCfg):
    def __post_init__(self):
        # post init of parent
        super().__post_init__()
        # make a smaller scene for play
        self.scene.num_envs = 50
        self.scene.env_spacing = 2.5
        # disable randomization for play
        self.observations.policy.enable_corruption = False
```
to register that entry point, in the init file inside `source/Reach/Reach/tasks/manager_based/reach/__init__.py`





Now we need to configure the rewards to actually use the functions we were calling before. These are custom mdp fucntions
inside mdp/rewards.py

remove everything and put this
```python
# Copyright (c) 2022-2025, The Isaac Lab Project Developers.
# All rights reserved.
#
# SPDX-License-Identifier: BSD-3-Clause

from __future__ import annotations

import torch
from typing import TYPE_CHECKING

from isaaclab.managers import SceneEntityCfg
from isaaclab.utils.math import combine_frame_transforms

from isaaclab.assets import RigidObject
from isaaclab.managers import SceneEntityCfg
from isaaclab.utils.math import combine_frame_transforms, quat_error_magnitude, quat_mul


if TYPE_CHECKING:
    from isaaclab.envs import ManagerBasedRLEnv
```

Ecluedian positon command error
L2-norm

```python
def position_command_error(env: ManagerBasedRLEnv, command_name: str, asset_cfg: SceneEntityCfg) -> torch.Tensor:
    """Penalize tracking of the position error using L2-norm.

    The function computes the position error between the desired position (from the command) and the
    current position of the asset's body (in world frame). The position error is computed as the L2-norm
    of the difference between the desired and current positions.
    """
    # extract the asset (to enable type hinting)
    asset: RigidObject = env.scene[asset_cfg.name]
    command = env.command_manager.get_command(command_name)
    # obtain the desired and current positions
    des_pos_b = command[:, :3]
    des_pos_w, _ = combine_frame_transforms(asset.data.root_state_w[:, :3], asset.data.root_state_w[:, 3:7], des_pos_b)
    curr_pos_w = asset.data.body_state_w[:, asset_cfg.body_ids[0], :3]  # type: ignore
    return torch.norm(curr_pos_w - des_pos_w, dim=1)
```


Posituon command error tanh
The exponential when close rule
```python
def position_command_error_tanh(
    env: ManagerBasedRLEnv, std: float, command_name: str, asset_cfg: SceneEntityCfg
) -> torch.Tensor:
    """Reward tracking of the position using the tanh kernel.

    The function computes the position error between the desired position (from the command) and the
    current position of the asset's body (in world frame) and maps it with a tanh kernel.
    """
    # extract the asset (to enable type hinting)
    asset: RigidObject = env.scene[asset_cfg.name]
    command = env.command_manager.get_command(command_name)
    # obtain the desired and current positions
    des_pos_b = command[:, :3]
    des_pos_w, _ = combine_frame_transforms(asset.data.root_state_w[:, :3], asset.data.root_state_w[:, 3:7], des_pos_b)
    curr_pos_w = asset.data.body_state_w[:, asset_cfg.body_ids[0], :3]  # type: ignore
    distance = torch.norm(curr_pos_w - des_pos_w, dim=1)
    return 1 - torch.tanh(distance / std)
```


Hyperparameters

[Intuition behing PPO](https://huggingface.co/blog/deep-rl-ppo#the-intuition-behind-ppo)
```python
seed: 42


# Models are instantiated using skrl's model instantiator utility
# https://skrl.readthedocs.io/en/latest/api/utils/model_instantiators.html
models:
  separate: False
  policy:  # see gaussian_model parameters
    class: GaussianMixin
    clip_actions: False
    clip_log_std: True
    min_log_std: -20.0
    max_log_std: 2.0
    initial_log_std: 0.0
    network:
      - name: net
        input: STATES
        layers: [64, 64]
        activations: elu
    output: ACTIONS
  value:  # see deterministic_model parameters
    class: DeterministicMixin
    clip_actions: False
    network:
      - name: net
        input: STATES
        layers: [64, 64]
        activations: elu
    output: ONE


# Rollout memory
# https://skrl.readthedocs.io/en/latest/api/memories/random.html
memory:
  class: RandomMemory
  memory_size: -1  # automatically determined (same as agent:rollouts)


# PPO agent configuration (field names are from PPO_DEFAULT_CONFIG)
# https://skrl.readthedocs.io/en/latest/api/agents/ppo.html
agent:
  class: PPO
  rollouts: 24
  learning_epochs: 5
  mini_batches: 4
  discount_factor: 0.99
  lambda: 0.95
  learning_rate: 1.0e-03
  learning_rate_scheduler: KLAdaptiveLR
  learning_rate_scheduler_kwargs:
    kl_threshold: 0.01
  state_preprocessor: RunningStandardScaler
  state_preprocessor_kwargs: null
  value_preprocessor: RunningStandardScaler
  value_preprocessor_kwargs: null
  random_timesteps: 0
  learning_starts: 0
  grad_norm_clip: 1.0
  ratio_clip: 0.2
  value_clip: 0.2
  clip_predicted_values: True
  entropy_loss_scale: 0.01
  value_loss_scale: 1.0
  kl_threshold: 0.0
  rewards_shaper_scale: 1.0
  time_limit_bootstrap: False
  # logging and checkpoint
  experiment:
    directory: "reach_ur10"
    experiment_name: ""
    write_interval: auto
    checkpoint_interval: auto


# Sequential trainer
# https://skrl.readthedocs.io/en/latest/api/trainers/sequential.html
trainer:
  class: SequentialTrainer
  timesteps: 24000
  environment_info: log

```



Testing before
Is good practice to first run an enviroment with few robots that loads the robot and dont move, just to check everithyng is in place
```bash
python scripts/zero_agent.py --task Template-Reach-v0 --num_envs=10

```
Also, one where a random agent controls the robot, to see everyhting is moving as it should
```bash
python scripts/random_agent.py --task Template-Reach-v0 --num_envs=10
```

The task name can be changed in `source/Reach/Reach/tasks/manager_based/reach/__init__.py`

For training we can run with the `--headless` flag, which can improve performance

```bash
python scripts/skrl/train.py --task Template--Reach-v0

```

![[Pasted image 20250913220232.png]]
Orientation is not taken into accoun :O
In rewards.py
```python
def orientation_command_error(env: ManagerBasedRLEnv, command_name: str, asset_cfg: SceneEntityCfg) -> torch.Tensor:
    """Penalize tracking orientation error using shortest path.

    The function computes the orientation error between the desired orientation (from the command) and the
    current orientation of the asset's body (in world frame). The orientation error is computed as the shortest
    path between the desired and current orientations.
    """
    # extract the asset (to enable type hinting)
    asset: RigidObject = env.scene[asset_cfg.name]
    command = env.command_manager.get_command(command_name)
    # obtain the desired and current orientations
    des_quat_b = command[:, 3:7]
    des_quat_w = quat_mul(asset.data.root_state_w[:, 3:7], des_quat_b)
    curr_quat_w = asset.data.body_state_w[:, asset_cfg.body_ids[0], 3:7]  # type: ignore
    return quat_error_magnitude(curr_quat_w, des_quat_w)

```

And in reach_env_cfg.py inside RewardsCgf
```python
end_effector_orientation_tracking = RewTerm(
        func=mdp.orientation_command_error,
        weight=-0.1,
        params={"asset_cfg": SceneEntityCfg("robot", body_names=["ee_link"]), "command_name": "ee_pose"},
    )
```

Play the result
```bash
python scripts/skrl/play.py --task Template--Reach-v0 --num_envs=200
```

