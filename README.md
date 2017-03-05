##**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car_not_car.png
[image2]: ./examples/HOG_example.jpg
[image3]: ./examples/sliding_window.png
[image4]: ./examples/detection1.png
[image5]: ./examples/detection2.png
[video1]: ./project_video_output.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

I read all the `vehicle` and `non-vehicle` images from the provided dataset. Here is an example of one of each of the `vehicle` and `non-vehicle` classes: 

![alt text][image1]

I used the scikit-image library's `hog()` function, with the following configuration:
`orientations = 9`
`pixels per cell = (8, 8)`
`cells per block = (2, 2)`

The code for this step can be found under the `Combined Features` heading in the IPython notebook `CarND-Vehicle-Detection`.

####2. Explain how you settled on your final choice of HOG parameters.

After implementing the entire pipeline, I experimented with different hog parameters and finally decided on the parameters that gave high test accuracy for the SVM classifier.

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I used scikit-learn's `svm.SVC()` function to train an SVM model. The feature vector consisted of hog features, spatial color features and color histogram features. The images were converted from RGB color space to YCrCb color space before extracting the features.

The code for this step can be found under the `Combined Features` heading in the IPython notebook `CarND-Vehicle-Detection`.

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

The code for this step can be found under `Processing Pipeline` heading.
I decided to restrict my search to the area in the image where a car can be present (400 to 656 along the y-axis). The window size and overlap where the same as those used in the lesson since they worked well for detecting vehicles.	

![alt text][image3]

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

I optimized the performance of my classifier by tweaking the parameters hog, spatial and histogram color features. I applied a threshold for the number of overlapping detections to reduce false positives. 
Here are some example images and their corresponding heatmaps to show the overlap in bounding boxes:

![alt text][image4]
![alt text][image5]

---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)

Here's a [link to my video result](./project_video.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

To improve vehicle detection I maintained a deque with bounding boxes for the last 30 video frames and applied a threshold on the integrated heatmap to reduce false postives. 

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

When two cars are in close proximity to each other they are detected as a single car. To resolve this issue I would like to detect if the region which is hot in the heatmap has two hot centroids instead of one, signalling the presence of two vehicles. Using smaller sliding windows may also help in this case.

