# **Finding Lane Lines on the Road**

## Project Writeup

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

[//]: # (Image References)

<!-- [image1]: ./examples/grayscale.jpg "Grayscale" -->
[image1]: ./EditedImages/solidYellowLeft_ColourFiltered.jpg "Filtered Colours"
[image2]: ./EditedImages/solidYellowLeft_GrayscaleAndBlur.jpg "Grayscale and Blur"
[image3]: ./EditedImages/solidYellowLeft_CannyTransform.jpg "Canny Transform"
[image4]: ./EditedImages/solidYellowLeft_maskedEdges.jpg "Masked Edges"
[image5]: ./EditedImages/solidYellowLeft_HoughTransform_SmallLines.jpg "Hough Transform"
[image6]: ./EditedImages/solidYellowLeft_HoughTransform_SingeLine.jpg "Single Line"
[image7]: ./EditedImages/solidYellowLeft_mixedImage.jpg "Combined Image"

### Reflection

#### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

##### The basic elements of my pipeline where as follows:

1. Apply a colour filter to pull the white and yellow colours from the origianl image
2. Convert the image to grayscale and apply a Gaussian Filter to suppress noise in the image
3. Apply the Canny Transform to identify the edges in the image
4. Mask the image to the field of view
5. Find the lines in the image, using the Hough Transform, that connect the edges identified in  the Canny transform
6. Find single lines to represent the left and right lanes and draw them onto a blank image
7. Combine the original image with the image with the lane lines drawn on

###### 1.1 Colour Filtering
This was implimented to help fix one of the biggest problems raised by the Optional Challenge where a large number of extra edges where picked up by the Canny Transform such as the bonnet of the car. The colour selection was split into two, one for the whites and the other the yellows. It was done this way to potentially improve any shortcomings that can come from using one filter to select different colours. This results in an image with both the white and yelloe road markings

![alt text][image1]

###### 1.2 Grayscale and Gaussian filtering
These two steps helped improve the ability of the Canny edge detection to find the edges in the image. Th grayscale image is a requirement to `canny` function whilst the Gaussian filter removes any noise to better identify the edges (sharp gradients in the image). The filter would not want to be too large, blunting the sharpness of the edges and effecting the edge detection.

![alt text][image2]

###### 1.3 Canny Transform
The Canny Transform was used to find the edges within the remaining image. Adjustments had to be made to the thresholds from the quizzes to better find the edges in the images. These was played with until the best output was found

![alt text][image3]

###### 1.4 Image Masking
The field of view was adjusted to remove any edges that where found outside the lane lines which could be assumed as not relavent. The field of view had to be adjusted for the Optional Challenge by making it relative to the size of the image instead of a fixed size. The bottom corners of the mask also had to be brought in to stop any edges from passing cars in the other lanes from being accidentally used in the the Hough Transform.

![alt text][image4]

###### 1.5 Hough Transform
This was used to connect the points found in the Canny Transform. The input parameters had to be adjusted via trial and error to produce the optimal result. Later, when dealing with the light area in the Optional Challenge, the threshold was reduced to allow more points to be connected with a line. When moving into the lighter part of the video less points could be found which caused no lines to be found. The reduction in the threshold did cause problems with the splitting the lines between left and right. Fix explained in the next section

![alt text][image5]

###### 1.6 Single lines to represent Left and Right lanes
The sign of the lines gradient was used to the assign them to either the left or right lane line. However, due to the lower threshold, some smaller lines, that were part of cat eyes or small horizontal strips, could be accidentally assigned to the incorrect lane line. This was solved by not assigning any lines whos gradient was beneath 0.5 thus removing these smaller lines whilst still allowing small non-horizontal lines to be used in the tricky lighting images.

Another way to potentially solve this was to split the lines based upon which side of the image they were on. This was choosen against due to the curves in the road which could cause the road marking to be on the other side of the image thus leading to them being mis-classified.

The next step was to map a 1st order polynomial to find the coefficients of the lane lines. This was done using `numpy.polyfit`. Initially this was done by finding the mean of the identified lines and using that to calcuted the gradient and intercept. However the polynomial fit was found to be less jittery due to its filtering effect by using more values in the calculation.

The lines were then extrapolated using the found coefficients to the edge and the (slight less than) middle of the image. Then the lines were drawn onto a blank image

![alt text][image6]

###### 1.7 Image Combining
The image was then combined with the original image to produce the image with the lane lines drawn on.

![alt text][image7]

---

### 2. Identify potential shortcomings with your current pipeline

The current pipeline has plenty of shortcomings. To name a few:
* The tuning of the filters and transforms are very specific to the test images. This was very clear when given the Optional Challenge after tuning on the test videos. Any changes in lighting, shade of colours used for the road markings or night time where the road would be lit with spot lights could throw the tunnings off.
* The mapped lines are straight meaning they are unable to track turns in the road. This could be maybe solved by using a higher order polynomial to map the lines but would require the whole line including the bend to be picked up in the image.
* The lines, especially when tracking the dashed markings, are fairly jittery and jump around with any small accidental lines being picked up. This would pose problems if the lines were being used to the place the car in the center of the lane.
* The field of view could cause problems if the car was not on flat road. If heading up on a slope more false lines could be potentially picked up by the transforms and if heading down a slope not all of the lines available would be used.

---

### 3. Suggest possible improvements to your pipeline

* With jittery lines and the potential for a line to be pulled away by an accidental edge some filtering between frames could improve the final output. Road markings are fairly consistent once found so the negatives of filtering wouldn't be to damaging. This would also help if marking where lost in frames of high brightness
* As mentioned above, the straight line mapping of the lane lines doesn't account for turns in the road. Mapping a higher order polynomial or mapping to different parts of lane line could be an improvement. This does have the need of finding the whole line in the image which can be a problem with the dashed lines. However as the dashed line is always parallel to the solid line the solid line could be used to calculate the curved of the line whilst the dashed line used to place the curve. This wouldn't work when in the middle lanes of the highway however
* The transforms and filters required a bit of manual trial and error tuning which could, and probably will, cause problems when tried on different examples. Tunings like this will never hold up to a large array of examples and so an adaptive method based up brightness or similar or a machine learning based method would probably be a better approach. A general algorithm rather than a specific one would almost always be better in the long run.
