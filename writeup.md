##Vehicle Detection Project

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./result_images/1.png
[image2]: ./result_images/2.png
[image3]: ./result_images/3.png
[image4]: ./result_images/4.png
[image5]: ./result_images/5.png
[image6]: ./result_images/6.png
[image7]: ./result_images/7.png
[image8]: ./result_images/8.png
[image9]: ./result_images/9.png


###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the third and fourth code cells of the IPython notebook.

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `RGB` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

####2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and it seemed the pipeline yielded the fastest process time and highest accuracy with 'YUV' color space, orientations = 11, 'pixels_per_cell=(16,16)' and 'cells_per_block=(2,2)'.

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using the example code provided in the lesson with X_train and y_train obtained with the above HOG parameters in seventh and eighth code cells. The result of the training is shown below:
![alt text][image3]


###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

In code cell 14 I have decided to use four sets of windows. These windows are in scales of 1, 1.5, 2, and 4. For the windows that have scales of 1, 16 pixels overlap. Windows with scales of 1.5 and 2 have 32 pixels that overlap, and windows with scales of 4 have 64 pixels that overlap. The reason behind these numbers is that I have noticed as the objects (cars) move further from the camera, towards the 400-pixel-line, they become smaller. So windows with bigger scales slide further down the image, thus ystop increases as scale increases. 

![alt text][image4]

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on four scales using YUV 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:
![alt text][image5]
![alt text][image6]

---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Video is included in the submission.


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are the selected frame and its corresponding heatmap:

![alt text][image6]
![alt text][image7]


### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from the selected frame:
![alt text][image8]

### Here the resulting bounding boxes are drawn onto the selected frame:
![alt text][image9]



---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

With limited number of training images, if the encountered vehicle does not match one of the images, the classification is most likely to fail. I noticed there are some false positives from the classification, which were mostly filtered out with thresholding. However, there still were some false positives that persisted through the thresholding. If the sliding windows were to be adjusted in size and placement, the result may be more accurate with the right thresholding. Also, more vehicle and non vehicle data may be used to augment the training data to obtain higher accuracy in classifying vehicles. 