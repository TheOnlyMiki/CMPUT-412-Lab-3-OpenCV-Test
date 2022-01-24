# Introduction
The goals of this lab are to:
* Intertacting with ROS camera topic to get real-time pictures
* Knowing how to filter color through build in functions in opencv
* Knowing how to match and compare images by feature detecture and matcher

This lab will use the ROS noetic environment and requires gazebo and turtlebot3 (see below for installation instructions)

### Installing lab package
```
cd ~/catkin_ws/src

git clone <repo-url>

cd ~/catkin_ws && catkin_make
source ~/catkin_ws/devel/setup.bash
```

### Launching world and turtlebot
`roslaunch lab_2 main.launch`

# Instructions and Potentailly Helpful Resources (non-exhaustive)

Note: the instruction below is mainly for python. If you are using other language, you need to find the corresponding class. 

First of all, we have to know that ROS store images which get from it's sensor by it own message formate - `senor_msg/Image`. However we can not using these meesages directly to work with Computer Vision algorithm. Therefore, we need to use `CvBridge`, which is a ROS library that provides an interface between ROS and OpenCV, to make conncetion and convert ROS images to OpenCV formate. After you make the package, in the `src` folder, there is a simple python file whcih shows how to use CvBridge and open the camera of robot, called `camera.py`. If you want to know more infromation about this, you can go to http://wiki.ros.org/cv_bridge/Tutorials/ConvertingBetweenROSImagesAndOpenCVImagesPython.

In this lab, you also need to know the how to detect colors in images. In order to descibe a color, We need to know the properties that make up this color. there are many way to define a color, a well knowed approach is RGB which describe all the colors by mixing red, green, and blue(Note: in Pyhton, ordering change to BGR). However, OpenCV uses HSV(Hue Saturation Value) model to describe color. If you want to know more about HSV model, please visit https://en.wikipedia.org/wiki/HSL_and_HSV . Therefore, in order to get color information in an image for specific model from other model, we need to use a function which include in `cv2` library called `cvtColor(src, code)`. Forexample, if you we want to convert a RGB image to HSV image in python, it should be like `cvtColor(image, cv2.COLOR_BGR2HSV)`. OpenCV also enable us to make other converting, such as RGB to Gray, etc. If you want to learn more about color converting 
function, you can visit these two link https://docs.opencv.org/3.4/de/d25/imgproc_color_conversions.html ,and https://www.geeksforgeeks.org/python-opencv-cv2-cvtcolor-method/. 

After we know how to describe color, we need to know how to handle color. Now, consider a question. If we only want to see the specific color in an image, what should we do?  The asnwer is let just watch this specific color. I know this sounds like crap, but it indeed what we will do in the program. We already know the properties of the color we want, and then we can set a window or bound so that our camera can only look for colors within this range. In order to do this, we need to use another funcion inlcude in `cv2` libray called `inRange(src, lower_bound, upper_bound)`. If you want to know more information about this function, please visit https://docs.opencv.org/3.4/da/d97/tutorial_threshold_inRange.html.  After you make the package, in the `src` folder, there is a simple python file whcih shows how to convert an image from RGB to HSV and fliter color, called `fliter.py`.

### Feature Matching

A feature is a meaningful area of an image that is unique or easily identifiable. Corners and high-density regions are a good feature, edges can divide the image into two regions, so they can be regarded as good features, and [blobs (image regions that are very different from surrounding areas)](https://towardsdatascience.com/image-processing-blob-detection-204dc6428dd) are also meaningful features.

Usually, our processing of features is divided into three parts, Detection, Description and Matching. [There is detailed introduction to feature detection and matching here, very helpful to read.](https://medium.com/data-breach/introduction-to-feature-detection-and-matching-65e27179885d)

#### Working with Feature matching is pretty straightforward. In general, we need to follow some steps:
1. Identify Points of Interest. (Features)
2. Describe the point of interest, describing its surrounding. The idea of this step is to make the algorithm robust to image transformations, like plane rotation, illumination changes, scale variations, etc.
3. Matching. This is where you want to compare the features of your object with the ones in other images, searching for similarities between them.

#### We recommend trying the following feature detectors and descriptors. 
We do not restrict students to use any detectors and algorithms. Also, encourage students to explore other detectors and algorithms, but please make sure to only use the detectors and matching algorithms from the OpenCV library, other CV libraries will not be accepted.

#### Feature detectors and descriptors:
- [FAST (Features from Accelerated Segment Test)](https://docs.opencv.org/4.x/df/d0c/tutorial_py_fast.html)
- [BRIEF (Binary Robust Independent Elementary Features)](https://docs.opencv.org/4.x/dc/d7d/tutorial_py_brief.html)
- [ORB (Oriented FAST and Rotated BRIEF)](https://docs.opencv.org/4.x/d1/d89/tutorial_py_orb.html)
- [SIFT (Scale-Invariant Feature Transform)](https://docs.opencv.org/4.x/da/df5/tutorial_py_sift_intro.html)

#### Feature matching algorithms:
- [BF (Brute-Force) Matcher](https://opencv24-python-tutorials.readthedocs.io/en/latest/py_tutorials/py_feature2d/py_matcher/py_matcher.html#basics-of-brute-force-matcher)
- [FLANN (Fast Library for Approximate Nearest Neighbors) Matcher](https://opencv24-python-tutorials.readthedocs.io/en/latest/py_tutorials/py_feature2d/py_matcher/py_matcher.html#flann-based-matcher)

More details and examples in the tutorial link. <br />
**Hint:** Try different combinations of detectors and matching algorithms, and choosing the properly hyperparameters can be useful.

# Task 

In this lab, students need to let their robot move along with the track on the ground, and stop after their robot seeing stop sign. 

Track is splited in differents color.

Whtie track is normal track. 

Blue track is accelertation zone where required you to increase your robot's speed to 1.5 times. 

Green tracks is a shortcut, you should let your robot change track from white to green. 

Red track is the deceleration zone where the speed of robot will decrease and stop before moving out from the zone.

# Grading
**40%**, Turtlebot can completely follow the entire path, which is the turtlebot able to follow the WHITE line perfectly.

**20%**, GREEN line (shortcut) can be filtering, and passed completely. (Hardest part, because incorrect timing of switching color filters will cause the robot to walk back along the white line)

**10%**, BLUE line (acceleration band) can be filtering, and passed completely by increasing half speed (1.5x).

**10%**, Red floor tiles (speed bumps) can be filtering, and slowed down by a half speed (0.5x), then start matching Stop-Sign icon.

**20%**, Turtlebot can effectively match the Stop-Sign icon, and should stopeed before passed red area.
