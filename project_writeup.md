#**Finding Lane Lines on the Road** 

The goal of this project is to identify lane lines in videos. To accomplish this goal, these are the steps taken:

1. Identify lane lines a given image
2. Build a pipeline function to process constant image stream
3. Appy the pipeline to a video to mark lane lines consecutively


[//]: # (Image References)

[image]: ./pipeline_image/iamge.jpg
[HSV]: ./pipeline_image/hsv.jpg
[yellow]: ./pipeline_image/yellow.jpg
[white]: ./pipeline_image/white.jpg
[masked_gray]: ./pipeline_image/masked_gray.jpg
[gray]: ./pipeline_image/gray.jpg
[edges]: ./pipeline_image/edges.jpg
[hough]: ./pipeline_image/hough.jpg
[final]: ./pipeline_image/final.jpg

---

### Reflection

###1. Pipeline for Lane Lines Identification

####The Pipeline

My pipeline consisted of the following steps:

1. Convert the original RGB image HSV image. Here, instead of using the grayscale conversion recommended in the course, I found that HSV yielded better result when identifying yellow lane lines.
![HSV]
2. Find yellow and white colors using openCV.inRange(). The paramaters to locate yellow/colors of the image hue, satauration and value(brightness) are based on multiple tries and help from Slack.
![yellow]
<center>Yellow</center>
![white]
<center>White</center>
3. Combind the yellow and white HSV images in step 2 into a single image.
![gray]
4. Apply region-of-interest mask to image in step 4 to rule out noise outside the lane lines region.
![masked_gray]
5. Using CannyEdge to find the edges in the picture. The high and low threshold are set to be 192, 64 based on multiple tries.
![edges]
6. Apply hough transformation to find lane lines. Here, draw_lines() are modified to draw only two lane lines on the image, after averaging and extrapolate the lines/points found by hough transformation. 
![hough]
Here's what the final image in the video looks like(combining the original image with lane lines):
![final]

####Modification of the draw_lines() function

Instead of drawing all the hough lines on the image, two averaged (left and right) lane lines are calculated using the following algorithm:

1. For each line found using hough transform, calculate the slope of it. If it's less than 0, than it's an element of the left lane line; on the contrary, line that has slope greater than 0 is an element of the right lane.

	Lines that has infinite or zero slope are ruled out; since lane lines can't be horizontal or perpendicular.
2. After dividing hough lines into left and right lane segments, x and y points of left lane segments and right lane segments are gathered into separate lists.

3. Use numpy.polyfit() function on the x and y coordinate list to find the average lane line for left and right side. Two final lanes are drawn on the image.



###2. Shortcomings



###3. Future Improvements