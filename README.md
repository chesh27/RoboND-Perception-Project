[![Udacity - Robotics NanoDegree Program](https://s3-us-west-1.amazonaws.com/udacity-robotics/Extra+Images/RoboND_flag.png)](https://www.udacity.com/robotics)
# 3D Perception

This is the third project of the Udacity Robotics Software Engineer Nanodegree. 
Before starting any work on this project, I completed all steps for Exercise 1, 2 and 3. At the end of Exercise-3 I have a pipeline that can identify points that belong to a specific object.

Exercise 1: Filtering pipeline and RANSAC filtering 
I used the filters listed below to take tabletop.pcd as input and output a filtered point cloud: 
- VoxelGrid Downsampling Filter 
- PassThrough Filter 
- Extract Indices Filter 
- RANSAC Plane Fitting (Random Sample Consensus) 
- Statistical Outlier Removal Filter 

Exercise 2: Clustering for segmentation pipeline 
- Subscribe to /sensor_stick/point_cloud topic
- Apply Euclidian clustering on filtered point clouds
- Publish clusters to separate pcl_table and pcl_objects topics 

Exercise 3: Feature Extraction and SVM classifier training for object recognition 
- Use object shape and color to classify and recognize and label objects 
- Complete capture_features.py
  - I tuned various paraeters before settling on training my model on 50 poses per object (8 objects from 3 worlds, and 1 'arm_part' object), used a linear kernel on my SVM and achieved about 85% accuracy overall. TThe 'arm_part' and 'glue' were frequently misclassified as each other, so in the future I would exclude arm_part and train only on the 8 objects The reason I decided to use 'arm_part' was because in its absense, my sensor was recognizing the robot arm to be an object itself. 
  - In features.py, I tried different binning schemes before settling on the one I used. The range of color histoogram values is 0 -- 256 and the range of normal historgram values i s -1 -- 1 

In this project, I assimilate my work from previous exercises to successfully complete a tabletop pick and place operation using PR2.

The PR2 has been outfitted with an RGB-D sensor. This sensor however is a bit noisy, much like real sensors. Given the cluttered tabletop scenario, I implemented a perception pipeline using my work from Exercises 1,2 and 3 to identify target objects from a so-called “Pick-List” in that particular order, pick up those objects and place them in corresponding dropboxes.

Project: 
- The 3 exercises are collected in pcl_callback(), which is in pr2_robot/scripts/project_template.py
- In pr2_mover, I loop through the list of possible objects, dropbox parameters and the pick list (in pr2_robot/config) for each individual world. While an object matches that which is in the pick list, I assign the pick_pose and place_pose for it, and send the request through mack_yaml_dict to putput .yaml files (pr2_robot/scripts). If it is not detected, I use default values of (0,0,0) for position and (0,0,0,0) for orientation. 

Results: 
- I was able to identify and correctly label 3 out of 3 objects in the first world, 4 out of 5 in the second and 8/8 in the third world 
- The project Writeup contains a detailed account of the results including images of the labeled tabletop objects.  

# Project Setup
For this setup, catkin_ws is the name of active ROS Workspace. This can be created by: 

```sh
$ mkdir -p ~/catkin_ws/src
$ cd ~/catkin_ws/
$ catkin_make
```
Now that you have a workspace, clone or download this repo into the src directory of your workspace:
```sh
$ cd ~/catkin_ws/src
$ git clone https://github.com/udacity/RoboND-Perception-Project.git
```
Now install missing dependencies using rosdep install:
```sh
$ cd ~/catkin_ws
$ rosdep install --from-paths src --ignore-src --rosdistro=kinetic -y
```
Build the project:
```sh
$ cd ~/catkin_ws
$ catkin_make
```
Add following to your .bashrc file
```
export GAZEBO_MODEL_PATH=~/catkin_ws/src/RoboND-Perception-Project/pr2_robot/models:$GAZEBO_MODEL_PATH
```

If you haven’t already, following line can be added to your .bashrc to auto-source all new terminals
```
source ~/catkin_ws/devel/setup.bash
```

To run the demo:
```sh
$ cd ~/catkin_ws/src/RoboND-Perception-Project/pr2_robot/scripts
$ chmod u+x pr2_safe_spawner.sh
$ ./pr2_safe_spawner.sh
```
![demo-1](https://user-images.githubusercontent.com/20687560/28748231-46b5b912-7467-11e7-8778-3095172b7b19.png)



Once Gazebo is up and running, make sure you see following in the gazebo world:
- Robot

- Table arrangement

- Three target objects on the table

- Dropboxes on either sides of the robot


In your RViz window, you should see the robot and a partial collision map displayed:

![demo-2](https://user-images.githubusercontent.com/20687560/28748286-9f65680e-7468-11e7-83dc-f1a32380b89c.png)

Proceed through the demo by pressing the ‘Next’ button on the RViz window when a prompt appears in your active terminal

The demo ends when the robot has successfully picked and placed all objects into respective dropboxes (though sometimes the robot gets excited and throws objects across the room!)

Close all active terminal windows using **ctrl+c** before restarting the demo.

You can launch the project scenario like this:
```sh
$ roslaunch pr2_robot pick_place_project.launch
```
