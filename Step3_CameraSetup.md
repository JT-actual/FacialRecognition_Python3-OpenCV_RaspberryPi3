# Step 3: Setting up the camera
In this step, we will cover installing, setting up, and testing our RPI3 camera. 


### Setup Camera

I will assume you have the camera physically attached to your RPI3 and not touch on that. Be mindful that the RPI3 B+ has two ports- be sure to use the one marked "Camera," and not "Display."

Kicking this off, we need to ensure we have the camera enabled on our RPI. 

To do this, we will type ```sudo raspi-config``` into the terminal. Scroll down to "Interfacing Options," click Enter. Then down to Camera, click Enter. Choose "Yes" to enable camera module. Reboot by typing ```sudo shutdown now -r```

Next, let's test to ensure our camera is functioning properly. Point that camera at your pretty face and run this:
```raspistill -o heytherehotstuff.jpg```

If this is funtioning properly, we should see our jpg file inside the default "pi" folder. Verify it took, and let's move on!

Next, we need to install PiCamera.

```
$ source ~/.profile
$ workon cv
$ pip install "picamera[array]"
```

FYI, the reason we add in the optional "[array]" submodule is so we can interface with the NumPy arrays our images will be stored in later!


### Single Image Capture with Python + OpenCV

Now we will begin to bring this whole crazy thing together. Open up a new file and name it "test_image.py"

```
# import packages
from picamera.array import PiRGBArray
from picamera import PiCamera
import time
import cv2
 
# initialize camera and make reference to the raw video capture
camera = PiCamera()
rawCapture = PiRGBArray(camera)
 
# allow the camera sensor to warmup
time.sleep(0.1)
 
# take an image from the camera
camera.capture(rawCapture, format="bgr")
image = rawCapture.array
 
# display the image on screen and wait for a keypress to exit
cv2.imshow("Image", image)
cv2.waitKey(0)
```

To test, run ```python test_image.py``` in Terminal


### Single Image Capture with Python + OpenCV

Next, we will do similar to above, but in video form! Open up a new file and name it "test_video.py"

```
# import packages
from picamera.array import PiRGBArray
from picamera import PiCamera
import time
import cv2
 
# initialize camera and make reference to the raw video capture
camera = PiCamera()
camera.resolution = (640, 480)
camera.framerate = 32
rawCapture = PiRGBArray(camera, size=(640, 480))
 
# allow the camera sensor to warmup
time.sleep(0.1)
 
# capture frames from the camera
for frame in camera.capture_continuous(rawCapture, format="bgr", use_video_port=True):
	image = frame.array
 
	# diplay the frame
	cv2.imshow("Frame", image)
	key = cv2.waitKey(1) & 0xFF
 
	# clear the stream for the next frame
	rawCapture.truncate(0)
 
	# if "q" is pressed, break loop
	if key == ord("q"):
		break
```

To test, run ```python test_video.py``` in Terminal


### Next Steps

Still here? Next we will build out face detection and tracking in Step 4. See you there!
