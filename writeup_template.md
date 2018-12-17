# **Finding Lane Lines on the Road** 
Identifying Lanes lines while driving is very important for the human drivers when driving cars on the road so is the same or may be more important for Autonomous Car Navigation System. This is the basic prerequisite and very important step. To accoumplish this goal for machines(Autonomous Navigation System) we will be using Opencv and Python along with some core concepts of Computer Vision such as colorchange in Image space, color selection for object extraction , edge detection and Hough transform


# *The pipeline to find lane lines are as follows:

1) Loading an image and converting an image to Grayscale to reduce color image
2) Coverting color space of image from RGB to HSV since it is easier to extract colored objects from HSV space
3) Smoothening/Blurring image to remove noise from image
4) Finding edges using Canny Edge Detection technique
5) Image masking and finding Region of interest
6) Identifying Lane lines using Hough transformation
7)Find smooth lanes using Weighted Arithmetic Mean
8)Find the coordinates of the Lanes
9)Draw Lanes
10)Merge results in the original image


[solidwhiteImage]: ./test_images/solidWhiteRight.jpg "Original"



### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

Original Image:
![alt text][solidwhiteImage]

My pipeline consisted of 6 steps. After Loading the image I First converted images to grayscale

grayimage = cv2.cvtColor(image, cv2.colorBGR2GRAY)

cv2.cvtColor() changes the color space of image from BGR<->GRAY , BGR<->HSV etc.

Next step was to convert the original image to HSV(Hue, Saturation, Value) color space since it is easy to work with HSV color space for extracting color objects than in RGB color space. For finding lane lines of White color converting image to grayscale can solve the problem but since in the given problem domain Lanes lines can be of Yellow color also so we need to extract yellow color lane lines also

hsvimg = cv2.Color(image, cv2.colorBRG2HSV)

finding the range of low and high threshold values for the colors we want to extract so in this case it's yellow and white color objects. We will use cv2.inRange()

yellow_lowthreshold = np.array([20,100,100])
yellow_highthreshold = np.array([30,255,255])
white_lowthreshold = np.array([0,0,235])
white_highthreshold = np.array([255,255,255])

Masked Images fow yellow and white color objects

yellow_mask = cv2.inRange(hsv_img, low_yellow, upper_yellow)
white_mask = cv2.inRange(hsv_img, low_white, upper_white)


Third step is to Smoothen image to reduce noise from image using popular GaussianBlur method
blurimage = cv2.blur(image,(5,5),0) here 5 is the value for kernel size. 

Once the image is smoothened the next step is to find the edges and region of interest.
To find the edges we will useCanny Edge Detection algorithm 

cv2.Canny(blurimage, low_threshold, high_threshold)

Low and high threshold parameters specify the strength of edges

For finding Region of interest first we will identify the size of the image using:
image.shape (will return row, columns and channels) for colored image
we can specify the area of interest as any geometric shape such as a Triangle or any quadilateral

Now apply Hough Transformating to detect lane lines in Image.

def get_hough_lines(image, rho=2, theta=1, voting=25, min_line_length=20, max_line_gap=1):
    return cv.HoughLinesP(image, rho, theta * np.pi / 180,  # degree to radian
                          voting,  # minimum voting in hough accumulator
                          min_line_length,  # min line length in pixels
                          max_line_gap  # gap b/w lines in pixels
                          )


# **Reflection
### 2. Identify potential shortcomings with your current pipeline

The current version of implementation is very simple and will detect straight lane line 
In case of very light/faded lanes line or scenario where Lane lines are absent, shadow present in lane line this implementation will fail



### 3. Suggest possible improvements to your pipeline
Use camera Caliberation --- cv2.findChessboardCorners() 

