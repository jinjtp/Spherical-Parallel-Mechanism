# 2-DOF Spherical Parallel Mechanism

Term Project 2569 · 2103-322 Design of Mechanism

| Path | Purpose |
| --- | --- |
| `hardware` | CAD models |
| `simulation/Simscape Multibody` | Simscape Mulyibody files |
| `simulation/Symbolic Mathematics` | Symbolic Math scripts |
| `media/` | Images, GIFs, and links to demonstration videos |
| `publications/` | Project-related journal and conference papers |

## Project overview

- **Project name** — 2-DOF Spherical Parallel Mechanism for Pan–Tilt Motion
- **Problem / purpose**

  Because the sun’s position changes throughout the day and across seasons, a solar panel fixed at one tilt angle receives sunlight at a certain angle, which reduces the energy it collects. A panel that can rotate about both a pan and a tilt axis can keep facing the sun and generate more energy. The purpose of this project is to model, simulate and build a prototype of a 2-DOF spherical parallel mechanism that orients a solar panel in pan and tilt, keeping both inputs on the fixed base. A symbolic kinematic model is used to predict the panel’s orientation from the two input angles and to check its range of motion and singular positions, providing a basis for a future sun-tracking system.
- **Team**: ริ๋มตั๊ก
  - Jinjuta Peecharsoontorn 6730069121
  - Pavida Mayudee 6730413321
  - Puntira Chatthammarak 6730400121
- **Status** — Finish
- **Last updated** — 2026-11-20

## Motivation

For this project, we had to choose a mechanism and explain how its input motion produces its output motion. We chose a pan–tilt mechanism and used a solar panel as an example. During the day, the sun changes both its direction along the horizon and its height above it. To keep a panel facing the sun, we need to turn it side to side (pan) and angle it up or down (tilt).

What we wanted to find out was how much the platform pans and tilts when we turn the two input joints, J1 and J3. We used kinematic equations to relate the input angles to the platform angles, then built a Simscape Multibody model to see how the links move together.

## Symbolic Math
#### Requirements
- MATLAB (developed in R2026a; R2016b or newer should work)
- Symbolic Math Toolbox
  
#### How to run
1. Open [Symbolic_Math_v6_1.mlx](docs/Symbolic_Math_v6_1.mlx) in MATLAB.
2. Adjust the settings in section 0 if needed.
3. Click Run All.

The live script is organised in sections:

| Section | Content |
| --- | --- |
| 0 | Settings |
| 1 | Parameters |
| 2 | Mechanism details and DOF calculation |
| 3 | Symbolic kinematics |
| 4 | Symbolic Jacobian, velocity and acceleration |
| 5 | Summary table |
| 6 | Graphs |
| 7 | Animation |

Comments were provided inside the script for better understanding of its function.
#### Symbolic Math overview
The panel is held by two kinematic chains (legs) that both start on the fixed base. Two input angles are applied at the base:
- θ1 at joint J1, which pans the panel
- θ2 at joint J3, which tilts the panel through the second leg
- The two legs are:
  - Leg A: Base → J1 → Link2 (half ring) → J2 → Platform
  - Leg B: Base → J3 → Link4+5 (upper arm) → J4 → Link3 (fork) → J5 → Platform

#### Kinematic analysis
##### Mechanism

All joints are revolute, and every joint axis passes through the centre point O = (0, 0, 0), which makes this a spherical mechanism.

| Joint | Connects | Axis | Point on axis (mm) | Role |
| --- | --- | --- | --- | --- |
| J1 | Base – Link2 | Y | (0, −45, 0) | active, θ1 (pan) |
| J2 | 	Link2 – Platform | X | (30, 0, 0) | passive (= tilt)) |
| J3 | 	Base – Link4+5 | X | (−49, 0, 0) | active, θ2 |
| J4 | 	Link4+5 – Link3 | Z | (0, 0, −30) | passive |
| J5 | 	Link3 – Platform | Y | (0, 25, 0) | 	passive |

The inputs are

```math
\theta_1(t) = A_1 \sin(\omega_1 t), \qquad \theta_2(t) = A_2 \sin(\omega_2 t)
```

| Variable | Meaning | Value |
| --- | --- | --- |
| A1, A2 | Input amplitudes, entered in degrees and converted to rad | 40°, −35° |
| w1, w2 | Input frequencies (rad/s) | 0.4, 0.4 |
| t | Simulation time, one full period | 15.708 |

##### Degrees of freedom


$$DOF = 3(n - 1) - 2j_1 - j_2$$

n = 5 links (base, Link2, Link4+5, Link3, platform)
j1 = 5 full joints (J1–J5, all revolute)
j2 = 0 half joints


$$DOF=3(5-1)-2(5)-0=2$$

This confirms θ1 and θ2 are the inputs.
##### Symbolic Kinemnatics
The angles of the passive joints J2, J4 and J5 are named `jj2`, `jj4` and `jj5` in the code (written jj2,jj4,jj5). Each leg gives the platform orientation as a product of rotations:


