##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.
 

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.

You're reading it!

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

Firstly, i loaded all the images and then differntiated saying `Car` and `No-Car` images. 

![alt text][./output_images/classify_car_No_car.png]

The code for extracting HOG features from an image is defined by the method Hog_features_extract and is contained in the cell titled "Convert Image to Histogram of Oriented Gradients (HOG)."
The figure below shows the HOG image.


![alt text][./output_images/HOG.png]

####2. Explain how you settled on your final choice of HOG parameters.

I settled on my final choice of HOG parameters based upon the performance of the SVM classifier produced using them. 
I considered not only the accuracy with which the classifier made predictions on the test dataset, but also the speed at which the classifier is able to make predictions.


####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM with the default classifier parameters and using HOG features alone  and was able to achieve a test accuracy of 98.06%.

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to search random window positions at random scales all over the image and came up with this.

![alt text][./output_images/Sliding_Windows.png]
![alt text][./output_images/Heatmap.png]
![alt text][./output_images/Label_map.png]


####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

I began by optimizing the SVM classifier. The original classifier used HOG features from the YUV Y channel only.
Using all three YUV channels increased the accuracy to 98.40%, but also tripled the execution time.
However, changing the pixels_per_cell parameter from 8 to 16 produced a roughly ten-fold increase in execution speed with minimal cost to accuracy.

![alt text][./output_images/Sliding_Window.png]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
[link to my video result](./project_output_video.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

The code for processing frames of video is contained in the cell titled "Pipeline for Processing Video Frames" and is identical to the code for processing a single image described above,
with the exception of storing the detections (returned by Search_cars) from the previous 15 frames of video using the prev_rects parameter from a class called Vehicle_Detect. Rather than
performing the heatmap/threshold/label steps for the current frame's detections, the detections for the past 15 frames are combined and added to the heatmap and the threshold for the
heatmap is set to 1 + len(det.prev_rects)//2 



---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Detection Accuracy was my major concern.Balancing the accuracy of the classifier with execution speed was crucial. Scanning 190 windows using a classifier that achieves 98% accuracy
should result in around 4 misidentified windows per frame. Of course, integrating detections from previous frames mitigates the effect of the misclassifications, but it also introduces
another problem: vehicles that significantly change position from one frame to the next (e.g. oncoming traffic) will tend to escape being labeled.

The code for processing frames of video is contained in the cell titled "Pipeline for Processing Video Frames" and is identical to the code for processing a single image described above,
with the exception of storing the detections (returned by find_cars) from the previous 15 frames of video using the prev_rects parameter from a class called Vehicle_Detect. Rather than
performing the heatmap/threshold/label steps for the current frame's detections, the detections for the past 15 frames are combined and added to the heatmap and the threshold for the
heatmap is set to 1 + len(det.prev_rects)//2.

And to make it more robust we should determine vehicle location and speed to predict its location in consecutive frames and use convolution neural network for sliding window search. 

