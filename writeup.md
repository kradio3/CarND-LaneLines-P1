# **Finding Lane Lines on the Road** 

## Writeup

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[canny]: ./writeup_images/canny.png "Canny over saturation"
[roi]: ./writeup_images/roi.png "Region of interest"
[hough_lines]: ./writeup_images/hough_lines.png "Hough lines"
[ruler]: ./writeup_images/rulers.png "Extrapolated rulers"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of these steps:

1. Convert images to HLS color model.
2. Apply 'Canny' algorithm for saturation channel.
Goal is to detect lines on images with different brightness or images with shadows.
![alt text][canny]
3. Region of interest. Select edges only in defined trapezoidal region.
![alt text][roi]
4. Hough lines. Detect lines in ROI and suppress noised lines by threshold.
![alt text][hough_lines]
5. Extrapolate hough lines and draw rulers. 
![alt text][ruler]
In order to draw a single line on the left and right lanes, I modified the draw_lines() in following way:
  - Calculate tangents for each hough line
  - Separate lines by tangents to left and right candidates
  - Draw candidates on binary image
  - Find coords of white points on binary image by ```np.nonzero()```
  - Apply polynomial regression for these coordinates by ```np.polyfit(x, y, degree)```. 
Now i have parameters ```m``` and ```b``` for line equation ```y=mx+b```
  - Draw each line by found equation

### 2. Identify potential shortcomings with your current pipeline
- Cars with yellow or white colors appears in ROI
- Road turns in sharp angle
- Snow on the road

### 3. Suggest possible improvements to your pipeline

- Canny algorithm detects edges by 2 Sobel's operators: one for vertical edges, second for horizontal edges. Possible improvement would be to extend it to detect sloping edges. Similar techniques widely used in convolution layers of neural nets.

- Canny algorithm takes 2-D picture as input. Possible improvements would be to analyze RGB (or HLS) channels simultaneously by 3-D version of Sobel's operator
