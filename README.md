![alt text](https://github.com/Tak-Au/Finding-Lane-Lines-on-the-Road/blob/master/test_images_output/original.png "Original image")
My pipeline consisted of following steps. First I convert the image from BRG format to HSV.  I created a yellow mask and a white mask.  Using the masks, I filter out all the pixel that doesn’t meet the mask and I will get the outline of the road only.  
![alt text](https://github.com/Tak-Au/Finding-Lane-Lines-on-the-Road/blob/master/test_images_output/color%20filter.png "filtered image")
I convert the image after the previous filter to grayscale.  From the grayscale image, I am able to use canny edge detector to locate all the edges.  The edges will be indicated in pixels.  
![alt text](https://github.com/Tak-Au/Finding-Lane-Lines-on-the-Road/blob/master/test_images_output/Canny.png "Canny image")
I will need to convert the edges in format of pixels into line segments with xy coordinate endpoints.  But before I do that, I define region of interest (ROI) in the image and black out the remainder of the region.  Once I filtered out only the region of interest, I can apply hough transformation to convert edges in pixels into line segments.  
Once, I get all the line segments from hough transformation, I have to filter the line segments into 3 groups: left, right, and ignore using the slope of the line.  The thinking is that the left and right line will be in some acceptable range so any line that is not within the range can be ignore.  At first, I assumed the range to be >25 degree or -<25 degree.  
From the left and right line segments bin, I use the numpy.poly fit function to fit the end points into line, the function will return the values of m (slope) and b (intercept) for both left and right line.  From the values, I compute the points when y is equal to the bottom of the image and to the middle of the image.  
![alt text](https://github.com/Tak-Au/Finding-Lane-Lines-on-the-Road/blob/master/test_images_output/hough.png "Hough with line fitting image")

Then I draw the line in the unprocessed image 

![alt text](https://github.com/Tak-Au/Finding-Lane-Lines-on-the-Road/blob/master/test_images_output/result.png "Final result")

One addition did was that I stored the slope of the (2) lines in degree and the lines itself to global variables.  The reason I did that is because on the next frame, I can recall these variables value.  Previously, I assumed the range of the slope to be > 25 degree or -<25 degree, but now that I know exactly, what slope that was previously compute. I make the assumption that the slope in the next frame will not change dramatically; therefore, I can refine the line segment filter to be more precise.
Also, I redefine the ROI so that it will be +/-50 pixels away from the previous computed lines.  So that on next frame, the result will be more accurate.

The videos where the pipeline processed can be found here.
[Test videos output](https://github.com/Tak-Au/Finding-Lane-Lines-on-the-Road/tree/master/test_videos_output)

The shortcoming with my current pipeline is that it doesn’t work well all the time, since the first filter works only for yellow and white lane lines, this pipeline will fail if the lane line are in different color.  Also, shadow and light levels can affect the performance of the filter because shadow can make white less white.  The filter does account for some variation but it doesn’t work under extreme lighting condition.  I did address this short coming in the pipeline by determining if at least one line is detected.  If at least one line is detected but the other one is not, the code will approximate where that missing line by using previous line data and the new line detected position since it is safe to assume that the left and the right lines are in fixed width apart.    

One improvement of this pipeline is to remove the color filter and try to make it work without it.  This did work for the first 2 videos but it failed in the challenge video.  This was due to the fact that the shadow caused the canny detector to fail.  I believe this can be overcome by using image processing. 
