# Invisible Cloak 隱形斗篷
<img src="https://user-images.githubusercontent.com/61585411/167294599-a43f7385-0380-4fff-bf59-3d6542c7e847.jpg" width=800>

### 電影特效常用綠幕來「刪除背景」，而這裡是用紅色布來「刪除前景」！我們基於Python和OpenCV創建了『隱形斗篷』。 
### The algorithm is very similar in principle to green screening. But unlike green screening where we remove the background, in this application, we remove the foreground! In this project, we are using Python and OpenCV to make invisibilty cloak.

## Prerequisites for the cloth:
- Choose a cloth of one color only and suppose the color of the cloth is red then ensure that your background does not contain any red color. Because if the background contains that color then it will cause problems.
- In this project, we are using red color cloth but you can make any color we just have to change the values for the visibilities of the color and it can be changed easily.

## Procedures
1. Import the libraries that we are going to use in this project.
2. Setting up a webcam.
3. Capturing the background.
4. Creating one mask with lower range for red color detection and another mask with higher range for red color detection.
5. Addition of the two masks to generate the final mask.
6. Replacing pixels corresponding to cloak with the background pixels.
7. Displaying the final output

## Step 1: Import the libraries
```
import cv2
import numpy as np
import time
```

## Step 2: Setting up a webcam (Windows)
```
cap = cv2.VideoCapture()
cap.open(0, cv2.CAP_DSHOW)
```
It is quicker to get web cam live in Windows environment by adding cv2.CAP_DSHOW attribute.

## Step 2: Setting up a webcam (Windows/Linux/Mac)
```
cap = cv2.VideoCapture(0)
```

## Step 3: Capturing the Background
Capturing the Image of the background in the first 3 seconds.
```
time.sleep(3)
background=0

for i in range(30):
    ret,background = cap.read()

background = np.flip(background,axis=1)
```
It is optional to flip the background. If you choose not to flip the background, you should not flip the captured flame in the below while loop.

## Step 4: Capturing the video feed using Webcam and create masks for different ranges of red colors
```
while(cap.isOpened()):
    ret, img = cap.read()

    # Flip the image
    img = np.flip(img, axis = 1)

    # Convert the image to HSV color space.
    hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
    blurred = cv2.GaussianBlur(hsv, (35, 35), 0)

    # Defining lower range for red color detection.
    lower = np.array([0,120,70])
    upper = np.array([10,255,255])
    mask1 = cv2.inRange(hsv, lower, upper)

    # Defining upper range for red color detection
    lower_red = np.array([170,120,70])
    upper_red = np.array([180,255,255])
    mask2 = cv2.inRange(hsv, lower_red, upper_red)

```

## Step 5: Addition of the two masks to generate the final mask
```
    # Addition of the two masks to generate the final mask.
    mask = mask1 + mask2
    mask = cv2.morphologyEx(mask, cv2.MORPH_OPEN, np.ones((5,5), np.uint8))
```
## Step 6: Replacing pixels corresponding to cloak with the background pixels
```
    # Replacing pixels corresponding to cloak with the background pixels.
    img[np.where(mask == 255)] = background[np.where(mask == 255)]
```
## Step 7: Displaying the final output
```
    cv2.imshow('Display',img)
    k = cv2.waitKey(10)
    if k == 27 or k == ord('q'):
        break
cap.release()
cv2.destroyAllWindows()
```
