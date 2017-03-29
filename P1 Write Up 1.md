# **Finding Lane Lines on the Road**

## Project Writeup

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"


### Reflection

#### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

##### The basic elements of my pipeline where as follows:

1. Apply a colour filter to pull the white and yellow colours from the origianl image
2. Convert the image to grayscale
3. Apply an gaussian filter to the image to suppress noise in the image and improve the edge detection
4. Apply the Canny Transform to identify the edges in the image
5. Mask the image to the field of view
6. Find the lines in the image that conect the edges identified in from the Canny transform using the Hough Transform
7. Find a single lines to represent the left and right lanes and draw the line onto a blank image
8. Combine the original image with the blank imagine with the lane lines drawn on

##### In order to draw a single line on the left and right lanes, I modified the draw_lines() function to do the following:

1. Split the lines into left and right by checking the sign of the gradient. This was choosen over splitting the lines by the side of the image they were on as that method could run into problems when travelling around a corner. The lines were also filtered out if their gradient was too low to stop essentially straight lines on one side being attributed to the line on the other side. This would occassionaly cause the lines to jump to the other side of the screen
2. Map a 1st order line to the points representing the lines to find the coefficients of the found lane lines. This was initially done by finding the mean of all of the points and finding the graident and intercept of that. However the fitting of the 1st order line using `numpy.polyfit` was choosen as the result was more stable due to it taking all of the points into consideration for the calculation.
3. Extrapolate the lane lines using the gradient and intercept and select points on the image

##### Improvements made to attempt the Optional Challenge (unfortunately not successful):

* The first challenge was from the video being a different size image to the other test videos. This caused the image mask to be off and so the lines could not be piced up properly. This was solved by making the window size relative to the image size
* The colour filter was implimented to reduce the number of edges picked up within the window such as the bonnet of the car. The colour selection was split in two, one to pick up the white and the other yellow. This was done to better tune the filters to their respective colours. Whether it made much difference im not sure
* Reduce the threshold for the hough transform


If you'd like to include images to show how the pipeline works, here is how to include an image:

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline

The current pipeline has plenty of shortcomings. To name a few:
* The tuning of the filters and transforms are very specific to the test images. This was very clear when given the Optional Challenge after tuning on the test videos.
* The mapped lines are straight meaning they are unable to track turns in the road.


One potential shortcoming would be what would happen when ...

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

* Use some filtering between video frames to stabilise the drawn lines further
* Track the lane lines better when they are curved
* Adaptive tunning to find the lines if they cant be found or something

A possible improvement would be to ...

Another potential improvement could be to ...
