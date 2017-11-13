# **Advanced Lane Finding Project**

The goals/steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to the center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)
[calibration1]: ./img/calb1.png
[calibration2]: ./img/calb2.png
[calibration3]: ./img/calb3.png

[image1]: ./img/chass_undistorted.png "chessboard"
[image2]: ./img/undistorted.png "Undistorted"

[image_color]: ./img/color_channel.png "Color_Channel"
[image3_3_th]: ./img/3x3_threshold.png

[image2]: ./test_images/test1.jpg "Road Transformed"

[image3]: ./img/DifferentMask.png "Different Mask"
[image4]: ./img/wraped_image.png "Warp Image"
[image4_1]: ./img/binary_wraped_image.png "Warp Binary Image"
[image4_2]: ./img/3x3_binary.png "3x3 Warp Binary Image"
[image5]: ./img/ploy_fit.png "Fit Visual"
[image6]: ./img/result_image.png "Output"
[video1]: ./video/project_video_output.mp4 "Video"

---

### Camera Calibration

#### 1. Briefly, state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion-corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located 1st code segment in "./Advanced_Lane_Finding_Project.ipynb"

I start by preparing "object points," which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
To see different color channel discrepancy, I visualize all color channel including RGB color space.

![][image_color]

The to find out each threshold function, I also visualize all different function include single threshold and the combination of x-y gradient and the combination of direction and magnitude.

![][image3_3_th]

In the end, I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at 4th code cell `Advanced_Lane_Finding_Project.ipynb`).  Here's an example of my output for this stage.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provided an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in the 6th code cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 45, img_size[1] / 2 + 90],
    [((img_size[0] / 6) - 10), img_size[1]-20],
    [(img_size[0] * 5 / 6) + 30, img_size[1]-20],
    [(img_size[0] / 2 + 45), img_size[1] / 2 + 90]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 595, 450      | 320, 0        | 
| 213.34, 700      | 320, 720      |
| 1096.67, 700     | 960, 720      |
| 685, 450      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to confirm that the lines appear parallel in the warped image.

![alt text][image4]
![alt text][image4_1]

I also visualize perspective transform result of different threshold binary images.
![alt text][image4_2]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I apply method `sliding window polyfit` to track lane-line pixels and find out positions polynomial.

First, it computes a histogram of the bottom half of the image and finds the most number of x position in the left and right lane lines.

It identified from the local maxima of the left and right halves of the histogram.

The function then uses ten windows from which to identify lane pixels, each one centered on the midpoint of the pixels from the window below. Then based on the midpoint, I used Numpy `polyfit()` to fits a second order polynomial of positions.

The image below demonstrates how this process works:
![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The curvature of the lane can be illustrated by the following picture.
![](https://i.imgur.com/OF4m0pN.png)

The radius of curvature can be written as the formula
![](https://i.imgur.com/TCC2Snj.png)

I did this in 10th code cell of my Ipython notebook in `Advanced_Lane_Finding_Project.ipynb`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in 12th code cell of Ipython notebook in `Advanced_Lane_Finding_Project.ipynb` in the function `pipeline()`.  Here is an example of my result of a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./video/project_video_output.mp4)

[![IMAGE ALT TEXT HERE](https://img.youtube.com/vi/p5B2-jFyc8Y/0.jpg)](https://www.youtube.com/watch?v=p5B2-jFyc8Y)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I first compute camera matrix to correct distortion for correct lane line projection. Then apply a perspective transform for the bird-eyed view. Finally, identify the lane point and fit second order polynomial of lane position.

As I finished single image detection, however, I can't have consist lane line detection on video. As a result, i modify `update_fits()` function and add `update_avg_coeffe()` to get the best fit and also consist of lane line polynomial. For `update_fits()` function, if there is too much difference, then it will use the previous fitting result. For `update_avg_coeffe()` function, it will average the newest ten fitting result to have display lane line on the picture.

The result of original `project_video.mp4` is great, nevertheless, when it processed the `challenge_video.mp4`, the result is not doing well. And it has to re-tune the parameter of image threshold such as gradient, magnitude and color value for good result. This tedious job takes time and energy, which makes me understand the strength of deep learning based on previous behavior clone project.

