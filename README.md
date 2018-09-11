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
[homography]: ./writeup_images/homography.png "Homography"
[running_means]: ./writeup_images/running_means.png "Running means"

---

### Reflection

### 1. Pipeline description

My pipeline consisted of these steps:

#### 1. Convert images to HLS color model.
#### 2. Apply 'Canny' algorithm for saturation channel.
Goal is to detect lines on images with different brightness or images with shadows.
![alt text][canny]
#### 3. Region of interest. Select edges only in defined trapezoidal region.
![alt text][roi]
#### 4. Hough lines. Detect lines in ROI and suppress noised lines by threshold.
![alt text][hough_lines]
#### 5. Extrapolate hough lines and draw rulers. 
![alt text][ruler]
In order to draw a single line on the left and right lanes, I modified the draw_lines() in following way:
  - Calculate tangents for each hough line
  - Separate lines by tangents to left and right candidates
  - Draw candidates on binary image
  - Find coords of white points on binary image by ```np.nonzero()```
  - Apply polynomial regression for these coordinates by ```np.polyfit(x, y, degree)```. 
Now i have parameters ```m``` and ```b``` for line equation ```y=mx+b```
  - Draw each line by found equation

### 2. Potential shortcomings
- Cars with yellow or white colors appears in ROI
- Road turns in sharp angle
- Snow on the road
- Signs drawn on the road

### 3. Possible improvements of pipeline

- Stabilize slope and bias of found rulers by running means. You can see that running mean even with 5 last frames cuts off noisy peaks.
![alt text][running_means]

- Canny algorithm detects edges by 2 Sobel's operators: one for vertical edges, second for horizontal edges. Possible improvement would be to extend it to detect sloping edges. Similar techniques widely used in convolution layers of neural nets.

- Canny algorithm takes 2-D picture as input. Possible improvements would be to analyze RGB (or HLS) channels simultaneously by 3-D version of Sobel's operator

- We know shape of lane line: rectangle. Don't find edges - find rectangles. Possible solution is to draw yellow and white rectangles, apply homography transformation for these rectangles: with different angles, different scales. And find positions of these transformed rectangles on the picture by cross correlation algorithm.

- The opposite way is to rectify whole image from camera by homography transformation. Find short lane lines by simple algorithm, we know they are parallel (in most of cases :) ). Now we have at least 4 points to rectify plane along these lane lines. Then find rectangles by cross corellation in rectified plane to have more precise location of lane lines.
![alt text][homography]
