## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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


## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "advance_lane_find.ipynb"

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.
I have used open cv function "findChessboardCorners" to get the chessboard corners.Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

<br>
<img src="./output_images/chessboard_corners.png"/>
<br>
### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
<br>
<img src="./output_images/undistorted_chess.png"/>
<br>
There are two main steps to this process: I used chessboard images to image points and object points, and then used the OpenCV functions cv2.calibrateCamera() and cv2.undistort() to compute the calibration and undistortion.This is illustrated in ipython notebook cells 16,68


#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines 18 ,73 in "advance_lane_find.ipynb".  Here's an example of my output for this step.  (note: this is not actually from one of the test images)
<br>
<img src="./output_images/binary_threshold.png"/>
<br>
I used combined threshold(Sobel X and S channel of HLS Color space) here to create the thresholded binary image.

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `corners_unwrap()`, which appears in lines 17,71 in the file `advance_lane_find.ipynb` .  The `corners_unwrap()` function takes undistorted image as input.  I chose the hardcode the source and destination points in the following manner:
src = np.float32([[490, 482],[810, 482],
                      [1250, 720],[40, 720]])
dst = np.float32([[0, 0], [1280, 0], 
                     [1250, 720],[40, 720]])


I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.
<br>
<img src="./output_images/perspective.png"/>
<br>

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I tried two different approaches to find the lane-line pixels. I first started with Sliding window approach which uses convolution. But it doesn't do well on the test images. Using the sliding window method which uses peaks in histogram to identify the lanes was more successful and I was able to find the polynomial fit. Now I applied continoues fit using the previous line position. This is shown in lines 37,79,21,38

<br>
<img src="./output_images/lane_fitting.png"/>
<br>
<br>
<img src="./output_images/continous_fit.png"/>
<br>


#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

This can be found at lines 42 through 84  in `advance_lane_find.ipynb`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `advance_lane_find.ipynb` in the function `plot_original_image()`.  Here is an example of my result on a test image:

<br>
<img src="./output_images/final_image.png"/>
<br>


---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

You can find the output video "output_video.mp4" in the directory output_video.
<video width="400" controls>
  <source src="output_video/output_video.mp4" type="video/mp4">
</video>
 
---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

## Techniques:
1) I computed the camera calibration matrix and distortion coefficients using the set of chessboard images.
2) I applied a distortion correction to raw images.
3) I used a combination of color and gradient thresholds to generate a binary image.
4) I applied a perspective transform to rectify binary image ("birds-eye view").
5) I used sliding window(histogram) to detect lane pixels and fit to find the lane boundary.
6) I found the radius of curvature of the lane and vehicle position with respect to center.
7) And finally after detecting the lane I Warp the detected lane boundaries back onto the original image and used open cv's fillPoly function to fill the boundaries.

The major challenge was finding the lane lines. I feel the  method that was used is kind of robust but some things aren't accurate.
