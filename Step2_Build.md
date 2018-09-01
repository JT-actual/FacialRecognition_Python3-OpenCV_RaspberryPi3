# Step 2: The Build
In this step, we will cover Installing & Compiling Python 3, OpenCV, and Dependencies. Let's get started!

### Dependencies
In order for OpenCV to work properly, we need to load in a handful of dependencies ranging from dev tools to image and video libraries. Special thanks to Adrian Rosebrock @ PyImageSearch, as I referenced his content to ensure I structured this part of the tutorial properly.

First up, if you did not update & upgrade all packages in the last step, please do so now. In terminal:
```
$ sudo apt-get update && sudo apt-get upgrade
```
Next, let's bring in CMake dev tools to help with the build. 
```
$ sudo apt-get install build-essential cmake pkg-config
```
We will also need image and video I/O packages.

Images:
```
$ sudo apt-get install libpng-dev libjpeg-dev libtiff5-dev libjasper-dev
```
And Video:
```
$ sudo apt-get install libavcodec-dev libavformat-dev libwscale-dev libv4l-dev
$ sudo apt-get install libxvidcore-dev libx264-dev
```
Now we will need the GTK dev library to be able to compile the highgui module in OpenCV.
```
$ sudo apt-get install libgtk2.0-dev libgtk-3-dev
```
Next, this pull in atlas and gfortran
```
$ sudo apt-get install libatlas-base-dev gfortran
```

Lastly, lets install Python 2.7 and 3 header files to avoid a python.h error when we compile OpenCV with Python bindings later on.
```
$ sudo apt-get install python2.7-dev python3-dev
```

### Installing OpenCV
As of 9/1/2018, the current release of OpenCV is 3.4.3. In this section, we will download OpenCV & its contributions.

First, let's grab the OpenCV source code:
```
$ cd ~
$ wget -O opencv.zip https://github.com/Itseez/opencv/archive/3.4.3.zip
$ unzip opencv.zip
```
And now the contributions repo:
```
$ wget -O opencv_contrib.zip https://github.com/Itseez/opencv_contrib/archive/3.4.3.zip
$ unzip opencv_contrib.zip
```

### Installing virtualenv & virtualenvwrapper
Next, we need to construct our virtual environment to house our build. 

First, let's bring in virtualenv/wrapper and clear out our pip cache:
```
$ sudo pip install virtualenv virtualenvwrapper
$ sudo rm ~/.cache/pip
```
Now lets modify our ~./profile file to reflect our virtualenv.
```
$ sudo nano ~/.profile
```
Add the following to the bottom of the file:
```
# virtualenv and virtualenvwrapper
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
source /usr/local/bin/virtualenvwrapper.sh
```
Now let's reload and ensure our changes took effect:
```
$ source ~/.profile
```

### Setting up our virtual evironment for Python
Next we will set up our virtual environment for Python 3.
```
$ mkvirtualenv cv -p python3
```
Done. That wasnt so hard was it?

One thing to point out: As mentioned by Adrian, once we create our virtual environment, we do not need to keep using mkvirtualenv to revisit our virtual environment. 
To revisit our virtual environment, we must execute the following command:
```
$ source ~/.profile
$ workon cv
```
And that will bring us back in. To check if it worked, your terminal window should look like this:
```
(cv) user@hostname:~ $
```
versus
```
user@hostname:~ $
```

### Numpy
Quick and easy- we need to bring Numpy into our virtual environment. 
```
$ pip install numpy
```

### Compile & Install OpenCV
The time has come. Make sure you are still in the virtual environment and run:
```
$ cd ~/opencv-3.4.3/
$ mkdir build
$ cd build
$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-3.4.3/modules \
    -D BUILD_EXAMPLES=ON ..
```
Check out the output. You want to make sure your output looks something like this:
```
Python 3
Interpereter:             some/path/here/python3 (version x.x.x)  
Libraries:                some/path/here/libpython3.xx.xx (version x.x.x) 
Interpereter:             some/path/here/python3.x/somepath/pointingto/numpy (version x.xx.x) 
```

Another great tip from Adrian's blog- increasing swapfile space for the compiling process to help prevent hanging. It is very important to remember to change this back afterwe have compiled!!! Not doing so could corrupt your SD memory!
To do so:
```
$ sudo nano /etc/dphys-swapfile
```
Then find "CONF-SWAPSIZE" and comment out the current swapsize and add "CONF_SWAPSIZE=1024" below it. It should look like this:
```
# set size to absolute value, leaving empty (default) then uses computed value
#   you most likely don't want this, unless you have an special disk situation
# CONF_SWAPSIZE=100
CONF_SWAPSIZE=1024
```

NOTE: Be sure to comment out the previous swapsize, instead of replacing it entirely!

Restart the swap:
```
$ sudo /etc/init.d/dphys-swapfile stop
$ sudo /etc/init.d/dphys-swapfile start
```

Now, let's compile!
```
$ make -j4
```

Now would be a good time to grab that beer mentioned in Step 1. This can take several hours!

Once your Pi has finished compiling, we we need to install OpenCV.
```
$ sudo make install
$ sudo ldconfig
```

### Binding
First, lets verify our bindings were installed in /usr/local/lib/python3.5/site-packages/ 
We can do so with this command:
```
$ ls -l /usr/local/lib/python3.5/site-packages/
```
Next, we need to rename our cv2.cpython-32m-arm-linux-gnueabihf.so (or a variation of) file to cv2.so for us to be able to sym-link OpenCV to Python 3.
Execute the command below, replacing ```v2.cpython-32m-arm-linux-gnueabihf.so``` with the filename found in ```/usr/local/lib/python3.5/site-packages/```. 
```
$ cd /usr/local/lib/python3.5/site-packages/
$ sudo mv cv2.cpython-32m-arm-linux-gnueabihf.so cv2.so
```
Now, sym-link OpenCV to our Python Virtual Environment
```
$ cd ~/.virtualenvs/cv/lib/python3.5/site-packages/
$ ln -s /usr/local/lib/python3.5/site-packages/cv2.so cv2.so
```

### Testing
Let's make sure all is well.
```
$ source ~/.profile 
$ workon cv
$ python
>>> import cv2
>>> cv2.__version__
```

If all is well, you should see something like this:
```
$ source ~/.profile 
$ workon cv
$ python
Python x.x.x (somestuff)
[GCC x.x.x numbers] on linux
Type "help", blah, blah, blah, for more information.
>>> import cv2
>>> cv2.__version__
'3.4.3'
>>>
```

And finally, revert your swapfile to the previous state so we dont burn up our SD card!
```
$ sudo nano /etc/dphys-swapfile
```
Then find "CONF-SWAPSIZE" and uncomment "CONF_SWAPSIZE=100" and remove "CONF_SWAPSIZE=1024". It should look like this:
```
# set size to absolute value, leaving empty (default) then uses computed value
#   you most likely don't want this, unless you have an special disk situation
CONF_SWAPSIZE=100
```

Restart the swap:
```
$ sudo /etc/init.d/dphys-swapfile stop
$ sudo /etc/init.d/dphys-swapfile start
```


### Great work!
This part has been rough, but you made it through. Join me now on Step 3. The fun is about to start!

