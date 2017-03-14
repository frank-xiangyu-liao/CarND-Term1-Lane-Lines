#**Finding Lane Lines on the Road** 

The goal of this project is to identify lane lines in videos. To accomplish this goal, these are the steps taken:

1. Identify lane lines a given image
2. Build a pipeline function to process constant image stream
3. Appy the pipeline to a video to mark lane lines consecutively


[//]: # (Image References)

[image]: ./pipeline_image/iamge.jpg
[HSV]: ./pipeline_image/hsv.jpg

---

### Reflection

###1. Pipeline for Lane Lines Identification

####The Pipeline

My pipeline consisted of the following steps:

1. Convert the original RGB image HSV image. Here, instead of using the grayscale conversion recommended in the course, I found that HSV yielded better result when identifying yellow lane lines.

[HSV]

2. Find yellow and white colors using openCV.inRange(). The paramaters to locate yellow/colors of the image hue, satauration and value(brightness) are based on multiple tries and help from Slack.

3. Combind the yellow and white HSV images in step 2 into a single image.

4. Apply region_of_interest() to image in step 4 to rule out noise outside the lane lines region.

5. Using CannyEdge to find the edges in the picture. The high and low threshold are set to be 192, 64 based on multiple tries.

6. Apply hough_lines() to find lane lines. Here, draw_lines() are modified to draw only two lane lines on the image, after averaging and extrapolate the lines/points found by hough transformation. 

####Modification of the draw_lines() function

Instead of drawing all the hough lines on the image, two averaged (left and right) lane lines are calculated using the following algorithm:

1. For each line found in the 



###2. Shortcomings



###3. Future Improvements