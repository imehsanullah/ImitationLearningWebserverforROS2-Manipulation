# Imitation Learning Pipeline for Teleoperated Robotic Manipulation in a ROS2 Webserver Platform

Letting an operator teleoperate a manipulator from the browser, record demonstrations, convert them into a structured dataset, train a behavior-cloning policy, and deploy that policy back into the ROS2 control loop.

## Targeted imitation learning task

The targeted task is teleoperated pick-and-place style manipulation in simulation. The system records robot observations, such as joint state, gripper state, end-effector pose, and object pose, together with operator actions, then trains a behavior-cloning policy to predict the next safe manipulation command through the ROS2 control loop.

## Architecture diagram

[![Architecture diagram — imitation learning pipeline](docs/imitation-learning-preview.png)](https://www.figma.com/design/Cb6d1QYDuZrXgJxJ397W5o/Imitation-Learning?node-id=0-1)

*Click the diagram to open the editable Figma file.*

The web frontend includes a robot-agnostic Three.js robot visualizer integrated directly into the Web UI. It displays the selected robot, scene, trajectories, and live state feedback in the browser while sharing the same interface used for teleoperation, recording demonstrations, dataset inspection, training, policy deployment, and monitoring. Three.js is only the visualization layer: the actual robot state comes from ROS2 topics exposed through the web-to-ROS bridge, and those states are produced by the Gazebo simulation.

MoveIt2 is used for manipulation-aware planning and validation. It provides the planning scene and state-validity checks needed to reject unsafe or infeasible teleoperation and policy commands before they reach the robot controllers.

## Robot-agnostic Three.js robot visualizer

The solution is designed around a robot-agnostic webserver and Three.js robot visualizer rather than a UI hardcoded for a single arm. The frontend receives a robot description, joint names, controller topics, gripper configuration, and visualization assets, then uses the same browser controls and Three.js scene to operate different manipulators through ROS2.

In the Web UI, the visualizer is paired with teleoperation controls so the operator can move the arm from the browser and immediately see the simulated robot response. The same interaction loop is also used during demonstration recording: user commands are sent through the ROS2 bridge, validated by the backend, executed in Gazebo through ROS2 controllers, and streamed back to the Three.js view as updated joint states and scene feedback.

For this prototype, I targeted and tested three robot arms:

- FR3
- UR5e
- xArm7

The same webserver flow is used for all three: browser teleoperation, Three.js visualization, ROS2 bridge communication, Gazebo simulation, MoveIt2 validation, controller execution, and feedback streaming back to the UI. Extending the platform to another manipulator should only require small configuration changes, such as adding the robot model, joint list, controller topics, MoveIt2 planning group, gripper interface, and visualization assets.

### Browser robot demos

![FR3 browser demo](docs/fr3-browser-demo.gif)

![UR5e browser demo](docs/ur5e-browser-demo.gif)

![xArm7 browser demo](docs/xarm7-browser-demo.gif)

## Web UI

![Web UI — ROS2 webserver platform](docs/web-ui.png)

### Sending teleoperation from the webserver

The operator sends teleoperation commands directly from the webserver UI. The frontend publishes the command through the ROS2 bridge, while the actual physics, robot motion, joint updates, and scene feedback come from the Gazebo simulation and ROS2 controllers.

![Sending teleoperation from the webserver](docs/sending-teleop.gif)

In addition to manual teleoperation, demonstration data can also be generated from the backend. This is useful for quickly creating repeatable sample episodes for testing dataset conversion, training, and policy deployment without manually recording every run.

![Backend-generated demonstration](docs/backend-generated.gif)
