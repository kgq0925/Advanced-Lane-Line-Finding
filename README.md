# Advanced Lane Line Finding

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./writeup/image_example.png "example"
[image2]: ./writeup/lane_example.png "lane_example"
[image3]: ./writeup/pipeline.png "pipeline"
[image4]: ./writeup/warped.png "wraped"
[image5]: ./writeup/curve.png "curve"
[image6]: ./writeup/output.png "output"

## Rubric Points

### Writeup / README

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in "./CarND-Advanced-Lane-Finding.ipynb"

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `object_point` is just a replicated array of coordinates, and `object_points` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `image_points` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `object_points` and `image_points` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Apply a distortion correction to raw images.

Apply a distortion correction to a raw image by using the `cv2.undistort()` function.

![alt text][image2]

#### 2. Create a thresholded binary image

I used a combination of color and gradient thresholds to generate a binary image (in the 7th code cell of the IPython notebook located in "./CarND-Advanced-Lane-Finding.ipynb").  Here's an example of my output for this step. 
![alt text][image3]

#### 3. Perspective Transformation

The code for my perspective transform includes a function called `transformer()`(in the 10th code cell of the IPython notebook located in "./CarND-Advanced-Lane-Finding.ipynb").  The `transformer()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
corners = np.float32([[253, 697],[585,456],[700, 456],[1061,690]])
new_top_left = np.array([corners[0, 0], 0])
new_top_right = np.array([corners[3, 0], 0])
offset = [50, 0]

img_size = (image.shape[1], image.shape[0])
src = np.float32([corners[0], corners[1], corners[2], corners[3]])
dst = np.float32([corners[0] + offset, new_top_left + offset, new_top_right - offset, corners[3] - offset])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 253, 697      | 303, 697      | 
| 585, 456      | 303, 0        |
| 700, 456      | 1011, 0       |
| 1061, 690     | 1011, 690     |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Find Lane Line

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this(in the 14th and 15th code cell of the IPython notebook located in "./CarND-Advanced-Lane-Finding.ipynb"):

![alt text][image5]

#### 5. Calculated the curvature of the lane and the position of the vehicle with respect to center.

I did this in the 14th and 15th code cell of the IPython notebook located in "./CarND-Advanced-Lane-Finding.ipynb"

#### 6. Plot Lane Line

I implemented this step in the 14th and 15th code cell of the IPython notebook located in "./CarND-Advanced-Lane-Finding.ipynb" in the function `process()`.  Here is an example of my result on a test image:

![alt text][image6]

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

The file is located in "./output_videos/project_video.mp4".

### Discussion

#### Pipeline

First, apply distortion correction on images.

Next, convert images from RGB color space to HLS color space. The performence of HLS is more stable than RGB in most situation.

Next, apply gradient direction operation. It picks up the lane lines well.

Next, apply transforms. This will be useful for calculating the lane curvature.

Finally, detect lane lines and calculate the lane curvature.

#### Discussion

The pipeline is only test on "project_video.mp4", which is a simple situation. It may failed in other complicated situations.

This is a traditional computer vision approach and took time to tune the parameters. Can machine learning help us to  find lane lines? In the future, I will explore the machine learning approach and combine both the machine learning approach and the traditional approach.
