## Project: Perception Pick & Place
### Writeup Template: You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---


# Required Steps for a Passing Submission:
1. Extract features and train an SVM model on new objects (see `pick_list_*.yaml` in `/pr2_robot/config/` for the list of models you'll be trying to identify). 
2. Write a ROS node and subscribe to `/pr2/world/points` topic. This topic contains noisy point cloud data that you must work with.
3. Use filtering and RANSAC plane fitting to isolate the objects of interest from the rest of the scene.
4. Apply Euclidean clustering to create separate clusters for individual items.
5. Perform object recognition on these objects and assign them labels (markers in RViz).
6. Calculate the centroid (average in x, y and z) of the set of points belonging to that each object.
7. Create ROS messages containing the details of each object (name, pick_pose, etc.) and write these messages out to `.yaml` files, one for each of the 3 scenarios (`test1-3.world` in `/pr2_robot/worlds/`).  [See the example `output.yaml` for details on what the output should look like.](https://github.com/udacity/RoboND-Perception-Project/blob/master/pr2_robot/config/output.yaml)  
8. Submit a link to your GitHub repo for the project or the Python code for your perception pipeline and your output `.yaml` files (3 `.yaml` files, one for each test world).  You must have correctly identified 100% of objects from `pick_list_1.yaml` for `test1.world`, 80% of items from `pick_list_2.yaml` for `test2.world` and 75% of items from `pick_list_3.yaml` in `test3.world`.
9. Congratulations!  Your Done!

# Extra Challenges: Complete the Pick & Place
7. To create a collision map, publish a point cloud to the `/pr2/3d_map/points` topic and make sure you change the `point_cloud_topic` to `/pr2/3d_map/points` in `sensors.yaml` in the `/pr2_robot/config/` directory. This topic is read by Moveit!, which uses this point cloud input to generate a collision map, allowing the robot to plan its trajectory.  Keep in mind that later when you go to pick up an object, you must first remove it from this point cloud so it is removed from the collision map!
8. Rotate the robot to generate collision map of table sides. This can be accomplished by publishing joint angle value(in radians) to `/pr2/world_joint_controller/command`
9. Rotate the robot back to its original state.
10. Create a ROS Client for the “pick_place_routine” rosservice.  In the required steps above, you already created the messages you need to use this service. Checkout the [PickPlace.srv](https://github.com/udacity/RoboND-Perception-Project/tree/master/pr2_robot/srv) file to find out what arguments you must pass to this service.
11. If everything was done correctly, when you pass the appropriate messages to the `pick_place_routine` service, the selected arm will perform pick and place operation and display trajectory in the RViz window
12. Place all the objects from your pick list in their respective dropoff box and you have completed the challenge!
13. Looking for a bigger challenge?  Load up the `challenge.world` scenario and see if you can get your perception pipeline working there!

## [Rubric](https://review.udacity.com/#!/rubrics/1067/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Exercise 1, 2 and 3 pipeline implemented
#### 1. Complete Exercise 1 steps. Pipeline for filtering and RANSAC plane fitting implemented.
The code of my work on Exercise 1 can be found [here](https://github.com/muchowsn/RoboND-Perception-Exercises/tree/master/Exercise-1)
The comments in the code provide an explanation of what I was doing

#### 2. Complete Exercise 2 steps: Pipeline including clustering for segmentation implemented.  
The code of my work on Exercise 2 can be found [here](https://github.com/muchowsn/RoboND-Perception-Exercises/tree/master/Exercise-2)
The comments in the code provide an explanation of what I was doing
I outsourced the functions used in `template.py` into the python file `segmentation.py`. This proved to be useful, as we were using the same functionality in all following exercises. So I just had to enter the function calls in my main code and not copy all of the functionality over and over again.

#### 2. Complete Exercise 3 Steps.  Features extracted and SVM trained.  Object recognition implemented.
The code of my work on Exercise 3 can be found [here](https://github.com/muchowsn/RoboND-Perception-Exercises/tree/master/Exercise-3)
The comments in the code provide an explanation of what I was doing
I outsourced the functions used in `object_recognition.py` into the python file `segmentation.py`

### Pick and Place Setup

#### 1. For all three tabletop setups (`test*.world`), perform object recognition, then read in respective pick list (`pick_list_*.yaml`). Next construct the messages that would comprise a valid `PickPlace` request output them to `.yaml` format.

I experimented with various training cycles for my SVM. I noticed that it was quite hard to get the accuracy better after reaching ~0.90 in the normalized confusion matrix. The set I was using in the end for the project took 100 cycles and looks like this:
![Training set figure 1 with 100 runs](https://github.com/muchowsn/RoboND-Perception-Project/blob/master/writeup_images/figure_1.png)
![Training set figure 2 with 100 runs](https://github.com/muchowsn/RoboND-Perception-Project/blob/master/writeup_images/figure_2.png)

#### World 1

World 1 YAML File: [output\_1.yaml](https://github.com/muchowsn/RoboND-Perception-Project/blob/master/pr2\_robot/scripts/output\_1.yaml)
Object detection rate: 3/3
All nice and shiny in world 1!

![World 1 Objects with labels](https://github.com/muchowsn/RoboND-Perception-Project/blob/master/writeup_images/world1.png)

#### World 2

World 2 YAML File: [output\_2.yaml](https://github.com/muchowsn/RoboND-Perception-Project/blob/master/pr2\_robot/scripts/output\_2.yaml)
Object detection rate: 5/5


![World 2 Objects with labels](https://github.com/muchowsn/RoboND-Perception-Project/blob/master/writeup_images/world2.png)

#### World 3

World 3 YAML File: [output\_3.yaml](https://github.com/muchowsn/RoboND-Perception-Project/blob/master/pr2\_robot/scripts/output\_3.yaml)
Object detection rate: 8/8
the noise would have a few of the items changeing but would more or less idetify all 8.

![World 3 Objects with labels](https://github.com/muchowsn/RoboND-Perception-Project/blob/master/writeup_images/world3.png)


### The Code

The code of my work on the project can be found [here](https://github.com/yulivee/RoboND-Perception-Project/blob/master/pr2_robot/scripts/project_template.py)
I outsourced the functions used in `project_template.py` in the python file `perception_functions.py`: [perception functions](https://github.com/yulivee/RoboND-Perception-Project/blob/master/pr2_robot/scripts/perception_functions.py)

The comments in the code follow along what I was doing as per usual. In short:

#### contents of pcl\_callback
The `pcl_callback`-function is called by the ROS-subscriber tyed to the virtual RGBD-Camera and gets a point cloud as input data. The following steps were taken to detect objects from this point cloud:

1. Convert the ROS-PointCloud data format into something the pcl library can work with
2. Remove noise
3. Downsample the data to reduce the number of points
4. Remove unnecessary data from point cloud to ease object detection by applying prior knowledge of object location
5. Remove the table from the scene
6. Identify clusters of points to be able to distiguish individual objects
7. Apply a different color to the individual objects ( useful for debugging in RViz)
8. Publish objects, table and coloured objects to ROS
9. Extract the color information for additional information guiding the object detection
10. Decide which object was found
11. Apply a label to the object 
12. Calculate the centroid of the object

#### Technologies in use

1. Statistical Outlier Filter
This was used for step 2 of the perception pipeline. It is a techniqe to remove noise by calculating distances between the points. The parameters for this filter are a mean-value (represents the distance between neighbors) and a standard deviation-value ( deviation from mean distance). I was achieving good results with a value of 20 for mean and a deviation of 0.3.

2. Voxel Grid Downsampling
This was used for step 3 of the perception pipeline. The voxel grid filter downsamples the data by taking a spatial average of the points in a given cloud and combining point within a certain radius into one point. The parameter for this filter is this radius, called LEAF\_SIZE. I achived good results with a LEAF\_SIZE with 0.005.

3. Passthrough Filter
This was used for step 4 of the perception pipeline. With prior information about the location of the target in the scene, the Pass Through Filter can remove useless data from the point cloud. It takes in locational parameters: axes on which the filter should be applied together with a min and max value for each axis. I applied to filters: one along the z-axis with min 0.6 and max 1.3 to remove the table legs and one along the y-axis with min -0.5 and max 0.5 to remove the table edges. what remains is the table with the items.

4. RANSAC Plane Fitting
This was used for step 5 of the perception pipeline. The Random Sample Consensus algorithm devides a cloud into inliers (points belonging to a model) and outliers (the rest - to be discarded). This was used to delete the table and only leave the objects in the point cloud. The parameter of RANSAC is the maximum distance between points before they are regareded as outliers. I achieved good results with max\_distance 0.01

5. Euclidean Clustering
This was used for step 6 of the perception pipeline. By using a k-d tree this clustering technology divdes a given point cloud into individual objects. First we remove the color information from the points, as PCLs cluster implementation needs just location information. Then parameters for Cluster Tolerance, min and max cluster size and a search method are defined. The min and max values remove too big and too small clusters. The values I ended up with after a lot of trial and error are: cluster\_tolerance 0.015, min 20, max 3000.

6. Cluster Visualization
This was used for step 7 of the perception pipeline. To visualize the results of the clustering in RViz, each detected object was assigned a unique color.

7. Feature Association
This was used for step 9 of the perception pipeline. In step 10, a Support Vector machine would finally decide which object has been found by looking at two features: shape and color. For the color part histogramm features were extracted from the input data.

8. Support Vector Machine
This was used for step 10 of the perception pipeline. The SVM is a machine learning algorithm that devides the point cloud into discrete classes by making use of a trainingset. Each item in a trainingset is characterized by a feature vector and a label. The training takes place before the execution of the project and during execution of the project, the trainingset is used to determine which feature a given point cloud matches.

#### Improvements

For reasons yet unknown to me, the project keeps mixing up the glue and soap2. I would experiment a bit further with various training set sizes to see if this issue gets better, but because of the slow VM speed I refrained from that. I would also be worth checking the feature association step and checking if the histograms look to similar.





