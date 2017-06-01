# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First,

    1. Convert the image to grayscale image
        Black/white image, a 2 dimensional matrix of singular brightness values,
        is more robust to lighting variations  than color image is.

    2. Then Apply Gaussian Smoothing on the grayscale image
        Gaussin smoothing will give a blur image. Reason for this is 
        i. Downsampling
        ii. noise reduction
        Here Each of the pixel is the sum of its weighted neighbouring pixel,
        larger the neighbour more the blurring effect

    3. Apply Canny Edge detection on image at step 2 above.
        Rapid change in brightness are where we find the edge. In addition to 
        gradient magnitude canny edge detection traces area, find local maxima
        and tries to connect them in a way that there is just a single edge.
        When multiple edges meet will create a hole.

    4. Apply mask of ROI (Region of Interest) on image at step 3 above.
        Mask the edge detected image with an interested region by looking into the image.
    
    5. Apply Hough transform
        Transform the image from image space to hough space to find lines from Canny 
        Edge detected image within the masked region of the image at step 4 above.
        Here Hough transform will return an array of lines which will be drawn into
        a copied version of the original image of same size and channel number

    lastly the image at step 5 and the original image will be weighted sum to output
    the final image.


In order to draw a single line on the left and right lanes, I modified the draw_lines() function by
    
    1. for each line from the given Hough line array of each frame(/image),
         i.  fit one straight line using the first order equation, y = mx + b. polyfit from numpy
              is used to fit such line and find the corresponding slope(m) and the coefficient(b)
         ii. consider the slops between 20 degree (0.35 radian) to 49 degree (0.85 degree) and their
              corresponding lines (x1s,y1s,x2s,y2s)
         iii.if the lines(x1 and x2 co-ordinate  are on the right of the middle of the image with 
              slope > 0; these are the lines on right side and can be use to draw the longest line
              on right lane. Save these lines and slopes
         iv. find the lowest of y and x from the saved lines at step iii) as this will give a longer
              line extrapolated from bottom right.
              Here also normalize the lowest y and x by correcting them over 2 previous frames's lowest
              y and x to overcome any outlier.
         v.  take the median of the slope from the saved slopes at step iv) and calculate x2 (bottom right)
             from it and draw the line
            
         vi. if the lines (x1, x2 co-ordinate) are on the left of the middle of the image with slope < 0;
             these are the lines on left side and can be use to draw the line on left lane, Save these 
             lines and slopes. Similarly follow step iv) to draw the longest line on left lane.




If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when 
  1. there is a sharp turned on the road, first order polynomial is no
     more a good fit on such case.

   
Another shortcoming could be ...

  2. sensitive in selecting the mask of interested region

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...
 1. use a higher order of polynomial incase of a sharp turn but then how to
    detect a sharp turn ?
    may be a larger difference among slopes of successive cached frames

Another potential improvement could be to ...
 2. Cache more frames and normalize the lowest x,y across them.

