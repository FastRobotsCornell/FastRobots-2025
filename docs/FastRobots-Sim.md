# Fast Robots @ Cornell

[Return to main page](./index.md)

# Simulation tool used for Labs 10-12

## Objective
Follow these directions to setup and use your simulation environment. You will learn how to control your virtual robot and use the live plotting tool.

## Simulation Environment
The simulation environment consists of three components:

### 1. Simulator
The simulator contains a wheeled robot equipped with laser range finder(s), similar to the physical robot and its ToF sensors. The simulated world is defined in a .yaml configuration file.

#### Odometry
*Odometry* is the use of data from onboard sensors to estimate change in position over time. The relative changes recorded by the sensors (typically IMU and/or wheel encoders) are integrated over time to get a pose estimate of the robot. It is used in robotics by mobile robots to estimate their position relative to a starting location. This method is sensitive to errors due to the integration of velocity measurements over time to give position estimates.

The virtual robot provides simulated IMU data that mimics a typical real robot and this data is integrated over time to give you the odometry pose estimate. (In your real robot, we know that accelerometer data is too noisy to estimate forward motion, but you can consider using the gyroscope to estimate turns, and doing forward motion open loop. More on this in future labs!)

#### Ground Truth
In robotics, *ground truth* is the most accurate measurement available. New methods, algorithms and sensors are often quantified by comparing them to a ground truth measurement. Ground truth can come either directly from a simulator, or from a much more accurate (and expensive) sensor.

For the virtual robot, ground truth is the exact position of your virtual robot within the simulator.

#### Virtual Robot
The virtual robot has a (simulated) constant speed controller and odometry pose estimation built in. It essentially mimics an idealized version of the real robot.

### 2. Plotter
The 2D plotting tool is a lightweight process that allows for live asynchronous plotting of multiple scatter plots using Python. The Python API to plot points is described in the Jupyter notebook. It allows you to plot the odometry and ground truth poses. It also allows you to plot the map (as line segments) and robot belief in future labs. Play around with the various GUI buttons to familiarize yourself with the tool; you want to be more familiar with the tools before you get into the future labs. 

### 3. Controller
You will be programming the controller in Python to perform various functions on your virtual robot. We provide you with a Python API which, among other things, provides a minimal control interface for the robot in the simulator. It allows you to:
  - Get robot odometry pose
  - Get the sensor data
  - Move the robot


## Computer Setup
<!--### WSL
WSL users should continue to use WSL (for Bluetooth reliability) and should follow the
Linux instructions when applicable. The provided WSL distribution comes with Python 3.9
and works without upgrading to a later Python version.

WSL users may have to install some packages in the WSL terminal.
```
sudo apt install libgl1 libegl1 libxkbcommon-x11-0 libxcb-xinerama0 libxcb-cursor0 libxcb-icccm4 libxcb-keysyms1 libxcb-shape0
```

### Upgrade Python
This is a reminder to upgrade python (3.10 for Windows/Mac, 3.9 for Linux/WSL) and pip (>= 21.0).
> NOTE: You may notice different versions when using `python` and `python3` commands; use the one that has the correct version and use pip in that version of python. For example, if `python3` has the latest version of python on your computer, use pip as `python3 -m pip`. Make sure you use the right python command for all the steps detailed in this documentation.

<details>
  <summary><strong>How to check for versions?</strong></summary>
    In a Command Line Interface (CLI), run the below commands to check for Python and pip versions:
    <div class="language-plaintext highlighter-rouge"><div class="highlight"><pre class="highlight"><code>python --version<br>python -m pip --version</code></pre></div></div>
</details><br>

<ol>
  <li>Install/Upgrade <a href="https://www.python.org/downloads/">Python 3.10</a></li>
  <li>Install/Upgrade <a href="https://pip.pypa.io/en/stable/installation/#upgrading-pip">pip</a></li>
</ol>

#### Install Python Dependencies
If you just upgraded your Python version and used an older version in Lab1B, then the virtual environment created in Lab 1B will no longer be compatible. Inside your project folder, delete the directory with the name of your virtual environment (**FastRobots_ble**). Verify it is actually deleted! Now, follow instructions in [Lab1](Lab1.md) to setup your new virtual environment and reinstall the packages from Lab 1B. -->

### Install pip packages
1. Activate your virtual environment.
2. Install dependencies
  ```bash
  python -m pip install numpy pygame pyqt6 pyqtgraph pyyaml ipywidgets colorama
  ```
  > Replace `python` with `python3` if `python3` points to the latest version.
3. Try 
```python -m tkinter```
to make sure that tkinter is set up correctly. If the Tkinter module isn't
installed, run
```pip install tk```
and try
```python -m tkinter```
again.

### Install Box2D package
#### Installing from a pip wheel
1. Navigate to your project directory and activate your virtual environment.
2. Download the [pip wheel](https://pypi.org/project/Box2D/#files) and place it in your project directory. The wheel file needs to match your OS and Python version. For example, MacOS (ARM) with Python 3.10 would download the wheel with ```cp310-macosx_11_0_arm64```.

>Note: The python version must match the wheel exactly (newer versions don't work) or else you will get a message of the type: ERROR:&lt;wheel file&gt; is not a supported wheel on this platform.

>Another Note: If you have multiple version of python, you should call the specific version using the command "py -3.x" (windows) or "python3.x" (Mac/Linux) instead of "python3. So calling python 3.10 is now "py -3.10".

3. Install <b>Box2D</b> from pip
  > Replace &lt;wheel file&gt; with the name of the wheel file in your project directory.
  ```bash
  pip install <wheel file>
  ```
  Remember that WSL can access your Windows ```C:\``` drive
  as ```/mnt/c```.

4. If Box2D is installed successfully, start a python interpreter:
  ```bash
  python
  ```
  and then run the following code in the python interpreter:
  ```python
  import Box2D; print(Box2D.__version__)
  ```
5. If the above command displays "2.3.10", you are done! Skip the next section ("Installing from source").

#### Installing from source
Follow the instructions [here](https://github.com/pybox2d/pybox2d/blob/master/INSTALL.md) **IF AND ONLY IF** Box2D was NOT successfully installed in the previous section.

## Instructions
1. Download and extract the [simulation base code](https://github.com/FastRobotsCornell/FastRobots-sim-release) into your project directory.
2. Open the *inClassDemo.ipynb* file in the **notebooks** directory.
3. Follow the instructions in the notebook to understand how to control your virtual robot and use the plotter.

## Tasks (We will do these as an in-class exercise)

1. Open Loop Control 
  - Make your robot follow a set of velocity commands to execute a "square" loop anywhere in the map.
  - Plot and analyze the ground truth and odometry of the robot.
  - What is the duration of a velocity command?
  - Does the robot always execute the exact same shape?
   
2. Closed Loop Control
    - Design a simple controller in your Jupyter notebook to perform a closed-loop obstacle avoidance.
    - By how much should the virtual robot turn when it is close to an obstacle?
    - At what linear speed should the virtual robot move to minimize/prevent collisions? Can you make it go faster?
    - How close can the virtual robot get to an obstacle without colliding?
    - Does your obstacle avoidance code always work? If not, what can you do to minimize crashes or (may be) prevent them completely?


