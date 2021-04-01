# The C++ Project Readme #

This is the readme for the C++ project.

For easy navigation throughout this document, here is an outline:

 - [Development environment setup](#development-environment-setup)
 - [Simulator walkthrough](#simulator-walkthrough)
 - [The tasks](#the-tasks)
 - [Evaluation](#evaluation)


## Development Environment Setup ##

Regardless of your development platform, the first step is to download or clone this repository.

Once you have the code for the simulator, you will need to install the necessary compiler and IDE necessary for running the simulator.

Here are the setup and install instructions for each of the recommended IDEs for each different OS options:

### Windows ###

For Windows, the recommended IDE is Visual Studio.  Here are the steps required for getting the project up and running using Visual Studio.

1. Download and install [Visual Studio](https://www.visualstudio.com/vs/community/)
2. Select *Open Project / Solution* and open `<simulator>/project/Simulator.sln`
3. From the *Project* menu, select the *Retarget solution* option and select the Windows SDK that is installed on your computer (this should have been installed when installing Visual Studio or upon opening of the project).
4. Make sure platform matches the flavor of Windows you are using (x86 or x64). The platform is visible next to the green play button in the Visual Studio toolbar:

![x64](x64.png)

5. To compile and run the project / simulator, simply click on the green play button at the top of the screen.  When you run the simulator, you should see a single quadcopter, falling down.


### OS X ###

For Mac OS X, the recommended IDE is XCode, which you can get via the App Store.

1. Download and install XCode from the App Store if you don't already have it installed.
2. Open the project from the `<simulator>/project` directory.
3. After opening project, you need to set the working directory:
  1. Go to *(Project Name)* | *Edit Scheme*
  2. In new window, under *Run/Debug* on left side, under the *Options* tab, set Working Directory to `$PROJECT_DIR` and check ‘use custom working directory’.
  3. Compile and run the project. You should see a single quadcopter, falling down.


### Linux ###

For Linux, the recommended IDE is QtCreator.

1. Download and install QtCreator.
2. Open the `.pro` file from the `<simulator>/project` directory.
3. Compile and run the project (using the tab `Build` select the `qmake` option.  You should see a single quadcopter, falling down.

**NOTE:** You may need to install the GLUT libs using `sudo apt-get install freeglut3-dev`


### Advanced Versions ###

These are some more advanced setup instructions for those of you who prefer to use a different IDE or build the code manually.  Note that these instructions do assume a certain level of familiarity with the approach and are not as detailed as the instructions above.

#### CLion IDE ####

For those of you who are using the CLion IDE for developement on your platform, we have included the necessary `CMakeLists.txt` file needed to build the simulation.

#### CMake on Linux ####

For those of you interested in doing manual builds using `cmake`, we have provided a `CMakeLists.txt` file with the necessary configuration.

**NOTE: This has only been tested on Ubuntu 16.04, however, these instructions should work for most linux versions.  Also note that these instructions assume knowledge of `cmake` and the required `cmake` dependencies are installed.**

1. Create a new directory for the build files:

```sh
cd FCND-Controls-CPP
mkdir build
```

2. Navigate to the build directory and run `cmake` and then compile and build the code:

```sh
cd build
cmake ..
make
```

3. You should now be able to run the simulator with `./CPPSim` and you should see a single quadcopter, falling down.

## Simulator Walkthrough ##

Now that you have all the code on your computer and the simulator running, let's walk through some of the elements of the code and the simulator itself.

### The Code ###

For the project, the majority of your code will be written in `src/QuadControl.cpp`.  This file contains all of the code for the controller that you will be developing.

All the configuration files for your controller and the vehicle are in the `config` directory.  For example, for all your control gains and other desired tuning parameters, there is a config file called `QuadControlParams.txt` set up for you.  An import note is that while the simulator is running, you can edit this file in real time and see the affects your changes have on the quad!

The syntax of the config files is as follows:

 - `[Quad]` begins a parameter namespace.  Any variable written afterwards becomes `Quad.<variablename>` in the source code.
 - If not in a namespace, you can also write `Quad.<variablename>` directly.
 - `[Quad1 : Quad]` means that the `Quad1` namespace is created with a copy of all the variables of `Quad`.  You can then overwrite those variables by specifying new values (e.g. `Quad1.Mass` to override the copied `Quad.Mass`).  This is convenient for having default values.

You will also be using the simulator to fly some difference trajectories to test out the performance of your C++ implementation of your controller. These trajectories, along with supporting code, are found in the `traj` directory of the repo.


### The Simulator ###

In the simulator window itself, you can right click the window to select between a set of different scenarios that are designed to test the different parts of your controller.

The simulation (including visualization) is implemented in a single thread.  This is so that you can safely breakpoint code at any point and debug, without affecting any part of the simulation.

Due to deterministic timing and careful control over how the pseudo-random number generators are initialized and used, the simulation should be exactly repeatable. This means that any simulation with the same configuration should be exactly identical when run repeatedly or on different machines.

Vehicles are created and graphs are reset whenever a scenario is loaded. When a scenario is reset (due to an end condition such as time or user pressing the ‘R’ key), the config files are all re-read and state of the simulation/vehicles/graphs is reset -- however the number/name of vehicles and displayed graphs are left untouched.

When the simulation is running, you can use the arrow keys on your keyboard to impact forces on your drone to see how your controller reacts to outside forces being applied.

#### Keyboard / Mouse Controls ####

There are a handful of keyboard / mouse commands to help with the simulator itself, including applying external forces on your drone to see how your controllers reacts!

 - Left drag - rotate
 - X + left drag - pan
 - Z + left drag - zoom
 - arrow keys - apply external force
 - C - clear all graphs
 - R - reset simulation
 - Space - pause simulation




### Testing it Out ###

When you run the simulator, you'll notice your quad is falling straight down.  This is due to the fact that the thrusts are simply being set to:

```
QuadControlParams.Mass * 9.81 / 4
```

Therefore, if the mass doesn't match the actual mass of the quad, it'll fall down.  Take a moment to tune the `Mass` parameter in `QuadControlParams.txt` to make the vehicle more or less stay in the same spot.

Note: if you want to come back to this later, this scenario is "1_Intro".

With the proper mass, your simulation should look a little like this:

<p align="center">
<img src="animations/scenario1.gif" width="500"/>
</p>


## The Tasks ##

For this project, we will be building a controller in C++. We will be implementing and tuning this controller in several steps.

we may find it helpful to consult the [Python controller code](https://github.com/udacity/FCND-Controls/blob/solution/controller.py) as a reference when we build out this controller in C++.

#### Notes on Parameter Tuning
1. **Comparison to Python**: Note that the vehicle we'll be controlling in this portion of the project has different parameters than the vehicle that's controlled by the Python code linked to above. **The tuning parameters that work for the Python controller will not work for this controller**

2. **Parameter Ranges**: we can find the vehicle's control parameters in a file called `QuadControlParams.txt`. The default values for these parameters are all too small by a factor of somewhere between about 2X and 4X. So if a parameter has a starting value of 12, it will likely have a value somewhere between 24 and 48 once it's properly tuned.

3. **Parameter Ratios**: In this [one-page document](https://www.overleaf.com/read/bgrkghpggnyc#/61023787/) we can find a derivation of the ratio of velocity proportional gain to position proportional gain for a critically damped double integrator system. The ratio of `kpV / kpP` should be 4.



## diagram of quadrotor contoller ##

![image](https://user-images.githubusercontent.com/34095574/113293695-63dd7500-92f6-11eb-9fdb-f8d35d345b78.png)






### Body rate and roll/pitch control (scenario 2) ###

First, we will implement the body rate and roll / pitch control.  For the simulation, we will use `Scenario 2`.  In this scenario, we will see a quad above the origin.  It is created with a small initial rotation speed about its roll axis.  The controller will need to stabilize the rotational motion and bring the vehicle back to level attitude.





#### 1- Implement body rate control ####

The commanded roll, pitch, and yaw are collected by the body rate controller, and they are translated into the desired moment along the axis in the body frame. This control method use only P controller.
 
 ```python
    def body_rate_control(self, body_rate_cmd, body_rate):
        """ Generate the roll, pitch, yaw moment commands in the body frame
        
        Args:
            body_rate_cmd: 3-element numpy array (p_cmd,q_cmd,r_cmd) in radians/second^2
            body_rate: 3-element numpy array (p,q,r) in radians/second^2
            
        Returns: 3-element numpy array, desired roll moment, pitch moment, and yaw moment commands in Newtons*meters
        """

        rate_err = body_rate_cmd - body_rate

        Kp_rate = np.array([self.Kp_p, self.Kp_q, self.Kp_r])

        m_c = MOI * np.multiply(Kp_rate, rate_err) 

        m_c_value = np.linalg.norm(m_c)

        if m_c_value > MAX_TORQUE:
            m_c = m_c*MAX_TORQUE/m_c_value
        return m_c
 
 ```
 
 - Tune `kpPQR` in `QuadControlParams.txt` to get the vehicle to stop spinning quickly but not overshoot

We see the rotation of the vehicle about roll (omega.x) get controlled to 0 while other rates remain zero. The vehicle will keep flying off quite quickly, since the angle is not yet being controlled back to 0.  Some overshoot will happen due to motor dynamics!.

If we come back to this step after the next step, we can try tuning just the body rate omega (without the outside angle controller) by setting `QuadControlParams.kpBank = 0`.





#### 2- Implement roll / pitch control ####

The roll-pitch controller is a P controller responsible for commanding the roll and pitch rates ( 𝑝𝑐  and  𝑞𝑐 ) in the body frame. First, it sets the desired rate of change of the given matrix elements using a P controller.

```python
   def roll_pitch_controller(self, acceleration_cmd, attitude, thrust_cmd):
        """ Generate the rollrate and pitchrate commands in the body frame
        
        Args:
            target_acceleration: 2-element numpy array (north_acceleration_cmd,east_acceleration_cmd) in m/s^2
            attitude: 3-element numpy array (roll, pitch, yaw) in radians
            thrust_cmd: vehicle thruts command in Newton
            
        Returns: 2-element numpy array, desired rollrate (p) and pitchrate (q) commands in radians/s
        """

        if thrust_cmd > 0 :

            c = -1 * thrust_cmd / DRONE_MASS_KG  

            # Find R13 (Target_X) and R23 (Target_Y)
            b_x_c_target , b_y_c_target  = np.clip(acceleration_cmd/c, -1, 1)  # min & max tilt (rad) 
             
            #Calculate Rotation Matrix
            rot_mat = euler2RM(attitude[0], attitude[1], attitude[2]) 

            b_x = rot_mat[0,2] # R13 (Actual)
            b_x_err = b_x_c_target - b_x
            b_x_p_term = self.Kp_roll * b_x_err

            b_y = rot_mat[1,2] # R23 (Actual)
            b_y_err = b_y_c_target - b_y
            b_y_p_term = self.Kp_pitch * b_y_err
            
            b_x_cmd_dot = b_x_p_term
            b_y_cmd_dot = b_y_p_term

            rot_mat1=np.array([[rot_mat[1,0],-rot_mat[0,0]],[rot_mat[1,1],-rot_mat[0,1]]])/rot_mat[2,2]
            rot_rate = np.matmul(rot_mat1,np.array([b_x_cmd_dot,b_y_cmd_dot]).T)
            
            p_c = rot_rate[0]
            q_c = rot_rate[1]

        else: 

            p_c = 0 
            q_c = 0
            thrust_cmd = 0

        return np.array([p_c, q_c])
 ```
 
 - Tune `kpBank` in `QuadControlParams.txt` to minimize settling time but avoid too much overshoot

We should now see the quad level itself (as shown below), though it’ll still be flying away slowly since we’re not controlling velocity/position!  We should also see the vehicle angle (Roll) get controlled to 0.

<p align="center">
<img src="animations/scenario2.gif" width="500"/>
</p>


### Position/velocity and yaw angle control (scenario 3) ###

Next, you will implement the position, altitude and yaw control for your quad.  For the simulation, you will use `Scenario 3`.  This will create 2 identical quads, one offset from its target point (but initialized with yaw = 0) and second offset from target point but yaw = 45 degrees.




#### 3- Altitude Control ( altitude_control() ) ####

 - implement the code in the function `AltitudeControl()`

```python
    def altitude_control(self, altitude_cmd, vertical_velocity_cmd, altitude, vertical_velocity, attitude,  acceleration_ff=0.0):
        """Generate vertical acceleration (thrust) command

        Args:
            altitude_cmd: desired vertical position (+up)
            vertical_velocity_cmd: desired vertical velocity (+up)
            altitude: vehicle vertical position (+up)
            vertical_velocity: vehicle vertical velocity (+up)
            attitude: the vehicle's current attitude, 3 element numpy array (roll, pitch, yaw) in radians
            acceleration_ff: feedforward acceleration command (+up)
            
        Returns: thrust command for the vehicle (+up)
        """
        
        alt_error = altitude_cmd - altitude

        p_term = self.Kp_alt * alt_error

        alt_dot_error = vertical_velocity_cmd - vertical_velocity

        d_term = self.Kd_alt * alt_dot_error

        acc_cmd = p_term + d_term + acceleration_ff

        b_z = np.cos(attitude[0]) * np.cos(attitude[1]) #  R33

        thrust = DRONE_MASS_KG * acc_cmd / b_z

        if thrust > MAX_THRUST:
            thrust = MAX_THRUST
        elif thrust < 0.0:
            thrust = 0.0
        return thrust



```
 - tune parameters `kpPosZ` and `kpPosZ`
 - tune parameters `kpVelXY` and `kpVelZ`

**Hint:**  For a second order system, such as the one for this quadcopter, the velocity gain (`kpVelXY` and `kpVelZ`) should be at least ~3-4 times greater than the respective position gain (`kpPosXY` and `kpPosZ`).

If successful, the quads should be going to their destination points and tracking error should be going down (as shown below). However, one quad remains rotated in yaw.

#### 4- Heading Control ( yaw_control() ) ####

A P controller is used to control the drone's yaw.

 ```python
 
     def yaw_control(self, yaw_cmd, yaw):
        """ Generate the target yawrate
        
        Args:
            yaw_cmd: desired vehicle yaw in radians
            yaw: vehicle yaw in radians
        
        Returns: target yawrate in radians/sec
        """
        yaw_err = yaw_cmd - yaw

        if yaw_err > np.pi:
            yaw_err = yaw_err - 2.0*np.pi
        elif yaw_err < -np.pi:
            yaw_err = yaw_err + 2.0*np.pi
         
        r_c = self.Kp_yaw * yaw_err
        
        # within range of 0 to 2*pi
        r_c = np.clip(r_c, 0, 2*np.pi) 

        return r_c
 
 
 
 ```
 - tune parameters `kpYaw` and the 3rd (z) component of `kpPQR`

#### 5- Lateral Position Control ( lateral_position_control() ) ####
 
 The drone generates lateral acceleration by changing the body orientation which results in non-zero thrust in the desired direction. A PD controller is used for the lateral controller.
 
 ```python
    def lateral_position_control(self, local_position_cmd, local_velocity_cmd, local_position, local_velocity,
                               acceleration_ff = np.array([0.0, 0.0])):
        """Generate horizontal acceleration commands for the vehicle in the local frame

        Args:
            local_position_cmd: desired 2D position in local frame [north, east]
            local_velocity_cmd: desired 2D velocity in local frame [north_velocity, east_velocity]
            local_position: vehicle position in the local frame [north, east]
            local_velocity: vehicle velocity in the local frame [north_velocity, east_velocity]
            acceleration_cmd: feedforward acceleration command
            
        Returns: desired vehicle 2D acceleration in the local frame [north, east]
        """

        lateral_pos_error = local_position_cmd - local_position

        p_term = self.Kp_lateral_pos * lateral_pos_error

        lateral_pos_dot_error = local_velocity_cmd - local_velocity

        d_term = self.Kd_lateral_pos * lateral_pos_dot_error

        acc_cmd = p_term + d_term + acceleration_ff


        return acc_cmd
        
```


Tune position control for settling time. Don’t try to tune yaw control too tightly, as yaw control requires a lot of control authority from a quadcopter and can really affect other degrees of freedom.  This is why you often see quadcopters with tilted motors, better yaw authority!

<p align="center">
<img src="animations/scenario3.gif" width="500"/>
</p>



### Non-idealities and robustness (scenario 4) ###

In this part, we will explore some of the non-idealities and robustness of a controller.  For this simulation, we will use `Scenario 4`.  This is a configuration with 3 quads that are all are trying to move one meter forward.  However, this time, these quads are all a bit different:
 - The green quad has its center of mass shifted back
 - The orange vehicle is an ideal quad
 - The red vehicle is heavier than usual

1. Run your controller & parameter set from Step 3.  Do all the quads seem to be moving OK?  If not, try to tweak the controller parameters to work for all 3 (tip: relax the controller).

2. Edit `AltitudeControl()` to add basic integral control to help with the different-mass vehicle.

3. Tune the integral control, and other control parameters until all the quads successfully move properly.  Your drones' motion should look like this:

<p align="center">
<img src="animations/scenario4.gif" width="500"/>
</p>


### Tracking trajectories ###

Now that we have all the working parts of a controller, you will put it all together and test it's performance once again on a trajectory.  For this simulation, you will use `Scenario 5`.  This scenario has two quadcopters:
 - the orange one is following `traj/FigureEight.txt`
 - the other one is following `traj/FigureEightFF.txt` - for now this is the same trajectory.  For those interested in seeing how you might be able to improve the performance of your drone by adjusting how the trajectory is defined, check out **Extra Challenge 1** below!

How well is your drone able to follow the trajectory?  It is able to hold to the path fairly well?


### Extra Challenge 1 (Optional) ###

You will notice that initially these two trajectories are the same. Let's work on improving some performance of the trajectory itself.

1. Inspect the python script `traj/MakePeriodicTrajectory.py`.  Can you figure out a way to generate a trajectory that has velocity (not just position) information?

2. Generate a new `FigureEightFF.txt` that has velocity terms
Did the velocity-specified trajectory make a difference? Why?

With the two different trajectories, your drones' motions should look like this:

<p align="center">
<img src="animations/scenario5.gif" width="500"/>
</p>


### Extra Challenge 2 (Optional) ###

For flying a trajectory, is there a way to provide even more information for even better tracking?

How about trying to fly this trajectory as quickly as possible (but within following threshold)!




## Implementation in C++ ##

Methods in QuadControl.cpp is given below



- This part is implemented in QuadControl::GenerateMotorCommands:

In this function , the individual motor thrust commands is set.The drone rotor positions are swapped in this project versus the 3D lesson [3] The rotor positions are given below

```cpp
VehicleCommand QuadControl::GenerateMotorCommands(float collThrustCmd, V3F momentCmd)
{
  // Convert a desired 3-axis moment and collective thrust command to 
  //   individual motor thrust commands
  // INPUTS: 
  //   desCollectiveThrust: desired collective thrust [N]
  //   desMoment: desired rotation moment about each axis [N m]
  // OUTPUT:
  //   set class member variable cmd (class variable for graphing) where
  //   cmd.desiredThrustsN[0..3]: motor commands, in [N]

  // HINTS: 
  // - you can access parts of desMoment via e.g. desMoment.x
  // You'll need the arm length parameter L, and the drag/thrust ratio kappa

  ////////////////////////////// BEGIN CODE ///////////////////////////

//  cmd.desiredThrustsN[0] = mass * 9.81f / 4.f; // front left
//  cmd.desiredThrustsN[1] = mass * 9.81f / 4.f; // front right
//  cmd.desiredThrustsN[2] = mass * 9.81f / 4.f; // rear left
//  cmd.desiredThrustsN[3] = mass * 9.81f / 4.f; // rear right
    float l = L / sqrtf(2.f);
    float c_bar = collThrustCmd;  // F1 + F2 + F3 + F4
    float p_bar = momentCmd.x / l; // F1 - F2 + F3 - F4
    float q_bar = momentCmd.y / l;  // F1 + F2 - F3 - F4
    float r_bar = -momentCmd.z / kappa; // F1 - F2 - F3 + F4
    
    cmd.desiredThrustsN[0] = (c_bar + p_bar + q_bar + r_bar) / 4.f; // front left
    cmd.desiredThrustsN[1] = (c_bar - p_bar + q_bar - r_bar) / 4.f; // front right
    cmd.desiredThrustsN[2] = (c_bar + p_bar - q_bar - r_bar) / 4.f; // rear left
    cmd.desiredThrustsN[3] = (c_bar - p_bar - q_bar + r_bar) / 4.f; // rear right
  
  /////////////////////////////// END CODE ////////////////////////////

  return cmd;
}

```


- This part is implemented in QuadControl::BodyRateControl:

The commanded roll, pitch, and yaw are collected by the body rate controller, and they are translated into the desired moment along the axis in the body frame. This control method use only P controller.

```cpp
V3F QuadControl::BodyRateControl(V3F pqrCmd, V3F pqr)
{
  // Calculate a desired 3-axis moment given a desired and current body rate
  // INPUTS: 
  //   pqrCmd: desired body rates [rad/s]
  //   pqr: current or estimated body rates [rad/s]
  // OUTPUT:
  //   return a V3F containing the desired moments for each of the 3 axes

  // HINTS: 
  //  - you can use V3Fs just like scalars: V3F a(1,1,1), b(2,3,4), c; c=a-b;
  //  - you'll need parameters for moments of inertia Ixx, Iyy, Izz
  //  - you'll also need the gain parameter kpPQR (it's a V3F)

  V3F momentCmd;

  ////////////////////////////// BEGIN CODE ///////////////////////////
  V3F MOI = V3F(Ixx, Iyy, Izz);
  
  momentCmd = MOI * kpPQR * ( pqrCmd - pqr );

  /////////////////////////////// END CODE ////////////////////////////

  return momentCmd;
```

- This part is implemented in QuadControl::RollPitchControl:

The roll-pitch controller is a P controller responsible for commanding the roll and pitch rates ( pqrCmd.x and pqrCmd.y) in the body frame.

```cpp
V3F QuadControl::RollPitchControl(V3F accelCmd, Quaternion<float> attitude, float collThrustCmd)
{
  // Calculate a desired pitch and roll angle rates based on a desired global
  //   lateral acceleration, the current attitude of the quad, and desired
  //   collective thrust command
  // INPUTS: 
  //   accelCmd: desired acceleration in global XY coordinates [m/s2]
  //   attitude: current or estimated attitude of the vehicle
  //   collThrustCmd: desired collective thrust of the quad [N]
  // OUTPUT:
  //   return a V3F containing the desired pitch and roll rates. The Z
  //     element of the V3F should be left at its default value (0)

  // HINTS: 
  //  - we already provide rotation matrix R: to get element R[1,2] (python) use R(1,2) (C++)
  //  - you'll need the roll/pitch gain kpBank
  //  - collThrustCmd is a force in Newtons! You'll likely want to convert it to acceleration first

  V3F pqrCmd;
  Mat3x3F R = attitude.RotationMatrix_IwrtB();

  ////////////////////////////// BEGIN CODE ///////////////////////////
  if ( collThrustCmd > 0 ) {
    float c = - collThrustCmd / mass;
    float b_x_cmd = CONSTRAIN(accelCmd.x / c, -maxTiltAngle, maxTiltAngle);
    float b_x_err = b_x_cmd - R(0,2);
    float b_x_p_term = kpBank * b_x_err;
    
    float b_y_cmd = CONSTRAIN(accelCmd.y / c, -maxTiltAngle, maxTiltAngle);
    float b_y_err = b_y_cmd - R(1,2);
    float b_y_p_term = kpBank * b_y_err;
    
    pqrCmd.x = (R(1,0) * b_x_p_term - R(0,0) * b_y_p_term) / R(2,2);
    pqrCmd.y = (R(1,1) * b_x_p_term - R(0,1) * b_y_p_term) / R(2,2);
  } else {
    pqrCmd.x = 0.0;
    pqrCmd.y = 0.0;
  }
  
  pqrCmd.z = 0;
  /////////////////////////////// END CODE ////////////////////////////

  return pqrCmd;
}
```

- This part is implemented in QuadControl::AltitudeControl:

A PID controller is used for the altitude control [6] Unlike the Python part, the integral term is added and acceleration is limited.

```cpp
float QuadControl::AltitudeControl(float posZCmd, float velZCmd, float posZ, float velZ, Quaternion<float> attitude, float accelZCmd, float dt)
{
  // Calculate desired quad thrust based on altitude setpoint, actual altitude,
  //   vertical velocity setpoint, actual vertical velocity, and a vertical 
  //   acceleration feed-forward command
  // INPUTS: 
  //   posZCmd, velZCmd: desired vertical position and velocity in NED [m]
  //   posZ, velZ: current vertical position and velocity in NED [m]
  //   accelZCmd: feed-forward vertical acceleration in NED [m/s2]
  //   dt: the time step of the measurements [seconds]
  // OUTPUT:
  //   return a collective thrust command in [N]

  // HINTS: 
  //  - we already provide rotation matrix R: to get element R[1,2] (python) use R(1,2) (C++)
  //  - you'll need the gain parameters kpPosZ and kpVelZ
  //  - maxAscentRate and maxDescentRate are maximum vertical speeds. Note they're both >=0!
  //  - make sure to return a force, not an acceleration
  //  - remember that for an upright quad in NED, thrust should be HIGHER if the desired Z acceleration is LOWER

  Mat3x3F R = attitude.RotationMatrix_IwrtB();
  float thrust = 0;

  ////////////////////////////// BEGIN CODE ///////////////////////////
  float z_err = posZCmd - posZ;
  float p_term = kpPosZ * z_err;
  
  float z_dot_err = velZCmd - velZ;
  integratedAltitudeError += z_err * dt;

  
  float d_term = kpVelZ * z_dot_err + velZ;
  float i_term = KiPosZ * integratedAltitudeError;
  float b_z = R(2,2);

  float u_1_bar = p_term + d_term + i_term + accelZCmd;

  float acc = ( u_1_bar - CONST_GRAVITY ) / b_z;

  thrust = - mass * CONSTRAIN(acc, - maxAscentRate / dt, maxAscentRate / dt);
  /////////////////////////////// END CODE ////////////////////////////
  
  return thrust;
}

```


- This part is implemented in QuadControl::LateralPositionControl:

The drone generates lateral acceleration by changing the body orientation which results in non-zero thrust in the desired direction. A PD controller is used for the lateral controller.

```cpp
V3F QuadControl::LateralPositionControl(V3F posCmd, V3F velCmd, V3F pos, V3F vel, V3F accelCmd)
{
  // Calculate a desired horizontal acceleration based on 
  //  desired lateral position/velocity/acceleration and current pose
  // INPUTS: 
  //   posCmd: desired position, in NED [m]
  //   velCmd: desired velocity, in NED [m/s]
  //   pos: current position, NED [m]
  //   vel: current velocity, NED [m/s]
  //   accelCmd: desired acceleration, NED [m/s2]
  // OUTPUT:
  //   return a V3F with desired horizontal accelerations. 
  //     the Z component should be 0
  // HINTS: 
  //  - use fmodf(foo,b) to constrain float foo to range [0,b]
  //  - use the gain parameters kpPosXY and kpVelXY
  //  - make sure you cap the horizontal velocity and acceleration
  //    to maxSpeedXY and maxAccelXY

  // make sure we don't have any incoming z-component
  accelCmd.z = 0;
  velCmd.z = 0;
  posCmd.z = pos.z;

  ////////////////////////////// BEGIN CODE ///////////////////////////
  
   if (velCmd.mag() > maxSpeedXY) {
      velCmd = velCmd.norm() * maxSpeedXY;
    }
   

  accelCmd = kpPosXY * (posCmd - pos) + kpVelXY * (velCmd - vel) + accelCmd ;

    if (accelCmd.mag() > maxAccelXY) {
      accelCmd = accelCmd.norm() * maxAccelXY;
    }

  /////////////////////////////// END CODE ////////////////////////////

  return accelCmd;
}

```
- This part is implemented in QuadControl::YawControl:

A P controller is used to control the drone's yaw. This controller returns desired yaw rate.

```cpp
float QuadControl::YawControl(float yawCmd, float yaw)
{
  // Calculate a desired yaw rate to control yaw to yawCmd
  // INPUTS: 
  //   yawCmd: commanded yaw [rad]
  //   yaw: current yaw [rad]
  // OUTPUT:
  //   return a desired yaw rate [rad/s]
  // HINTS: 
  //  - use fmodf(foo,b) to constrain float foo to range [0,b]
  //  - use the yaw control gain parameter kpYaw

  float yawRateCmd=0;
  ////////////////////////////// BEGIN CODE ///////////////////////////
  
  float yaw_cmd_2_pi = 0;
  if ( yawCmd > 0 ) {
    yaw_cmd_2_pi = fmodf(yawCmd, 2 * F_PI);
  } else {
    yaw_cmd_2_pi = -fmodf(-yawCmd, 2 * F_PI);
  }
  float err = yaw_cmd_2_pi - yaw;
  if ( err > F_PI ) {
    err -= 2 * F_PI;
  } if ( err < -F_PI ) {
    err += 2 * F_PI;
  }
  yawRateCmd = kpYaw * err;
  /////////////////////////////// END CODE ////////////////////////////

  return yawRateCmd;

}


```








## Evaluation ##

To assist with tuning of your controller, the simulator contains real time performance evaluation.  We have defined a set of performance metrics for each of the scenarios that your controllers must meet for a successful submission.

There are two ways to view the output of the evaluation:

 - in the command line, at the end of each simulation loop, a **PASS** or a **FAIL** for each metric being evaluated in that simulation
 - on the plots, once your quad meets the metrics, you will see a green box appear on the plot notifying you of a **PASS**


### Performance Metrics ###

The specific performance metrics are as follows:

 - scenario 2
   - roll should less than 0.025 radian of nominal for 0.75 seconds (3/4 of the duration of the loop)
   - roll rate should less than 2.5 radian/sec for 0.75 seconds

 - scenario 3
   - X position of both drones should be within 0.1 meters of the target for at least 1.25 seconds
   - Quad2 yaw should be within 0.1 of the target for at least 1 second


 - scenario 4
   - position error for all 3 quads should be less than 0.1 meters for at least 1.5 seconds

 - scenario 5
   - position error of the quad should be less than 0.25 meters for at least 3 seconds

## Authors ##

Thanks to Fotokite for the initial development of the project code and simulator.
