#**Finding Lane Lines on the Road** 

##V. Pavlov, based on "Writeup Template"


---



[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

###1. Requirements to the Processing steps (should be used for the unit tests.)
0.1 Import matplotlib.pyplot to drow the images
0.2 Import matplotlib.image to load the images
0.3 Import numpy to work with arrays and 
0.4 Import cv2 for OpenCV functionalty

####1. Load the recording and extract the single frame 
1.1 The name of recording should be a parameter.
1.2 All frames (images) should be processed successively.
1.3 The next steps should be applied for each frame in the recording.

####2. Convert the images to grayscale
2.1 OpenCV-method cv2.cvtColor() should be used.
2.2 Input is the image  from the Step 1.
2.3 Ouput is the image _blur_ _gray_.

####3. Apply the edge detection based on Canny method
3.1 OpenCV-method cv2.Canny() should be used.
3.2 Input is an output image _blur_ _gray_ from the Step 2.
3.3 Output is an image _edges_ with edges.
3.4 Lower and Upper Thresholds should be set to 50 pixels and 150 pixels respectively. 
3.5 Grey image from the step 2 should be used as an input.
3.6 OpenCV-method cv2.GaussianBlur() may be used before cv2.Canny() in order to reduce the outlier. 
3.7 Empirically detected kernel size for cv2.GaussianBlur() should be set to 5 pixels for both height and width.

####4. Define the area of the interest in the image (region masking) 
4.1 For better accuracy the OpenCV-method cv2.fillPoly() should be used.
4.2 Input is an output image _edges_ from the Step 3.
4.3 Output is an image _masked_ _edges_ with the same size as the input image.
4.4 The nodes of the mask polyline should be defined as ratio relative to imshape[0] and imshape[1] to avoid a mismatch by sequences with different image sizes.
4.5 The ratio values should be defined empiricaly. 
4.6 Color for the pixels out of the region _ignore_ _mask_ _color_ should be set to 255 (white)
4.7 The region is fixed for all videos as long as no dynamical ground detection applied.


####5. Apply the Hough transformation for line extraction
5.1 OpenCV-method cv2.HoughLinesP() should be used.
5.2 Input is an output image _masked_ _edges_ from the Step 4.
5.3 Output is an image _line_ _img_ with the approximated stright lines drown by means of (x,y)-coordinates in pixels for each endpoint.
5.4 The objects of interest are stright lines.
5.5 Origin for (x,y) is in the left upper corner of the image.
5.6 All following parameters are defined empirically.
5.7 Distance resolution of the Hough grid _rho_ should be set to 1 pixel 
5.8 Angular resolution of the Hough grid _theta_ should be set to pi/180 radians 
5.9 Minimum number of votes (intersections in Hough grid cell) _threshold_ should be set to 30 units in order to define long lines.
5.10 Minimum number of pixels making up a line _min_ _line_ _length_ should be set to 40 pixels to take 
5.11 Maximum gap between connectable line segments _max_ _line_ _gap_ should be set to 20 pixels.
5.12 OpenCV-method cv2.bitwiseand should be used to apply a mask to an image.

####6. Draw image and overlay the detected lines
6.1 Iterate over the output "lines" and draw lines on a blank image.
6.2 OpenCV-method cv2.addWeighted() should be used to overlay the lines on the edge image 
6.3 Inputs: the image _line_ _img_ from Step 5 and the image  from the Step 1.
6.4 Parameter for cv2.addWeighted() should be set to α=0.8, β=1., λ=0 in order to draw the overlay lines semi-transparent.
6.6 matplotlib.pyplot-method "imshow" should be used to show the result

####7. Average/extrapolate the line segments
7.0 In order to identifying the full extent of the lane the average and extrapolation steps are required.
7.1 For each line in array of the detected lines in Step 5 _slope_ = _((y2-y1)/(x2-x1))_ should be calculated.
7.2 In accordance with the slope value the (x1,y1), (x2,y2) should be assigned to the left or to the right side. By positive values of the slope (x1,y1), (x2,y2) should be assined to the left side, by negative values to the right side.
7.3 To reduce the number of outlines the tresholds _slope_ _min_ _left_ = 1 (tan(45 deg))and _slope_ _max_ _right_ = -1 (tan(45 deg)) for allowed slopes should be defined.
7.4 For linear regression the least squares polynomial fit (numpy.polyfit()) of the first order should be used.   


###2. Some shortcomings for the current pipeline

The represented alorithm :
- has no dynamic ground detection so by non-zero slope or by decalibrated camera sensor the maximum detection range will be reduced
- is restricted for the stright road and is not adaptive to the curvy lanes (circles, s-curves)
- bot dots as lane marking type can not be detected
- only data from the current snapshot are used (no tracking applied), so the detection will be noisy.  


###3. Possible improvements to pipeline

- define regions on the image with the different parameter sets for the edge detection 
- use more sophisticated lane models by Hough-transformation  
- use lane tracking to make the detection and classification more robust
- estimate ground plate for dynamical definition of the region of the interest
- add self calibration/blockage detection for the camera
- use odometry of the ego-vehicle in order to adjust the estimation to the vehicle vibrations and to allow the ego-motion estimation for the tracking