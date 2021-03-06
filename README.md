# superquadric-grasp
Framework for computing a good grasping pose, by exploiting superquadric models for the object and the robot hand.
#### Overview
- [Theoretical background](#theoretical-background)
- [Dependencies & how to compile](#dependencies)
- [Module pipeline](#module-pipeline)
- [Use case & results](#use-case)
- [Documentation & more](#documentation)


## Theoretical background

The **superquadric-grasp** framework is based on **object** and **hand modeling** with superquadric functions.
The superquadric modeling the object can be obtained with the [superquadric-model](https://github.com/robotology/superquadric-model/tree/master) code, wherease the volume graspable by the hand is represented by an ellipsoid computed a priori and properly attached to the robot hand frame.

The grasping pose for the object of interested is computed by **overlapping the hand ellipsoid to the superquadric modeling the object**. From a mathematical viewpoint, the pose is computed by solving a novel optimization problem:

<p align="center">
<img src="https://github.com/robotology/superquadric-grasp/blob/master/misc/optimization-problem-general.png" width=400>
</p>

The cost function imposes the minimization of the distance between the ellipsoid and the object superquadric. The formulation is general enough to deal also with **obstacle avoidance** with the definition of suitable constraints. For instance,  the table on which the object is located can be modeled as an object in order to prevent the robot hand from hitting the table:

<p align="center">
<img src="https://github.com/robotology/superquadric-grasp/blob/master/misc/optimization-problem-specific.png" width=400>
</p>

More details are in [our ICRA paper [1]](#references).

[`Go to the top`](#superquadric-grasp)
## Dependencies
- [YARP](https://github.com/robotology/yarp)
- [iCub](https://github.com/robotology/icub-main)
- [icub-contrib-common](https://github.com/robotology/icub-contrib-common)
- [IPOPT](https://projects.coin-or.org/Ipopt)
- [OpenCV](http://opencv.org/)
- [tactile-control-lib](https://github.com/robotology/tactile-control/tree/master/tactile-control-lib)

## How to compile
In `Linux systems` code can be compiled as follows:
```sh
git clone https://github.com/robotology/superquadric-grasp.git
cd superquadric-grasp
mkdir build; cd build
ccmake ..
make install
```
[`Go to the top`](#superquadric-grasp)
## Module pipeline
The module structure is outlined in the following picture:

<p align="center">
<img src="https://github.com/robotology/superquadric-grasp/blob/master/misc/superquadric-grasping.png" width=700>
</p>

The superquadric-grasp module consists of:
- `GraspComputation` class, computing the grasping pose, once the object model is received.
- `GraspVisualization` thread, showing the computed pose and the used object model overlapped to the robot camera.
- `GraspExecution` thread, implementing the robot movements for grasping the object.

The superquadric-grasp module also provides some `thirft services` through a `rpc port`. The user can communicate with the module through these services in order to ask the state of the threads and to modify some parameters on the fly.
In addition, the thrift services allow the user to ask directly the computation of the grasping pose and the execution of the grasping task. The module also receives the camera image as input and, if the `GraspVisualization` is enabled, the output is shown on a yarpview. 

[`Go to the top`](#superquadric-grasp)
## Use case
The superquadric-grasp module requires the reconstructed superquadric of the object. An example code for retriving this information, together with a tutorial is provided in the folder [`tutorial`](https://github.com/robotology/superquadric-grasp/tree/master/tutorial) in this repository.

## Some results
Here is an example of grasping poses computed with our method:

<p align="center">
<img src="https://github.com/robotology/superquadric-grasp/blob/master/misc/results.png">
</p>

The execution time for pose computation is nealy 2.0 s. Hereafter, you can find a video showing the computed poses used for real grasping tasks:

[<img src="https://github.com/robotology/superquadric-grasp/blob/master/misc/new-video-grasp.png" width=1000>](https://www.youtube.com/watch?v=dfYdBQ-mBJQ&feature=youtu.be)
[`Go to the top`](#superquadric-grasp)
## Documentation
Online code documentation is available [here](https://robotology.github.io/superquadric-grasp).

## Going beyod
The method and the code we developed has been tested on the iCub humanoid robot in case one object is in the robot field of view. However, this approach can be extended to other robotic hands and to work with multiple objects scenario.
#### Porting the approach to other robots
Our grasping approach is based on modeling the volume graspable by the robot hand instead of using a detailed representation of the hand itself. For this reason, our method can be ported to other robots, just **changing the dimensions and the shape  of the superquadric modeling the volume graspable by the hand**. At this regard, we already managed to performed autonomous grasps with our approach with the R1 robot [2].

#### Dealing with multiple objects
If more than one objects are in the robot field of view, the `superquadric-model` approach is able to compute the superquadric of the desired object as long as the objects can be properly segmented by [lbpExtract module](https://github.com/robotology/segmentation), i.e. if their images are not adjacient in the image.
The `superquadric-grasp` works in a multiple objects scenario if the objects do not hinder the arm momevements during the approaching phase.

[`Go to the top`](#superquadric-grasp)

## License
Material included here is Copyright of _iCub Facility - Istituto Italiano di Tecnologia_
and is released under the terms of the GPL v2.0 or later. See the file LICENSE for details.



## References
[1] G. Vezzani, U. Pattacini and L. Natale, "A grasping approach based on superquadric models", _IEEE-RAS International Conference on Robotics and Automation 2017_, pp 1579-1586. 

[![DOI](https://zenodo.org/badge/54572419.svg)](https://zenodo.org/badge/latestdoi/54572419)

[2] A. Parmiggiani, L. Fiorio, A. Scalzo, A. V. Sureshbabu, Ma. Randazzo, M. Maggiali, U. Pattacini, H. Lehmann, V. Tikhanoff, D. Domenichelli, A. Cardellino, P. Congiu, R. Cingolani, L. Natale, G. Metta, "The Design and Validation of the R1 Personal Humanoid," _2017 IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS 2017), Vancouver, Canada_, 2017.



