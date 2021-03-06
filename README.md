# Quadcopter Assisted Evacuation Simulation Work Space
Main workspace for my project done at [Simons Summer Research Program] (http://www.stonybrook.edu/simons/).

See the [poster](https://github.com/michaelhuang14/simons_project_ws/blob/master/SimonsPosterFinal.pdf) for summary of the project.

As the project uses latest [ROS](http://wiki.ros.org/) distribution (kinetic), and latest [Gazebo simulator](http://gazebosim.org/) built from source code,  many ROS packages used by the project are either unavailable in binary form or incompatible with the new Gazebo during the time frame of the project. This workspace serves as a holding area that includes many other components as git submodules. Note some of these submodule may be removed once they are available in compatible binary form

## Dependencies hosted in this work space
1. Modified version of [Menge](https://github.com/michaelhuang14/Menge)
2. [gazebo_ros_pkgs] (https://github.com/ros-simulation/gazebo_ros_pkgs.git)
3. [hector_gazebo] (https://github.com/tu-darmstadt-ros-pkg/hector_gazebo)
4. [hector_localization]( https://github.com/tu-darmstadt-ros-pkg/hector_localization)
5. [hector_models](https://github.com/tu-darmstadt-ros-pkg/hector_models)
6. [hector_quadrotor](https://github.com/tu-darmstadt-ros-pkg/hector_quadrotor)
5. [hector_slam]( https://github.com/tu-darmstadt-ros-pkg/hector_slam)
6. [menge_gazebo](https://github.com/michaelhuang14/menge_gazebo)

## External Dependencies
1. [Gazebo modified for hardware skinning] (https://bitbucket.org/michaelhuang16/gazebo)
   * See build instructions [here](http://gazebosim.org/tutorials?tut=install_from_source&cat=install) on additional dependencies.
   * Gazebo should be built and installed from source.
   * SDFormat also modified to support instanced_actor model with hardware skinning. You can download the modified version from [here](https://bitbucket.org/michaelhuang16/sdformat2).
2. Ubuntu 16.04
3. [ROS kinetic](http://wiki.ros.org/kinetic/Installation/Ubuntu)
   * Do not install full desktop version (ros-kinetic-desktop-full) that includes gazebo7 binaries
   * Only install ros-kinetic-desktop or less
4. Other ROS dependencies can be installed by running "rosdep install --from-paths . --ignore-src --rosdistro kinetic" from "catkin_ws" directory 
   * ignore all gazebo related packages and answer "no" when prompted for confirming installation

## Steps to Build and Run
1. Follow [ROS kinetic installation instructions](http://wiki.ros.org/kinetic/Installation/Ubuntu) on Ubuntu to install *ros-kinetic-desktop* package
2. Follow instructions [here](http://gazebosim.org/tutorials?tut=install_from_source&cat=install) to download modified source mentioned above, build and install SDFormat, gazebo. 
3. clone this repo including submodules
   * git clone --recursive https://github.com/michaelhuang14/quadevac_sim_ws
4. build menge
   * cd simons_project_ws/Menge/projects/g++
   * make debug
5. build all ros packages in this repo
   * cd simons_project_ws/catkin_ws
   * rosdep install --from-paths . --ignore-src --rosdistro kinetic
   * catkin_make
6. Setup environment
   * in catkin_ws dir: source devel/setup.bash && source env_setup.sh
7. Run the simulation
   * in same terminal as step 6: roslaunch menge_hector_quadroto_demo menge_quadrotor_rand.launch
8. control the quadcopter in simulation:
   * in separate terminal repeat step 6 to setup environment
   * then in the same terminal run one of following.
   - roslaunch hector_quadrotor_teleop logitech_gamepad.launch (you need plugin a logitech gamepad to use this)
   -  rosrun teleop_twist_keyboard teleop_twist_keyboard.py (see instructions [here](http://wiki.ros.org/teleop_twist_keyboard))

## Quick Guide on Creating Your Own Simulation
### Overview of Components
First of all, the simulation framework is made revolving around three different components. ROS, Gazebo, Menge.
Before using this tool it’s best to have at least basic knowledge of each of those components by following tutorials/instructions linked below:
* [ROS](http://wiki.ros.org/ROS/Tutorials)- Understand how workspaces setup, ROS nodes and communication framework, how to write ROS launch files.
* [Gazebo](http://gazebosim.org/tutorials) - Get familiar with user interface, how to write world file in SDF, how to write a world plugin. 
* [Menge](http://gamma.cs.unc.edu/Menge) - Read the paper to understand the xml format for scene and agent behavior specification.

### Creating a ROS Launch file

All launch files are stored inside the catkin_ws/src/menge_gazebo/menge_hector_quadrotor_demo/launch directory; two examples have already been included. The world launch files are located in the catkin_ws/src/menge_gazebo/menge_gazebo_worlds/launch/ directory.

### Creating a plugin (ROS package)

Create a ROS package with the name of your plugin. Copy and paste the contents of one of the example plugins in the workspace (maybe menge_plugin_randomized), and change naming and Cmake files appropriately. Then, add personalized code to the main plugin class (In the randomized plugin case, MengeRand.cpp). This plugin should handle inserting crowd agents and updating the crowd position and handing the quadcoper position to the Menge simulator, as well as any special instances like where one agent does not use and actor model but a simple cylinder.

### Building the scene

For now, obstacles and buildings need to be specified in both menge and gazebo xml files. The gazebo world files are located in catkin_ws/src/menge_gazebo/menge_gazebo_worlds/worlds, while obstacles are stored in the scene specification xml (nameS.xml) for each scenario in the Menge/examples/core/ directory. When creating the gazebo world file make sure you link the correct plugin library to the scene. For example, in the case of menge_rand.world, I have specified the library of menge_plugin_randomized in the world file.
All agent parameters such as personal space or mass or turning bias, as well as the quantity and spawn formation of crowds are specified in the scene xml files of each scenario, while crowd behavior, transitions, states, and goalsets are specified in the behavior xml files (nameB.xml) of each scenario. All of these xml files are located in the Menge/examples/core/ directory.

### Runnning the Simulation

Once all launch files, plugin code, menge specifications have been completed and built, roslaunch menge_hector_quadrotor_demo yourlaunchfile.launch in the catkin_ws directory.

