# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I apply gaussian_blur to remove any noise. Use the canny function to detect the sharp image changes to detect the edges. The masked_edges have some unrelvant data apart from the lanes. The region_of_interest takes vertices that cover only the lanes. I then use hough transform to find all the intersecting lines. The weighted_img fucntion is used to color the detected lanes. 
.... 

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by finding the left and right line based on the slope. After left and right lanes are split, I use polyfit to fit the lines based on left and right and get the top and bottom of the values. 

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when curvature is greater, the polyfit will not do a great job to fit the line. 

Another shortcoming could be the region of interest as it does not pick up some of the points when there is a lane curvature. 


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to clean up the outliers of the left and right lanes before performing a polyfit. 

Another potential improvement could be to make the region of interest to be more dynamic. When the lane is turning, some of points close to the middle are discarded. 
