# Project 1 submission: Carnd : nd013-connect

## Finding Lane Lines on the Road
### Submission by: Sandeep Patil  

.
### Following are details of Project 1

---

**Finding Lane Lines on the Road**

The steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on work in a written report


[//]: # (Image References)

[image_p1_step1]: ./sample_images/p1_step1.jpg "pipeline 1 gray image"
[image_p1_step2]: ./sample_images/p1_step2.JPG "pipeline 1 blur image"
[image_p1_step3]: ./sample_images/p1_step3.jpg "pipeline 1 edge image"
[image_p1_step4]: ./sample_images/p1_step4.jpg "pipeline 1 masked image"
[image_p1_step5]: ./sample_images/p1_step5.jpg "pipeline 1 hough lines"
[image_p1_step6]: ./sample_images/p1_step6.jpg "pipeline 1 hough line overlap"
[image_p1_step7]: ./sample_images/p1_step7.jpg "pipeline 1 extended lines"
[image_p1_step8]: ./sample_images/p1_step8.jpg "pipeline 1 final image with extended lines"

[image_p1_ch_hough1]: ./sample_images/p1_ch_houghline1.jpg "pipeline 1 hough line ch_img 1"
[image_p1_ch_hough2]: ./sample_images/p1_ch_houghline2.jpg "pipeline 1 hough line ch_img 2"
[image_p1_ch_hough3]: ./sample_images/p1_ch_houghline3.jpg "pipeline 1 hough line ch_img 3"
[image_p1_ch_hough4]: ./sample_images/p1_ch_houghline4.jpg "pipeline 1 hough line ch_img 4"

[image_p1_ch_final1]: ./sample_images/p1_ch_final1.jpg "pipeline 1 final ch_img 1"
[image_p1_ch_final2]: ./sample_images/p1_ch_final2.jpg "pipeline 1 final ch_img 2"
[image_p1_ch_final3]: ./sample_images/p1_ch_final3.jpg "pipeline 1 final ch_img 3"
[image_p1_ch_final4]: ./sample_images/p1_ch_final4.jpg "pipeline 1 final ch_img 4"

[image_p2_ch_final1]: ./sample_images/p2_ch_final1.jpg "pipeline 2 final ch_img 1"
[image_p2_ch_final2]: ./sample_images/p2_ch_final2.jpg "pipeline 2 final ch_img 2"
[image_p2_ch_final3]: ./sample_images/p2_ch_final3.jpg "pipeline 2 final ch_img 3"
[image_p2_ch_final4]: ./sample_images/p2_ch_final4.jpg "pipeline 2 final ch_img 4"



### The project code 
---
Project code and outputs can be found in P1.ipynb.

***Please note that the notebook has become heavy (around 15 mb) due to lot of images into it.  So it will take a while to load it in browser.***


.
### Reflection
---
### 1. Pipeline description. 
#### A. Finding possible lines on images which would represent lanes. 
I have used Canny Edge detection and Hough transformation to find lane lines in images.

My pipeline consisted of 6 steps. Please see below images for reference.

1. Canny algorithm works on grayscale image, So first I converted the images to grayscale.
2. Then I applied Gaussian blur with kernel size equal to 5, this helps to reduce noise in gray image. 
3. On blur image I applied Canny edge detection algorithm with threshold range of 50 to 150.
In this we got clear lane lines in provided images.
4. Then I masked edge images to get area of interest. Here I used region_of_interest() helper function.

    To calculate coordinate points for region polygon (vertices) following steps followed.
 * For bottom 2 points I considered bottom corners of image
 * Height of region : I considered 40% portion from bottom of image, where we can see lanes in image. 
 * I placed top of region of interest (polygon) in mid 6% part of image.
5. Then I applied probabilistic Hough transformation to find line in interest area. 
Here the key parameters were min_line_length = 100 and max_line_gap = 50. This helped to elimiate small line which could create noise and only gate lines with big trail which obviously would fall on lane lines.
6. Next step is to overlap Hough lines with original image which is achieved by calling weighted_img() helper function.  
  
In all above steps I have  used all helper functions and also added my own function to render multiple images in one notebook cell. This helped me to see all types of variance in all images at one place. This helped me to fine tune parameters in less amount of time.  

|Image processing at different stage|
|--------------|--------------|
|Step 1 : graysale image|Step 2 : blur image |
|![Pipeline 1 graysale image][image_p1_step1] | ![Pipeline 1 blur image][image_p1_step2] |
|Step 3 : edge image|Step 4 : masked image |
|![Pipeline 1 edge image][image_p1_step3]|![Pipeline 1 masked image][image_p1_step4]|
|Step 5 : hough lines|Step 6 : hough line overlap |
|![Pipeline 1 hough lines][image_p1_step5]|![Pipeline 1 hough line overlap on original image][image_p1_step6]|


.
#### B. Finding distinct lines for left and right lane.
Up till now we were able to find all possible lines on lanes, however the main objective of project is to find 2 lines which would represent left lane and right lane in image in full extent. 

In order to draw a single line on the left and right lanes, I created new method the draw_lines1() by extending functionality of draw_lines(), in this I applied following approach.
1. First I calculated slope and intercept from 2 line points which are provided by Hough algorithm.
2. Then separate out lines in 2 lists based on their slope value. I found that negative slope corresponds to left lane and positive slope corresponds to right lane.
3. Then calculate average slope and intercept which would be final value for left and right line lane.
4. Based on average slope and intercept I calculated point coordinates on images such that line would touch bottom of image and would extend till 40% portion of image from bottom. This is achieve by calling new helper function get_point() with appropriate y values.   

|Extended lines | Final image with extended lines |
|---|---|
|![Pipeline 1 image with extended lines][image_p1_step7]|![Pipeline 1 final image with extended lines][image_p1_step8]|

#### C. Image processing for Video stream.

To do image processing for video stream I have put all above steps in process_image() function which get called in clip.fl_image() to add annotation to image. 

### 2. Shortcomings in current pipeline

After running above pipeline on challenge video I found that lane lines are running all over images and not overlapping with actual lanes.

Then I extracted few sample frames from challenge video and applied above pipeline and found following shortcomings.

|Hough lines with above pipeline | Final image of challenge video with extended lines |
|---|---|
|![Pipeline 1 hough lines ch_img1][image_p1_ch_hough1]| ![Pipeline 1 final ch_img1][image_p1_ch_final1]|
|![Pipeline 1 hough lines ch_img2][image_p1_ch_hough2]| ![Pipeline 1 final ch_img2][image_p1_ch_final2]|
|![Pipeline 1 hough lines ch_img3][image_p1_ch_hough3]| ![Pipeline 1 final ch_img3][image_p1_ch_final3]|
|![Pipeline 1 hough lines ch_img4][image_p1_ch_hough4]| ![Pipeline 1 final ch_img4][image_p1_ch_final4]|

1. The average slope and intercept are no way near to actual lane line slope and intercept. 
2. Yellow and white lanes not get properly detected in white patch of road
3. There are multiple horizontal white lines get detected in shadow portion (though lane lines can be seen)
4. Car bonnet producing horizontal lines at bottom portion. In white patch of road it is more highlighted may be due to glare. 
5. Road patch change from tar to concrete producing horizontal line.

### 3. New approach to resolve issues in challenge video

As suggested by Subodh and some sneak in to advance lane finding project I tried to resolve above problems by using HLS color space.

Following is approach.
1. Convert image to HLS color space.
2. Apply filters on S channel and H channel that would produce s_binary and h_binary.
3. The combination of s_binary and h_binary (bitwise & operation) helps to extact points correponds to lane.
4. Apply probablistic hough transformation on above combination to find lanes.


|Images with old pipeline (RGB based) | Images with new pipeline (HLS based) |
|---|---|
|![Pipeline 1 final ch_img1][image_p1_ch_final1]| ![Pipeline 2 final ch_img1][image_p2_ch_final1]|
|![Pipeline 1 final ch_img2][image_p1_ch_final2]| ![Pipeline 2 final ch_img2][image_p2_ch_final2]|
|![Pipeline 1 final ch_img3][image_p1_ch_final3]| ![Pipeline 2 final ch_img3][image_p2_ch_final3]|
|![Pipeline 1 final ch_img4][image_p1_ch_final4]| ![Pipeline 2 final ch_img4][image_p2_ch_final4]|

#### Findings with new pipeline
1. This new approach helps to find yellow lane line on white patch of road also.
2. However it cannot detect white lane in shadow region and where lane is not continuing in long stretch. 
3. As the video is on small curvature, lane lines do not match with lanes in faraway part of road. 


### 4. Possible improvements to pipeline

1. Use combination HLS color space based extraction as specified above and club it with canny edge detection based on RGB color space.
2. Use some method to find edges to find only vertical lines instead of horizontal lines.
3. Use some good region selection method which could be applied on curve lanes.
4. Use some method to create curve lane line instead of straight one.
