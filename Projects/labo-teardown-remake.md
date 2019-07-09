
![Imgur](https://i.imgur.com/f5RBArp.png)

The IR camera. Since we use a camera we must use some amount of computer vision technology. In this article I will assume you have installed Python, OpenCV and Numpy. In this project decided to use a standard webcam and and a few stickers. Because, most people don't have access to an IR camera. 

The Difference between an IR camera and a webcam is focus. But, not in terms of zoom but in terms of filtering. IR cameras see Infrared Radiation and as mentioned in my last Labo Teardown Article The Nintendo Switch Joy-cons include IR LEDs and Ir cameras they then use IR reflective strips to create a track-able blob. The advantage of an IR camera is the ability to only track IR radiation.

**Note:** We will Note be using an IR camera because I'd like to keep this accessible as possible but if you have a Microsoft Kinect you ca use it as an IR camera and achieve a better result.

![Imgur](https://i.imgur.com/7s415Mf.jpg)

<iframe width="560" height="315" src="https://www.youtube.com/embed/id6eKOffdT4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

##### SOFTWARE REQUIREMENTS
 - OpenCV ^3.0
 - Python
 - Python autogui library
 - Python imutils library
 

##### HARDWARE REQUIREMENTS
 - Lasercutter
 - RGB LED
 - 3v Button Cell Battery
 - Wire
 - Battery holder with toggle switch
 - Webcam
 
So, started by thinking up a test case, I came up with PONG "Why not use a ping pong paddle as an input for PONG". I decided to laser cut my own Ping Pong Paddles and place a vinyl sticker on the top as trackers. 

**Problem:** I tried using a vinyl sticker but it wasn't bright enough and hard to track on my very low quality webcam. So i used an RGB LED and a button cell battery as the tracker simular to LED Throwies. 

![Imgur](https://i.imgur.com/M0CeIAd.png)

Now, that we have the circuit I used super glue and double sided tape to adhere it to the laser cut paddle.

![Imgur](https://i.imgur.com/YTIXjJC.jpg)

![Imgur](https://i.imgur.com/xUzOOPW.jpg)

![Imgur](https://i.imgur.com/pY1lu9L.jpg)

I have stated that it seems Nintendo has decided to use reflection as there mode for tagging and tracking. They use reflective tape that bounce the IR LED light and because the built the cardboard device they control most of the variables to ensure accurate tracking.

![Imgur](https://i.imgur.com/qUHn1FN.png)

#### THE CODE:
Now that the hardware is done we can move on to the code.

```

import cv2
import numpy as np
import imutils
import pyautogui

```
First We import the dependencies. they are pretty standard we will do in to detail as we continue through the explanation.

```
cap = cv2.VideoCapture(0)
screenWidth, screenHeight = pyautogui.size()
```

Next, We create a video capture object this will enable us to input video frame by frame. A frame is considered to be a grid of pixels with several color dimensions containing values in each pixel. Then, We use the pyautogui module to get the screen size this will be used to map the paddle position to cursor position.

```
while True:
    _, frame = cap.read()
    frame = cv2.flip(imutils.resize(frame, width=600), 1)
    blurred = cv2.GaussianBlur(frame, (11, 11), 0)
    hsv = cv2.cvtColor(blurred, cv2.COLOR_BGR2HSV)

    lower_green = np.array([45,0,200])
    upper_green = np.array([86,255,255])

    green_mask = cv2.inRange(hsv, lower_green, upper_green)

    mask = green_mask

    mask = cv2.erode(mask, None, iterations=3)
    mask = cv2.dilate(mask, None, iterations=2)

    res = cv2.bitwise_and(frame, frame, mask=mask)

    cnts = cv2.findContours(mask.copy(), cv2.RETR_EXTERNAL,
        cv2.CHAIN_APPROX_SIMPLE)[-2]
    center = None
```

I then created an infinite loop this is to process every frame from the video capture object as if it were a photo.

#### LINE - BY - LINE:
**_, frame = cap.read()** - store frame in variable

**frame = cv2.flip(imutils.resize(frame, width=600), 1)** - set frame width to 600px and flip frame horizontaly (in a mirror if i waved right the mirrored image would wave left. if i want to translate left to left and right to right i had to flip the frame)

**blurred = cv2.GaussianBlur(frame, (11, 11), 0)** - Blur frame to filter out noise

**hsv = cv2.cvtColor(blurred, cv2.COLOR_BGR2HSV)** - Convert to HSV color space ( using BGR which is 3 dimensions Blue, Green, Red would be very complicated.)

**lower_green = np.array([45,0,200])** - HSV hue: color; Saturation: Brightness; Value: Color Magnitude; create a min threshold for the green we are trying to track.

**upper_green = np.array([86,255,255])** - Create Upper green threshold

**green_mask = cv2.inRange(hsv, lower_green, upper_green)** - creates a map of all pixels with in the green threshold.

**mask = cv2.erode(mask, None, iterations=3)** - converts grey pixels to white using a kernel.

**mask = cv2.dilate(mask, None, iterations=2)** - converts grey pixels to black using kernel

**res = cv2.bitwise_and(frame, frame, mask=mask)** - creates a black mask on every pixel that our green mask did not select.

**cnts = cv2.findContours(mask.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)[-2] - find curves/ edges in green mask ( atthis point there should be a black screen until a green object is detected. Which has super contrast due to the mask and line 10.
center = None** - declare empty variable for holding the center of a contour.

```
if len(cnts) > 0:
        c = max(cnts, key=cv2.contourArea)
        ((x, y), radius) = cv2.minEnclosingCircle(c)
        M = cv2.moments(c)
        center = (int(M["m10"] / M["m00"]), int(M["m01"] / M["m00"]))
        centerText = (int((M["m10"] / M["m00"])+ radius), int(M["m01"] / M["m00"]))

        if radius > 17:
            currentMouseX, currentMouseY = pyautogui.position()
            pyautogui.moveTo(int(x*2), int(y*2))

            cv2.circle(frame, (int(x), int(y)), int(radius),
                (0, 255, 255), 2)
            cv2.circle(frame, center, 5, (0, 0, 255), -1)
            font                   = cv2.FONT_HERSHEY_SIMPLEX
            bottomLeftCornerOfText = center
            fontScale              = 1
            fontColor              = (255,0,0)
            lineType               = 2

            cv2.putText(frame,'x:{},y:{}'.format(x, y), 
                bottomLeftCornerOfText, 
                font, 
                fontScale,
                fontColor,
                lineType)
```

In the code above we find contours and if they are big (meaning not noise) we take the biggest and calculate the center by drawing a circle around the contour and dividing the top and the bottom and left and right. This gives us an (x,y) coordinate to track. 

After that we get the current position of the cursor and move it using the x and y cooridinate from the contour. We effectively can now track the center of a marker and use it to move the mouse. you may have noticed **"pyautogui.moveTo(int(x*2), int(y*2))"** that I am multiplying the x and y coordinates by 2. its be earlier we set the frame to 600 pixels wide. We did this to increase processing speed because the screen is smaller and less pixels to process, it also gives us a constant for our scaling which is screen width/600. I just found a sweet spot with '2'.

```
cv2.imshow('frame', frame)
    cv2.imshow('mask', mask)
    cv2.imshow('res', res)

    ju = cv2.waitKey(5) & 0xff
    if ju == 27:
        break

cv2.destroyAllWindows()
cap.release()
```

Lastly, we show the result which isn't necessary and provide a way to break the infinite loop which is by pressing 'ESC'.

OK Guys and Gals and Any one in between. This is my first coding explanation article and I'd love feedback if you could manage it. I believe I have knowledge to share and I' love to do it efficiently and bear - ab - ly. :)

###### REFRENCE:

https://pythonprogramming.net/morphological-transformation-python-opencv-tutorial/
