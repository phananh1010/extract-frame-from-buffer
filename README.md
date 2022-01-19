# How to extract decoded frame data from playback buffer and convert it into RGB format

It turns out that off-the-self streaming clients do not store decoded video frames in a buffer in RGB format. Popular encoders such as HVEC stored decoded frames in YUV format. I realized this when I was working with a 4K video of the size 1920x3840. When extracting decoded frames from the buffer, the returned result is a single dimensional data array of size 11059200. I noticed a mismatch in size here. There was no way I could resize this array into the shape `(1920, 3840, 3)`, which is the expected size of a `RGB` frame. In fact, `11059200` if 1.5 times greater than `1920 * 3840`.


Then this website and this website saved me. Here is the process to convert decoded frame data into a RGB image.


Assume that the decoded frame data has already been extracted. For demonstration, I stored the decoded frame and named it packet_10. This file contains one single dimensional array of size `11059200`. We want to get an RGB image of size `1920x3840` from this file.
First, read the file using pickle, and convert it to `2880x3840`. Note that `2880 = 1920 * 1.5`.
```
import cv2
import pickle
import numpy as np
p1   = pickle.load(open('./packet_10', "rb"))
src  = p1.reshape(2880, 3840)
```

Then, use cvtColor from cv2 library to convert it to BGR (or RGB)
```
dst = cv2.cvtColor(src, cv2.COLOR_YUV2BGR_YV12)
```
 
After this step, the dst variable will contain the image data in traditional RGB format.
![Framedata](https://github.com/phananh1010/extract-frame-from-buffer/blob/master/img.jpg)
