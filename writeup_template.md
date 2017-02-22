##Writeup

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

[image1]: ./output_images/undistorted_calibration1.jpg "Undistorted"
[image2]: ./output_images/undistorted_straight_lines2.jpg "Undistorted Road"
[image3]: ./output_images/binary_straight_lines1.jpg "Binary Example"
[image4]: ./output_images/transformed_straight_lines1.jpg "Warp Example"
[image5]: ./output_images/polyfit_test2.jpg "Fit Visual"
[image6]: ./output_images/test2.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!
###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "AdvLaneLines.ipynb".
Here I detect the corners of the chessboard and store the value in `imagepoints`, I also store the coordinates of the chessboard corners in real world 3d space.
I reuse the computed `objpoints` and `imagepoints` to calibrate and calculate the distortion coefficients. This is later used to undistort images, without furthur recomputation.
I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![Undistorted Image][image1]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.
To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

Code is in CELL 4.

I tried multiple combination of gradient\_X, gradient\_y, magnitude gradient and direction gradient. A combination worked will for harder challenge while another combination worked well for project video. I currently have the threshold which works well for project video, as it is graded.

The best result for project video came with boolean AND of magnitude gradient and direction gradient.
For color, I convert RGB to HLS and use the binary OR of 'L' and 'S'. 
All of the above is encapsulated in the function `filter_image_with_lane_line`.

Here's an example of my output for this step.

![alt text][image3]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

Code in CELL 3.

The code in cell 3, contains global variable src, which selected the area to transform and dst, which is the resultant dimension on the transformed image.
The function `transform` gets the perspective transform coefficients and applies them on the image, a inverse transform coefficients is also calculated and stored.

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Code in CELL 7 and 9
I haven't made much changes to the lane detection code provided in the classroom. I although have tuned parameters to find a better fit than the raw code.

The function `find_lane`, takes binary\_warped image as input, takes the histogram of the image and finds the index with highest value. It uses the 2 max value (left of middle and right of middle), as the starting point of lanes.
We do this for every windows in the image (here n\_window = 7) and draw rectangle around the identified white pixels. These indexes are later used to fit a quadratic polynomial.


![alt text][image5]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Code in CELL 7 and 9

In the function `find_lane`, apart from finding the lanes, I also find out radius of curvature of the left and right lane. This is done using the US roads standards, as mentioned in the lecture.
The code is taken from the lecture videos.

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Cell 7 and 9 draws images with lane lines.CELL 10 contains the lane lines drawn on the original RGB image for better visualization.

![alt text][image6]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](https://www.youtube.com/watch?v=GXlDawp2g54&feature=youtu.be)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The most troublesome part in the project was coming up with the best parameters for the thresholded binary image.
After many-many iterations, the pipeline worked well on the project video. However during the process there were some parameters (gradx, grady, angular, magnitude) which worked decent for the challenge video.
The current solution also need improvement in finding out the area that required perspective transform. As of now it is hardcoded.

Curve fitting is nicely done, and IMO that shouldn't be a problem as the lane lines would be quadratic at most. With multiple video angle and sensors, hopefully it will be easier to detect lane lines.
