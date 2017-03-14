# **Finding Lane Lines on the Road** 

The goal of this project is to identify lane lines in videos. To accomplish this goal, these are the steps taken:

1. Identify lane lines in an image
2. Build a pipeline function to process consecutive image stream
3. Apply the pipeline to a video to mark lane lines consecutively

All the required files are contained in the [Github repository](https://github.com/frank-xiangyu-liao/CarND-Term1-Lane-Lines). It also includes an [exported HTML file](./P1.html) of the notebook for viewing pleasure.

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

### Pipeline for Lane Lines Identification

#### Pipeline Steps

My pipeline consisted of the following steps:

1. Converting the original RGB image into HSV image. Here, instead of using the grayscale conversion recommended in the course, I found that HSV yielded better result when identifying yellow lane lines.
![HSV]
2. Find yellow and white colors using openCV.inRange(). The Hue, Satauration and Value(brightness) parameters are set based on multiple tries and suggestions from [Medium](https://medium.com/) and [Slack](https://carnd.slack.com/messages/p-lane-lines).
![yellow]
![white]
3. Combind the yellow and white HSV images in step 2 into a single image.
![gray]
4. Apply region-of-interest mask to image in step 4 to rule out noise outside the lane lines region.
![masked_gray]
5. Using CannyEdge to find the edges in the picture. The high and low threshold are set to be 192, 64 based on multiple tries.
![edges]
6. Apply Hough transformation to find lane lines. draw_lines() are modified to draw only two lane lines on the image, after averaging and extrapolate the lines/points found by hough transformation. 
![hough]
Here's what the final image in the video looks like (combining the original image with lane lines):
![final]

#### Modification of the draw_lines() function

Instead of drawing all the Hough lines on the image, two averaged (left and right) lane lines are calculated using the following algorithm:

1. For each line segment found using Hough transform, its slope is calculated. If the slope is less than 0 & it's in the left half of the image(X1 < width/2 & X2 < width/2), than it's an element of the left lane line; on the contrary, lines that have slope greater than 0 and are in the right half of the image is an element of the right lane.

	Lines that has infinite or zero slope are ruled out; since lane lines can't be horizontal or perpendicular.
2. After dividing Hough lines into left and right lane segments, x and y points of left and right lane segments  are gathered into separate array.

3. Use numpy.polyfit() function with the x and y coordinate array as inputs to find the average lane line for left and right side. Two final lanes are drawn on the image.

---

### Reflections

### 1. Shortcomings

The pipeline works fine for the video with solid white lane on the right; for the one with yellow solid lane on the left, the pipeline can accurately annotate left and right lanes throughout the most part, but has glitches (deviated lane lines) for a few frames. In my opinion, it reflects the following shortcomings of my pipeline:

1. In my pipeline, the accuracy of the numpy.polyfit() function used in the draw_lines() function heavily depends on the accuracy of the Hough lines. The underlying assumption is that: the lines identified by Hough algorithm are in-lined with the lane lines. 

	If even a few number of lines lie out of the lane line region, this will yield considerable deviation/noise, as polyfit function takes into account the estrayed points, causing the final extrapolated lane lines deviated from the right result.

2. With the above point being mentioned, it also means that the pre-processing(before apply Canny and Hough) of the image needs refinement. Deviation of the final lane lines proves that there're still noises included in Canny edge finding and Hough lines identification, which mislead the algorithm to a fault result. Left/right lane lines segments separation by simply using the positivity and negativity of the line slope and X coordinates is a bit simple and 'naive'.

3. Hard-coded region of interest (directly set the points of the vertices of the region based on image size and use it throughout the stream). That's why the lane lines yielded from my pipeline is a little bit too short: to rule out all the undesired noises with a fixed mask, it needs to be aggressive enough, and that compromises some lane lines that should've been in the image.

### 2. Future Improvements

Based on the shortcomings listed above, the following improvements can be made:

1. In draw_lines() function, a more competent algorithm should be implemented to eliminate the lines/points outside of the lane line region. A range of slope can be set for left and right lane segments, which rules out the line segments that have slope out of this range; or, a dynamic average slope/lane line can be tracked, and the slope of an incoming line segment is compared against this value to be determined if it should be a left lane line, a right lane line, or to be ruled out.

2. Soft/dynamic region of interest. An algorithm can be added to dynamically change the region of interest based on the Hough lines results, so that it can roughly identify the lane lines region and end points, and then  apply the mask more precisely; that will also increase the accuracy of the polyfit() function used in the pipeline.

3. HSV conversion of the image works better than the grayscale conversion when dealing with yellow lanes, but still includes much noise when identifying white lanes. A joint approach of HSV conversion masking and grayscale conversion masking can be used to give better results; or other image processing technique can be adopted.  
