# Imitation Learning Pipeline for Teleoperated Robotic Manipulation in a ROS2 Webserver Platform

Letting an operator teleoperate a manipulator from the browser, record demonstrations, convert them into a structured dataset, train a behavior-cloning policy, and deploy that policy back into the ROS2 control loop.

## Targeted imitation learning task

The targeted task is teleoperated pick-and-place style manipulation in simulation. The system records robot observations, such as joint state, gripper state, end-effector pose, and object pose, together with operator actions, then trains a behavior-cloning policy to predict the next safe manipulation command through the ROS2 control loop.

## Architecture diagram

[![Architecture diagram — imitation learning pipeline](docs/imitation-learning-preview.png)](https://www.figma.com/design/Cb6d1QYDuZrXgJxJ397W5o/Imitation-Learning?node-id=0-1)

*Click the diagram to open the editable Figma file.*

The web UI is robot-agnostic: Three.js only renders the arm, scene, and trajectories, while joint and object state arrive over the web-to-ROS bridge from Gazebo. The same screen supports teleoperation, demonstration recording, dataset inspection, training, and deployment; swapping arms is mostly configuration (robot description, joint names, controller topics, gripper, and visualization assets).

MoveIt2 provides the planning scene and state-validity checks so unsafe or infeasible teleoperation and policy commands are rejected before they reach the controllers.

For this prototype, I targeted and tested three robot arms:

- FR3
- UR5e
- xArm7

### Browser robot demos

![FR3 browser demo](docs/fr3-browser-demo.gif)

![UR5e browser demo](docs/ur5e-browser-demo.gif)

![xArm7 browser demo](docs/xarm7-browser-demo.gif)

## Web UI

![Web UI — ROS2 webserver platform](docs/web-ui.png)

The Web UI exposes the main imitation-learning workflow through four panels:

- **Recording:** select the task, choose teleoperation or backend-generated mode, reset the cube pose, and start/mark demonstration episodes.
- **Dataset:** inspect collected episodes, sample counts, success rate, and whether data came from teleoperation or backend generation.
- **Training:** trigger behavior-cloning training from the collected dataset and monitor training/validation loss.
- **Deployment:** load a trained checkpoint, run or pause the policy, and monitor whether the policy runtime is loaded.

### Sending teleoperation from the webserver

The operator sends teleoperation commands directly from the webserver UI. The frontend publishes the command through the ROS2 bridge, while the actual physics, robot motion, joint updates, and scene feedback come from the Gazebo simulation and ROS2 controllers.

![Sending teleoperation from the webserver](docs/sending-teleop.gif)

In addition to manual teleoperation, demonstration data can also be generated from the backend. This is useful for quickly creating repeatable sample episodes for testing dataset conversion, training, and policy deployment without manually recording every run.

![Backend-generated demonstration](docs/backend-generated.gif)
