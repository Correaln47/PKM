Using the Unitree G1 create a task that makes it learn to pick up a cube. 

Relevant joints
Left Arm:
- left_shoulder_pitch_joint
- left_shoulder_yaw_joint
- left_shoulder_roll_joint
- left_elbow_joint
- left_wrist_roll_joint
- left_wrist_pitch_joint
- left_wrist_yaw_joint
RigthArm:
- right_shoulder_pitch_joint
- right_shoulder_yaw_joint
- right_shoulder_roll_joint
- right_elbow_joint
- right_wrist_roll_joint
- right_wrist_pitch_joint
- right_wrist_yaw_joint
LeftHand:
- left_hand_index_0_joint
- left_hand_index_1_joint
- left_hand_middle_0_joint
- left_hand_middle_1_joint
- left_hand_thumb_0_joint
- left_hand_thumb_1_joint
- left_hand_thumb_2_joint
RightHand:
- right_hand_index_0_joint
- right_hand_index_1_joint
- right_hand_middle_0_joint
- right_hand_middle_1_joint
- right_hand_thumb_0_joint
- right_hand_thumb_1_joint
- right_hand_thumb_2_joint

1. Create the articulation configuration file. 
	Under source/projectname/projectname/tasks/manager_based/projectname/ create a python file
	```python
	import isaaclab.sim as sim_utils
from isaaclab.actuators import ImplicitActuatorCfg
from isaaclab.assets.articulation import ArticulationCfg

G1_CFG = ArticulationCfg(

# Where is the USD file for this robot?
spawn=sim_utils.UsdFileCfg(       
    usd_path=f"/home/nicolukas/ILProjects/G1Grasp/g1.usd", 
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
            "left_shoulder_pitch_joint": 0.0,
            "left_shoulder_yaw_joint": 0.0,
            "left_shoulder_roll_joint": 0.0,
            "left_elbow_joint": 0.0,
            "left_wrist_roll_joint": 0.0,
            "left_wrist_pitch_joint": 0.0,
            "left_wrist_yaw_joint": 0.0,
            "left_shoulder_yaw_joint": 0.0,
            "right_shoulder_pitch_joint": 0.0,
            "right_shoulder_yaw_joint": 0.0,
            "right_shoulder_roll_joint": 0.0,
            "right_elbow_joint": 0.0,
            "right_wrist_roll_joint": 0.0,
            "right_wrist_pitch_joint": 0.0,
            "right_wrist_yaw_joint": 0.0,
            "left_hand_index_0_joint": 0.0,
            "left_hand_index_1_joint": 0.0,
            "left_hand_middle_0_joint": 0.0,
            "left_hand_middle_1_joint": 0.0,
            "left_hand_thumb_0_joint": 0.0,
            "left_hand_thumb_1_joint": 0.0,
            "left_hand_thumb_2_joint": 0.0,
            "right_hand_index_0_joint": 0.0,
            "right_hand_index_1_joint": 0.0,
            "right_hand_middle_0_joint": 0.0,
            "right_hand_middle_1_joint": 0.0,
            "right_hand_thumb_0_joint": 0.0,
            "right_hand_thumb_1_joint": 0.0,
            "right_hand_thumb_2_joint": 0.0,
        },
    ),
# What parts of the robot move, and how stiff / damped are they?
    actuators={
        "left_arm": ImplicitActuatorCfg(
            joint_names_expr=["left_shoulder_pitch_joint",
            "left_shoulder_yaw_joint",
            "left_shoulder_roll_joint",
            "left_elbow_joint",
            "left_wrist_roll_joint",
            "left_wrist_pitch_joint",
            "left_wrist_yaw_joint",
            "left_shoulder_yaw_joint"],
            effort_limit=200.0,
            stiffness=800.0,
            damping=40.0,
        ),
        "right_arm": ImplicitActuatorCfg(
            joint_names_expr=["right_shoulder_pitch_joint",
            "right_shoulder_yaw_joint",
            "right_shoulder_roll_joint",
            "right_elbow_joint",
            "right_wrist_roll_joint",
            "right_wrist_pitch_joint",
            "right_wrist_yaw_joint"],
            effort_limit=200.0,
            stiffness=800.0,
            damping=40.0,
        ),
        "left_hand": ImplicitActuatorCfg(
            joint_names_expr=["left_hand_index_0_joint",
            "left_hand_index_1_joint",
            "left_hand_middle_0_joint",
            "left_hand_middle_1_joint",
            "left_hand_thumb_0_joint",
            "left_hand_thumb_1_joint",
            "left_hand_thumb_2_joint"],
            stiffness=280,
            damping=28
        ),
        "right_hand": ImplicitActuatorCfg(
            joint_names_expr=["right_hand_index_0_joint",
            "right_hand_index_1_joint",
            "right_hand_middle_0_joint",
            "right_hand_middle_1_joint",
            "right_hand_thumb_0_joint",
            "right_hand_thumb_1_joint",
            "right_hand_thumb_2_joint"],
            stiffness=280,
            damping=28
        ),
    }
)
	```


assembler fixed joint




 python scripts/skrl/train.py --task Template-G1grasp-v0 --checkpoint logs/skrl/G1_grasp/2025-10-05_21-00-04_ppo_torch/checkpoints/agent_20000.pt --headless   