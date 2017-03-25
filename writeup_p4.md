##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/test2.jpg "Origin"
[image2]: ./output_images/undistort_test2.jpg "Undistorted"
[image3]: ./output_images/test2_combined.jpg "Binary Example"
[image4]: ./output_images/test2_bird_view.jpg "Warp Example"
[image5]: ./output_images/find_lane.jpg "Fit Visual"
[image6]: ./output_images/result.jpg "Output"
[image7]: ./output_images/test2_gradx.jpg "Canny edge detection"
[image8]: ./output_images/test2_mag_binary.jpg "Magnitude of the Gradient"
[image9]: ./output_images/test2_dir_binary.jpg "Direction of the Gradient"
[image10]: ./output_images/test2_hls_binary.jpg "HLS"
[image11]: ./output_images/curve.png "curve equation"

[video1]: ./output_images/project_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!
###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in "./project4_advanced_lane_lines.ipynb" 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]
![alt text][image2]
###Pipeline (single images)

####1. 
To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image1]
####2. 
I used a combination of color and gradient thresholds to generate a binary image (cell 5-9 in ipython notebook)
I first use cv2 to convert to grayscale then apply Canny edge detection (abs_sobel_thresh):
![alt text][image7]
Then using Magnitude of the Gradient and Direction of the Gradient.
![alt text][image8]
![alt text][image9]
and convert the image to HLS and using the S channel to find the better line
![alt text][image10]

finally, I combine those threshold together to have a nice image on the lane line
Here's an example of my output for this step.  

![alt text][image3]

####3. 

The code for my perspective transform includes a function called `prespective_transform()` in the 12 code cell of the IPython notebook.  The `prespective_transform()` function takes as inputs an image (`img`), use the given source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 200, 720      | 200, 720      | 
| 1100, 720     | 1050, 720     |
| 590, 450      | 180, 0        |
| 690, 450      | 1070, 0       |

And here is the image after transform

![alt text][image4]

####4.  
The code for my finding lane line includes a function called `find_lane()` in the 16 code cell of the Ipython notebook. The `find_lane()` function takes as inputs as warped image and divide into left and right side. Each side divide into 9 windows, finding the point inside each window and then concatenate toghter, using numpy polyfit and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

####5. 
After find_lane(), I calculate the curvature and the position of the car, the code is in the 15 code cell of the Ipython notebook. I use ![alt text][image11] to find the curvature. For the position, I calcuate the center of the left and right line and compare it with the center of the image.



####6. 

I combine everything and draw out the lane, the code is in the 20 code cell. Here is an example of my result on a test image:

![alt text][image6]

---

###Pipeline (video)

####1. After testing with the images, I use the same pipline for the video.

Here's a [link to my video result](./project_video.mp4)

---

###Discussion

####1. 

This pipline does not work on the light issue like in the harder challenge video, where the light shining on the windshield, object cross in front the car. I could adjust the light issue changing the light of the image, and if object crossing in front, I can use the previous data to keep the drawing the line, but if the object stay to long, the line will off from the road.  

