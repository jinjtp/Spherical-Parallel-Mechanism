# Publications

This project is based on the following publication, which provided the
kinematic structure and analysis method used to develop the prototype:

> [1]A. Arian, B. Danaei, and M. T. Masouleh, “Kinematics and Dynamics Analysis of a 2-DOF
> Spherical Parallel Robot,” in 2016 4th International Conference on Robotics and Mechatronics (ICROM),
> IEEE, Oct. 2016, pp. 154–159. doi: 10.1109/icrom.2016.7886838.

**How it is used in this project**
- The 2-DOF spherical parallel mechanism (5 revolute joints, all axes meeting
  at one centre point) was adopted as the basis for the pan-tilt solar panel.
- The kinematic approach in the paper guided the symbolic MATLAB model
  (DOF, pan/tilt from the input angles, and Jacobian).

**What differs from the paper**
- Link dimensions and geometry were redesigned in Fusion 360 for a 3D-printed prototype.
- The inputs are manual knobs instead of motors.

All credit for the original mechanism analysis goes to the authors.
Any errors in this implementation are our own.