$$RA=Ry(1)Rx(jj2),RB=Rx(2)Rz(jj4)Ry(jj5)$$

Loop closure. Link3 keeps the J4 axis perpendicular to the J5 axis:

$$\mathbf{u}_4 \cdot \mathbf{u}_5 = 0, \qquad \mathbf{u}_4 = R_x(\theta_2)\,\hat{z}, \quad \mathbf{u}_5 = R_A\,\hat{y}$$

which gives

$$\cos\theta_1 \cos\theta_2 \sin jj_2 - \sin\theta_2 \cos jj_2 = 0$$

Pan and tilt (assembly branch with cos jj2 > 0):

$$\text{pan} = \theta_1, \text{tilt} = jj_2 = {arctan2}(\sin\theta_2,\ \cos\theta_1\cos\theta_2)$$

These follow from the platform rotation matrix: pan = arctan2(R13, R33) and tilt = arctan2(−R23, R22).

Passive joints of leg B, from Rx(2)TRA=Rz(jj4)Ry(jj5)=M

$$jj_4 = \{arctan2}(-M_{12},\, M_{22}), \qquad jj_5 = {arctan2}(-M_{31},\, M_{33})$$

##### Velocity, acceleration and singularity
Velocities and accelerations are found by differentiating pan(t) and tilt(t) symbolically with `diff`.The Jacobian relates input rates to output rates:

```math
$$\begin{bmatrix}\dot{\text{pan}} \\ \dot{\text{tilt}}\end{bmatrix} = J \begin{bmatrix}\dot\theta_1 \\ \dot\theta_2\end{bmatrix}, \qquad
J = \begin{bmatrix} 1 & 0 \\ \dfrac{\partial\,\text{tilt}}{\partial\theta_1} & \dfrac{\partial\,\text{tilt}}{\partial\theta_2} \end{bmatrix}$$
```

$$\det J = \frac{\cos\theta_1}{\cos^2\theta_1\cos^2\theta_2 + \sin^2\theta_2}$$

- det J = 1: tilt changes one-to-one with θ2 (this is the case at θ1 = 0).
- det J → 0 at |θ1| = π/2: a singularity. There θ2 no longer changes the tilt, so the panel can't be controlled in that direction. From the symbolic math, det J is never close to zero. The motion never comes close to the singularity. 
  
#### Results 
##### Summary Table

| Parameter | Value |
| --- | --- |
| max pan | 0.6981 rad |
| max tilt | 0.7405 rad |
| max pan velocity | 0.2793 rad/s |
| max tilt velocity | 0.2498 rad/s |
| max pan acceleration | 0.1117 rad/s² |
| max tilt acceleration | 0.1503 rad/s² |
| J2 range (= tilt) | ±0.7405 rad |
| J4 range | 0 to 0.4486 rad |
| J5 range | ±0.5545 rad |
| det J range | 1.000 to 1.086 | 

##### Figures
| Figure | Descrption |
| --- | --- |
| Input angle | θ1 and θ2 vs time |
| Pan & Tilt | Pan vs time; tilt vs time with θ2 for comparison |
| Velocity & Acceleration | Pan and tilt rates and accelerations |
| Passive joints | Angles of J2 (= tilt), J4 and J5 vs time |
| Pan-tilt path | Tilt vs pan: the directions the panel points during the motion |

![Input angle](media/Input_angle.png)

![Pan vs Time](media/pan_angle.png) 

![Tilt vs Time](media/tilt_angle.png)

|  |  |
| :---: | :---: |
| ![Pan velocity](media/pan_v.png) | ![Tilt velocity](media/tilt_v.png) 
| ![Pan acceleration](media/pan_a.png) | ![Tilt acceleration](media/tilt_a.png) |

![Passive joints](media/passive_joints.png)

![Pan-tilt path](media/panel_path.png)

##### Symbolic simulation

![symbolic math simulation](media/symbolic_math_ani.gif)

##### Limitations
- The animation uses simplified shapes (arcs, rings and pins) sized from the CAD parts, not the CAD geometry itself.
- Kinematics only: masses, gravity and joint torques are not modelled.

## Simscape Multibody

For the proposed solar-tracking application, the platform must be able to change its orientation in both pan and tilt. The Simscape Multibody model was used to relate these platform angles to the rotation of the two actuated joints at the base.

![Isometric animation of the spherical parallel mechanism](media/iso.gif)

#### Model Configuration

The fixed base and moving platform are connected by two chains of rigid bodies that form a closed-loop mechanism with five revolute joints. Angular motion is prescribed at J1 and J3. The passive joints, J2, J4, and J5, move according to the constraints of the linkage. The platform orientation is measured relative to the fixed base and expressed as pan and tilt.

![Simscape Multibody block diagram showing the joint-angle inputs and the platform-angle outputs](media/simscape_model.png)

