# Simulation for the LeRobot hackathon
This simulation environment that will be used for the [LeRobot Hackathon 2024](https://github.com/huggingface/lerobot_hackathon_oct2024).

## General outline and goals
- Train the [simulation environment](https://github.com/michel-aractingi/gym-lowcostrobot/tree/environment_fixes) of the Koch arms to perform basic manipulation tasks.
- Use the [TD-MPC implementation](https://github.com/huggingface/lerobot/tree/user/michel-aractingi/2024-10-15-control-sim/lerobot/common/policies/tdmpc) in this branch of LeRobot to train your policies to acheive different tasks.
- If you have a leader arm, you can use the `control_sim_robot.py` script [in my branch](https://github.com/huggingface/lerobot/blob/user/michel-aractingi/2024-10-15-control-sim/lerobot/scripts/control_sim_robot.py) to teleoperate the sim robot and collect datasets that you can couple with the RL part.

## Simulation and tasks
We will be using the sim environment [this branch of my fork](https://github.com/michel-aractingi/gym-lowcostrobot/tree/environment_fixes) of the original [gym-lowcostrobot](https://github.com/perezjln/gym-lowcostrobot/tree/main). The enviroment is based on Mujoco and defines several manipulation tasks like pushing a cube to a target and pick and place. 

- **The main task of the hackathon** would be to mimic the real-world task that the participants with the arm will try to solve and attempt to find ideas that improves the learning performance in sim which can then benifit learning in the real-world. What can you change?
  1. The hyper-parameters of TD-MPC. You can find different parameters of the algorithm that could improve the overall learning performance.
  2. The elements of the sim environment: reward shaping, action space, observations...
- You have the freedom to try to design and solve your own task or to work on much harder tasks in the environment (pick and place, stack two cubes).
- _**It is important to note**_ that the rewards for many of these enviornemnt are not well tuned and it will be up to you to improve their design and find ideas to make them work better.


## Code 
Get familiar with the following code:
- `lerobot/scripts/control_sim_robot.py`: code to run control in sim if you have a leader arm. Choose the record option to record a dataset in sim.
- `lerobot/scripts/train.py`: Updated in my branch to be able to use it with sim environment and with different platforms.
- `gym_lowcostrobot/envs/*`: The gym environment for each task, check how the observations, control and rewards are computed.
- `gym_lowcostrobot/assets/*`: The environment description that defines the scene, objects and physics.
  
## Useful links
- Mujoco [documentation](https://mujoco.readthedocs.io/en/stable/overview.html).
- [TD-MPC](https://arxiv.org/abs/2203.04955) and [FOWM](https://www.yunhaifeng.com/FOWM/paper.pdf) papers.
- **Highly Recomended**: The videos by Alexander Soare that explain TD-MPC (part [1](https://www.youtube.com/watch?v=--hDN4LLmPI&t=397s&ab_channel=HuggingFace) and part [2](https://www.youtube.com/watch?v=_CKJJRAlvKI&ab_channel=HuggingFace)) and [implementation of TD-MPC](https://www.youtube.com/watch?v=2crxpBojS04&ab_channel=HuggingFace) in LeRobot.

## Examples of configuration files for the sim environments.
For the sim env in lerobot/configs/env you need to define a config file that takes into account the proper gym arguments to setup the simulation. Example for the push task
```
# @package _global_

fps: 50

env:
  name: lowcostrobot
  fps: ${fps}
  handle: PushCube-v0 
  state_dim: 6
  action_dim: 6
  task: PushCube

  gym:
    render_mode: rgb_array
    max_episode_steps: 200
    actions_in_degrees: true
    reward_type: 'dense'

calibration:
  axis_directions: [-1, -1, 1, -1, -1, -1]
  offsets: [0, -0.5, -0.5, 0, -0.5, 0] # factor of pi

eval:
  use_async_envs: false

state_keys:
  observation.state: 'arm_qpos'
  observation.velocity: 'arm_qvel'
```
