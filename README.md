# Intern Challenge

## The following python packages are required to run this:

- roboticstoolbox
- numpy
- matplotlib

This was run on an Anaconda environment running python 3.11.11

## Project structure
```css
intern-challenge/
├── RRR.ipynb
├── sim.ipynb
├── rrr_robot_animation.gif
├── rrr_robot_animationA.gif
├── rrr_robot_animationB.gif
└── README.md
``` 

### Task A animations:
- `rrr_robot_animationA.gif` : original gif for task A
- `rrr_robot_animation.gif` : slightly sped up version of the task A gif

### Task B animations:
- `rrr_robot_animationB.gif` : original gif for task B

## Running the Code:
`sim.ipynb` shows the target trajectories for x and y over time for both tasks  
`RRR.ipynb` has the code for calculating the robot control and making the animations

The first cell of `RRR.ipynb` is running calculation for Task A and the second cell is the animation for task A.

The third cell of `RRR.ipynb` is running calculation for Task B and the fourth cell is the animation for task B.

## Explanation

For both tasks I am implementaing PD control for a manipulator, tracking the target trajectory, which is given at a rate of 30 Hz for task A or 5 Hz for task B. The simulation lasts for 2 seconds.

The system considers the robot to have link lengths L, with their masses at the end of the link, and the inertia for each joint being ignored. Gravity is assumed to be in pointing in the negative y direction. Each link is also limited between -135 to 135 degrees of rotation.

The inital joint angles of the robot is set to (0, 0, 0), resulting in the end effector postion being (3L, 0)

The time step of the loop is defined by the control rate which is 1000 Hz for task A and 50 Hz for task B, resulting in task B having much fewer points.

The target is updated at the `trajectory_freq` within the main time loop. Everytime the target updates, a new trapezoidal trajectory is planned from the robot's current position to the new target position.

At each time step, the control loop is updated. The PD controller is given by:  
![Equation](https://latex.codecogs.com/png.image?\dpi{110}\color{white}f=-K_d\dot{q}-K_p(q-q_{des}))  
Since the acceleration in the trajectory is discontinuous, it is set to 0 in the controller. Similarly q dot desired is also set to 0.

Where ![Kd](https://latex.codecogs.com/png.image?\dpi{110}\color{white}K_d) is equal to ![2sqrtKp](https://latex.codecogs.com/png.image?\dpi{110}\color{white}2\sqrt{K_p}) for critical damping

Then the torques are calculated using the following equation:  
![2sqrtKp](https://latex.codecogs.com/png.image?\dpi{110}\color{white}\tau=M(q)f+V(q,\dot{q})\dot{q}+G(q))   
Where M is the mass matrix, V is the coriolis/centripetal matrix, and G is the gravity load matrix.

The joint acceleration due to this torque is calculated. This is then integrated to find the joint velocities and positions.

The end effector position, target position and planned trajectory is plotted for x and y against time. The joint angles are also plotted against time.

For task A a very high Kp of 80000 was chosen. This allowed the robot to track the trajectory relatively well, with a slight delay, due to the sampling rate of the target. Smaller Kp values resulted in more delay in tracking and lower amplitudes of oscillation.

For task B, since the sampling frequency is 5 Hz, it always samples the y position to be zero as the frequency of the sine wave is also 5 Hz. This means over the two seconds, the target position is always (2L, 0). A much smaller Kp of 1000 was chosen. This shows how important the sampling frequency of the target is, as the low sampling frequency in task B completely mischaracterises the target trajectory.
