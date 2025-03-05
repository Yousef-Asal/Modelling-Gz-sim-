# Modelling

In this documentation we will learn about modelling and how can we build a robot and a world to test our system in.

First of all we need to choose a simulator to build our environment in. There are several simulators that support ROS2 that we can use each one has its pros and cons and is suitable for certain applications.

Secondly we will need to learn about the format or the language that we will use to write the robot description or if there is a tool to help us in this regard.

Lastly is controlling the robot that we had made in this simulator and validating the output that we see in a certain way.

So after we have defined what are the various aspects of the modelling task let's list the steps that we are going to take to achieve this task:

1. What is simulations and why it's important?
2. Simulators comparison and use cases.
3. GZ Sim
4. GZ Sim customizability.
5. SDF format.
6. Robot building structure.
7. SDF components.
8. World design.
9. Importing models.
10. GZ Sim topics.
11. ROS2 bridge.

We will go through this list one by one till we fully understand how the simulation works and integrate our system in it.

---

### What is simulations and why it's important?

Simulation refers to using virtual environment that looks and acts as the real world to test and analyze our systems before and after deployment. In robotics and autonomous applications simulation plays a crusial rule in the different phases of development.

Let's imagine a scenario where we are building a robot that navigates in a home and it's designed to get to a goal location that we are going to input without any prior knowledge in the environment.

before we start building the robot in the real world firstly we have to design the robot and test it in the simulation and fix any arising errors this type of simulation is called dynamic simulation as we simulate the real-workd physics such as gravity and friction. and after we make sure that our robot is working fine and the system is performing as expected we will need to simulate the sensors that will be mounted on our robot and make sure they are compatable. Even after building the robot in the real world simulation still plays a very important role in improving the robot and adding extra features.

So to sum up, simulation in robotics provides us with a cost effective solutions to develop and test our system before deployment also we can simulate difficult and inaccessible environments the we as humans can't reach. so overall simulation is a very important step in development. so let's take a deep dive in our first stops of the simulation train.

---

### Simulators comparison and use cases

Now let's take a look at the different simlators and their use cases.

| **Simulator**           | **Best For**                      | **Physics Engine**   | **Sensor Simulation**   | **ROS Support** | **AI & ML Integration** | **Real-Time Capable** | **GPU Acceleration** | **Ease of Use** |
| :---------------------------- | --------------------------------------- | :------------------------- | ----------------------------- | --------------------- | ----------------------------- | --------------------------- | -------------------------- | --------------------- |
| **Gazebo (Ignition)**   | General robotics, ROS-based robots      | ODE, Bullet, DART, Simbody | ✅ (LIDAR, cameras, IMU)      | ✅ (ROS 1 & 2)        | ⚠️ Limited                  | ✅                          | ⚠️ Limited               | Medium                |
| **Webots**              | Educational & industrial robotics       | ODE                        | ✅ (Cameras, GPS, IMU, LIDAR) | ✅ (ROS 1 & 2)        | ⚠️ Limited                  | ✅                          | ❌                         | High                  |
| **CoppeliaSim (V-REP)** | Robotic manipulators, industrial robots | Bullet, ODE, Vortex        | ✅                            | ✅ (ROS 1 & 2)        | ✅ (Reinforcement Learning)   | ✅                          | ❌                         | Medium                |
| **CARLA**               | Autonomous vehicles, ADAS               | Unreal Engine physics      | ✅ (LIDAR, cameras, radar)    | ✅ (ROS Bridge)       | ✅ (Deep Learning)            | ⚠️ Partial                | ✅ (CUDA, RTX)             | Medium                |
| **LGSVL Simulator**     | Autonomous driving, ADAS                | Unity physics              | ✅ (LIDAR, cameras, radar)    | ✅ (Apollo, ROS)      | ✅ (AI Training)              | ✅                          | ✅ (NVIDIA GPU)            | Medium                |
| **NVIDIA Isaac Sim**    | Robotics AI, digital twins              | PhysX                      | ✅ (High-fidelity sensors)    | ✅ (ROS 1 & 2)        | ✅ (Deep Learning, RL)        | ✅                          | ✅ (RTX, Omniverse)        | Low (Complex)         |
| **PyBullet**            | AI, Reinforcement Learning (RL)         | Bullet                     | ❌                            | ❌                    | ✅ (RL & Physics-based AI)    | ✅                          | ✅ (GPU Support)           | Medium                |
| **MuJoCo**              | Biomechanics, AI research               | Custom physics             | ❌                            | ❌                    | ✅ (RL & AI Training)         | ✅                          | ✅ (GPU for acceleration)  | Medium                |
| **AirSim**              | Drones, autonomous flight               | Unreal Engine physics      | ✅ (Depth, LIDAR, GPS, IMU)   | ✅ (ROS Bridge)       | ✅ (Deep Learning, RL)        | ✅                          | ✅ (CUDA, RTX)             | Medium                |

As we can see from this table there are a lot of simulators and there are more that were not included each one excells in some aspects making it suitble for certain applications.

Since we are using ROS2 and we are looking for a simulator for general purpose tasks we will use gazebo which is refered to as (gz sim).

---

### GZ Sim

GZ Sim formerly known as gazebo is an open-source simulation tool for robotics and autonomous systems. it provides us with:

* physics simulation
* sensor modeling
* ROS integration

Gazebo is one of the most if not the most used simulators with ROS. Previously when you install ROS1 gazebo is installed with it by default so they are a good match to use them together.

Gazebo was developed in 2002 by Andrew Howard and was initially just a 2d physics engine but as the years went by it became an open source 3d simulator.

Gazebo officially transitioned to ignition gazebo in 2022 and it was renamed to GZ Sim.

Since we have decided to continue with gazebo the first step is installing the program on our machine.

#### Installation

Before we install gazebo we should choose the right version that fits our ROS2 version. here is the table for the different versions:


|                              | **GZ Citadel (LTS)** | **GZ Fortress (LTS)** | **GZ Harmonic (LTS)** | **Gz Ionic** |
| ---------------------------- | -------------------------- | --------------------------- | --------------------------- | ------------------ |
| **ROS 2 Rolling**      | ❌                         | ❌                          | ⚡                          | ✅                 |
| **ROS 2 Jazzy (LTS)**  | ❌                         | ❌                          | ✅                          | ❌                 |
| **ROS 2 Humble (LTS)** | ❌                         | ✅                          | ⚡                          | ❌                 |
| **ROS 1 Noetic (LTS)** | ✅                         | ⚡                          | ❌                          | ❌                 |

* ✅ - Recommended combination
* ❌ - Incompatible / not possible.
* ⚡ - Possible,  *but use with caution* . These combinations of ROS and Gazebo can be made to work together, but some effort is required.

Since we are using ROS2 Humble we are going to download GZ Fortress. Follow these commands to install it:

```bash
sudo apt-get update

sudo apt-get install lsb-release gnupg

sudo curl https://packages.osrfoundation.org/gazebo.gpg --output /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null

sudo apt-get update

sudo apt-get install ignition-fortress
```

After you run these commands GZ Fortress should be installed and you can find it in your apps as `ign gazebo`

#### Getting started with gazebo