#### Platform Response Under Combined Inputs

J1 and J3 were driven simultaneously with sinusoidal angle commands. The amplitudes were 0.698 rad (40°) at J1 and 0.611 rad (35°) at J3. Both commands had an angular frequency of 0.4 rad/s, corresponding to a period of approximately 15.7 s. The plots show one cycle, with time in seconds and angles in radians.

| Platform Pan | Platform Tilt and Measured J3 Angle |
| :---: | :---: |
| ![Platform pan angle plotted against time](media/scope_pan.png) | ![Platform tilt and measured J3 angle plotted against time](media/scope_tilt_theta2.png) |

The pan amplitude was approximately 0.70 rad, close to the commanded J1 amplitude. In the right plot, the platform tilt (yellow) had an amplitude of approximately 0.74 rad, compared with 0.61 rad for the measured J3 angle (blue, θ₂). For this test, the platform tilt amplitude exceeded the J3 rotation amplitude by approximately 0.13 rad.

#### Single-Joint Sweep Tests

The time histories quantify the platform response when both inputs are active. The sweeps below show the motion of the links and platform when each input is applied separately. Both tests started from Position A, with J1 at 0° and J3 at +90°. In each test, one joint was swept while the other was held at its starting angle.

| J1 Sweep - Top View | J3 Sweep - Isometric View |
| :---: | :---: |
| ![Top view of the J1 sweep with J3 held at 90 degrees](media/j1_sweep_top.gif) | ![Isometric view of the J3 sweep with J1 held at zero degrees](media/j3_sweep_iso.gif) |

The J3 angles at B and C in the J3 sweep are from the input command.
Angles at P1 and P2 are approximate readings from the Simulink Scope plots.
A dash indicates that the source data did not establish an angle for that pose.

| With J3 fixed at +90°, the top view shows the platform turning relative to the base. | With J1 fixed at 0°, the isometric view shows the change in platform inclination. |
| [J1 sweep video (MP4)](media/j1_sweep_top.mp4) | [J3 sweep video (MP4)](media/j3sweep_iso.mp4) |

#### Reproducing the Combined-Input Simulation

1. Download and extract the repository. Open MATLAB with Simulink, Simscape, and Simscape Multibody installed.
2. Set the MATLAB Current Folder to `simulation/`. Open `SPM_allR.slx` and click **Run**.
3. Open **Scope pan** to view the platform pan angle and **Scope tilt,θ2** to compare the platform tilt with the measured J3 angle.

| File | Description |
| --- | --- |
| [SPM_allR.slx](simulation/Simscape%20Multibody/SPM_allR.slx) | Simscape Multibody model |
| [spm_params.m](simulation/Simscape%20Multibody/spm_params.m) | Model geometry and input parameters |
| [CAD files](simulation/cad/) | Geometry of the rigid bodies |
| [Mechanism.avi](media/Mechanism.avi) | Submission demonstration video |
| [Render.jpg](media/Render.jpg) | Isometric render of the mechanism |
| [Isometric](media/iso.mp4) · [Top](media/Top.mp4) · [Left](media/left.mp4) | Simulation recordings from three viewpoints |

## Physical prototype

3D CAD assembly of the 2-DOF Spherical Parallel Mechanism modeled in Autodesk Fusion 360, illustrating the home configuration and single-joint motions corresponding to J1 and J3:

| Neutral Pose | J3 Sweep | J1 Sweep |
| :---: | :---: | :---: |
| ![Neutral Pose](media/Fusion360%20View1.png) | ![Tilt Motion](media/Fusion360%20View2.png) | ![Pan Motion](media/Fusion360%20View3.png) |

#### SLS 3D-Printed Prototype Testing (Formlabs Fuse / Nylon 12)

Manual actuation tests were conducted by rotating the two base input axes to verify the motion of the central ring:

| State 1 | State 2 | State 3 |
| :---: | :---: | :---: |
| ![Hardware move 1](media/Hardware%20move1.jpg) | ![Hardware move 2](media/Hardware%20move2.jpg) | ![Hardware move 3](media/Hardware%20move3.jpg) |

| State 4 | State 5 |
| :---: | :---: |
| ![Hardware move 4](media/Hardware%20move4.jpg) | ![Hardware move 5](media/Hardware%20move5.jpg) |

#### Physical Prototype Demonstration Video

The SLS 3D-printed prototype was manually actuated to demonstrate the conversion of two independent base rotations (J1 and J3) into pure 2-DOF spherical orientation of the central platform.

The demonstration animation illustrates the smooth, coordinated motion of the assembled mechanism, confirming that all five revolute joint axes remain aligned with the central Remote Center of Motion (RCM) without link interference or binding across the tested workspace.

Despite these physical tolerances, the prototype successfully validates the kinematic behavior and smooth spherical mobility of the 2-DOF Spherical Parallel Mechanism within its functional operating range.

![Hardware Motion Demonstration](hardware/Hardware%20Video.gif)
