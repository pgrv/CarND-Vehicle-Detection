## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/HOG.png
[image2]: ./output_images/slide_window.png
[image3]: ./output_images/find_cars_1.png
[image4]: ./output_images/find_cars_2.png
[image5]: ./output_images/find_cars_3.png
[image6]: ./output_images/find_cars_4.png
[image7]: ./output_images/find_cars_5.png
[image8]: ./output_images/find_cars_6.png
[image9]: ./output_images/heatmap_1.png
[image10]: ./output_images/heatmap_2.png
[image11]: ./output_images/heatmap_3.png
[image12]: ./output_images/heatmap_4.png
[image13]: ./output_images/heatmap_5.png
[image14]: ./output_images/heatmap_6.png
[image15]: ./output_images/labels_1.png
[image16]: ./output_images/labels_2.png
[image17]: ./output_images/labels_3.png
[image18]: ./output_images/labels_4.png
[image19]: ./output_images/labels_5.png
[image20]: ./output_images/labels_6.png
[video1]: ./project_video_output.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it.

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the sections "HOG Features" and "Extract features from list" of the IPython notebook.

I started by reading in all the `vehicle` and `non-vehicle` images. I then explored different color spaces and different HOG-parameters (`orientations`, `pixels_per_cell`, `cells_per_block` and `hog_channel`).
Here is an example using HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![HOG Images][image1]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters in the classroom and the best configuration I found is in the colorspace `YUV` with `orientations=10`, `pixels_per_cell=(12, 12)`,  `cells_per_block=(2, 2)` and `hog_channel = ALL`.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

In the section "Train classifier" I trained a linear SVM using random splitted training data and test data.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

In the section "Sliding window search" I implemented the same function for sliding window search as defined in the classroom (slide window, single_img_features, search_windows and draw_boxes). I scaled the window to 94X94 and the overlap to 0.8X0.8 by trying out which parameter gives the best results:

![Sliding window][image2]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YUV 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result. I use three different scaling factors whith different searching windows. Here are some example images:

![Find cars 1][image3]
![Find cars 2][image4]
![Find cars 3][image5]
![Find cars 4][image6]
![Find cars 5][image7]
![Find cars 6][image8]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)

Here's a [link to my video result](./project_video_output.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are six frames and their corresponding heatmaps:

![Heatmap 1][image9]
![Heatmap 2][image10]
![Heatmap 3][image11]
![Heatmap 4][image12]
![Heatmap 5][image13]
![Heatmap 6][image14]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames:
![Labels 1][image15]
![Labels 2][image16]
![Labels 3][image17]
![Labels 4][image18]
![Labels 5][image19]
![Labels 6][image20]


Because I wasn't happy with that results I have implemented a history similar to the last project (advanced lane detection) so that the heatmaps from the last 10 frames are added to the actual heatmap and the threshold value is higher by the half length of the history.

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

To make the pipeline more robust another filter than my "history" could be used.
The vehicle class could be added by more parameters (width and height of the bounding box, etc.). So that maybe the mean width and height of the last frames could be used to calculate the new bounding box widths and heights.
There are some false targets on the other road even if there was no vehicle. This must be improved.
Maybe other parameters for HOG could be lead to better results.

