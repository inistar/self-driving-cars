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

[image1]: ./output_images/undistort_output.png "Undistorted"
[image2]: ./output_images/test1.jpg "Road Transformed"
[image3]: ./output_images/binary_combo_example.jpg "Binary Example"
[image4]: ./output_images/warped_straight_lines.jpg "Warp Example"
[image7]: ./output_images/fit_poly.jpg "Fit Polynomial"
[image5]: ./output_images/color_fit_lines.jpg "Fit Visual"
[image6]: ./output_images/example_output.jpg "Output"
[video1]: ./output_image/project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in `compute_camera_calibration` function of the IPython notebook located in "./examples/solution.ipynb".

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

The code for this step is contained in `cal_undistort` function of the IPython notebook located in "./examples/solution.ipynb".

Once the camera calibration is performed and `objpoints` and `imgpoints` are avaiable, uses these variables to correct the image. Read the image into grayscale and calibrateCamera to retrieve the `ret`, `mtx`, and `dist` variables. Based the values, undistort the image. 

![alt text][image1]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

The code for this step is contained in `binary_image` function of the IPython notebook located in "./examples/solution.ipynb".

I used a combination of color and magnitude thresholds to generate the binary image. Lines 11 to 23 show the process of getting the gradients of both the x and y and calculating the gradient magnitude. Lines 4 to 5 and 27 to 28 show the process of getting the HLS channel and getting the thresholds. The end result is the combination of the two techniques. 

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for this step is contained in `warp_image` function of the IPython notebook located in "./examples/solution.ipynb". Image is hardcoded as follows:
```python
src = np.float32(
                        [[590, 450],
                         [685, 450],
                         [230, 700],
                         [1070, 700]])

dest = np.float32(
                        [[400, 5],
                         [1000, 5],
                         [400, 700],
                         [1000, 700]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 590, 450      | 400, 5        | 
| 685, 450      | 1000, 5       |
| 230, 700      | 400, 700      |
| 1000, 700     | 1000, 700     |

I verified that my perspective transform is working as expected by drawing the `src` and `dst` points by ploting the points onto the test image so that they appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The code for this step is contained in `find_lane_pixels` and `fit_polynomial` functions of the IPython notebook located in "./examples/solution.ipynb".

Based on the binary image, the algorithm plots the points on a histogram to find the two lanes based ont the 2 highest peaks. By splitting the left and right lanes, the algorithm uses windows to reposition based on the mean of points within the square. The result points are later used to plot the polynomial. 


![alt text][image7]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The code for this step is contained in `measure_curvature_real` and `center_offset` functions of the IPython notebook located in "./examples/solution.ipynb".

The x and y dimesions first get turned into actual pixels. Using the left and right lanes found in the previous step, I use the polyfit to to find the acutal line in meters. Using the r_curvature formula, calculate the the radius of the curvature.

To find the center, split the image based on the lane and the image. Get the difference of the middle minus the acutal middle of the lane. 

![alt text][image6]

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The code for this step is contained in `unwarp_image` and `draw_detection` functions of the IPython notebook located in "./examples/solution.ipynb".

Draw detection draws a polygon based on the highest and lowest values of x and y as a green layer to indicate the lane detection. The unwarp function transforms the green layer back to the original image. 


![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result][video1]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The pipeline implementation overall is performing well. Some improvements that could be made are to tune magnitude threshold so that it does not pick up so much noise. The warp_image function is also not exactly picking up the lane exactly as is. Being able capture the image at the top to not have huge lanes will help with the accuracy of the polyfit. 
