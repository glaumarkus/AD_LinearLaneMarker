[//]: # (Image References)

[img_grey]: ./transformation_examples/gray.png "Grayscale"
[img_blur]: ./transformation_examples/blur.png "Gaussian Blur"
[img_canny]: ./transformation_examples/canny.png "Canny Edge Detection"
[img_roi]: ./transformation_examples/roi.png "Region of Interest"
[gif_result]: ./transformation_examples/intro.gif "Road Lane Detection"
[gif_houghlines]: ./transformation_examples/houghlines.gif "Hough Lines on Road"
[gif_wiggle]: ./transformation_examples/wiggle.gif "Full Line on Road"
[gif_wiggle_vs_smoothed]: ./transformation_examples/wiggle_vs_smoothed.gif "Normal vs Averaged Line"

# **Finding Lane Lines on the Road** 

### Overview

As one of the first steps to helping our cars understand the concept of autonomous driving, we need to give them similar inputs to what we, when driving, percieve. One of these inputs comes from our eyes, which is then processed within our brain to identiy what action to do next. This project focusses on processing images or videostreams to identify the road lanes with Canny Line Detection on a highway. These lines can be displayed as raw Hough-Lines or full drawn lines. This project serves as a general starting point for further lane finding projects, to enable our car to take actions based on the image input.


<p align="center">
	<img src="/transformation_examples/intro.gif" alt="result"
	title="result"  />
</p>


---

### Transformation

For image processing several steps are applied to raw input:
1.	To reduce dimensionality, the image is converted to grayscale
2.	A guassian blur helps to soften the edges of visible objects 
3.	canny edge detection is applied to the softened image
4.	Finally the processed image is then cropped, to narrow down the range of detectable objects within the road in perspective to the car

<p>
	<img src="/transformation_examples/gray.png" alt="Grayscale"
		title="Grayscale" height="220" alt="Grayscale"/>
	<img src="/transformation_examples/blur.png" alt="Grayscale"
		title="Grayscale" height="220" alt="Grayscale"/>
	<img src="/transformation_examples/canny.png" alt="Grayscale"
		title="Grayscale" height="220" alt="Grayscale"/>
	<img src="/transformation_examples/roi.png" alt="Grayscale"
		title="Grayscale" height="220" alt="Grayscale"/>
	
</p>
	
The calculated hough lines serve as input for correctly identifying the correct road lanes. Lines with similar slope and intercept are likely to be the same lane, therefore the highest occurring positive and negative slope are the most likely lanes and are used to calculate an approximate lane for the lane. 


<p align="center">
	<img src="/transformation_examples/houghlines.gif" alt="houghlines"
	title="houghlines"  />
</p>

When transitioning from image to video the applied pipelines can be plotted over the images of the frames. As can be seen in the example below, the drawn lines wiggle somehow due to changing the perspective while driving. 

<p align="center">
	<img src="/transformation_examples/wiggle.gif" alt="wiggle"
	title="wiggle"  />
</p>

The main advantage from image to video comes from using the output of the last frames to validate current input. As a further improvement to archive more consistent result, the information of slope and intersect is stored between images from video streams. Even if you change your position on the road, the lanes should be in close proximity to the last observation. If for some reason no new distinct new lines can be found in an image, the last known lane will be shown in the picture. Also the new line will always be averaged against the last lane, to smooth the transition and avoid wiggling lanes. 

<p align="center">
	<img src="/transformation_examples/wiggle_vs_smoothed.gif" alt="comparison"
	title="comparison"  />
</p>

---

### Shortcomings and possible extensions


Since this is a linear fitting, it will struggle to be accurate within curves, driving up hills or down a valley, since the region of interest is always fixed at 40% of screen. In both cases the calculated lane will show a greater variance to the actual line than in the repository examples. One simple improvement for curves fitting is to narrow down the region of interest closer to the bottom, since exponential functions get more slope the further up they are and therefore the averaging will increase the variance of distance from projected lane versus actual lane. For curves a separate fitting algorithm could then be implemented. Comparing variances of two drawn functions to all points enable a check which fitting is required. 

Same could be done when the perspective of hills and valley either opens up and the same distance in meters is actually further up in the picture. One could think of counting the average lines that hough lines detect and increasing the region, when fewer lines are found (therefore driving up) and narrowing the region when more lines are found. 

Cars or other objects crossing the lane will also cause distortion within the processed image, since the program is not able to distinct between the objects that create the hough lines. Therefore the input of this lane should be narrowed down to prevent wiggling of the lane. Additionally one could use the information of the other lane that is not crossed, to calculate an approximate lane for the segment that is crossed. 
