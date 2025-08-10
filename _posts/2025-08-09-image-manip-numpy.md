---
title: Image Manipution with NumPy
date: 2025-08-09
layout: post
image: "/posts/img_squirrel_stack.jpg"
tags: [NumPy, Image Manipulation]
---
# Image Manipulation with NumPy
In this exercice we are going to use NumPy to create this picture collage...

![output image](/img/posts/img_squirrel_stack.jpg)

...from this image:

![input image](/img/posts/img_squirrel.jpg)
## Basic Idea: Images as Numeric Arrays
Each pixel in the image has unique horizontal and vertical coordinates. Besides, each pixel has three color channels (Red, Green, Blue). These characteristics mean that we can treat our image as a numeric array. We will see how NumPy's array processing capabilities help us perform various tasks on our image.
## Step by Step Python Implementation
### Reading the Input Image
We begin by importing the libraries that we are going to need:
```python
import numpy as np
from skimage import io
import matplotlib.pyplot as plt
```
Next, let us read the input. It is a file named `squirrel.jpg` in Python's current working directory. We will keep the data in a variable named `squirrel`.
```python
squirrel = io.imread("squirrel.jpg")
```
Let us check the size of the image:
```python
squirrel.shape
```
```
(3000,3000,3)
```
Our image is a three-dimensional array, with 3000 rows, 3000 columns, and 3 color channels.
Let us see it!
```python
plt.imshow(squirrel)
plt.show()
```
![load image](/img/posts/img_1_load.png)

Ok! Python transposed and flipped our image! That is a good reminder that Python locates pixels by their row and column from the top-left of the image, which is different from our typical `(x,y)` coordinates with the origin at the bottom-left.
### Transposing the Image
To fix the problem, we first transpose the image and store it under the same name.
```python
squirrel = squirrel.transpose((1,0,2))
```
Since our data is three-dimensional, we need to specify which dimensions we wish to transpose. We do that with the input tuple of the `transpose()` method. It tells Python to change the order of our array's three dimenstions from the default `(0,1,2)` to `(1,0,2)`. Note that the third dimension, that is the color channels, remains unchanged.
Let us see the result.
```python
plt.imshow(squirrel)
plt.grid()
plt.show()
```
![transpose](/img/posts/img_2_1_transpose.png)
### Flipping the Image
We still need to flip the image horizontally. That is, we want to rearrange the columns of the image in reverse order. We can do that with NumPy by slicing:
```python
squirrel = squirrel[:,::-1,:]
```
In the above code, we have left the first dimension (rows) unchanged, modified the second dimension (columns), and left the third dimension (colors) unchanged. Note that slicing is specified with `[start:end:step]`. In this case we have not specified start and end, so slicing is applied to all columns, and we have set the step parameter to `-1`, which specifies reverse order.
Let us see how the image looks now.
```python
plt.imshow(squirrel)
plt.grid()
plt.show()
```
I added the command `plt.grid()` to display a grid over the image. This will help us in the next step, when we want to crop the image.
![flip](/img/posts/img_2_2_flip.png)
### Cropping the Image
Finally our image looks all right! Now, we want to crop two sections from it: a `3000x2000` rectangular crop containing mainly the tree, and a `1000x1000` square block containing the squirrel. With the help of the grid lines that we just drew, we can specify our crop lines. Also note that we can crop our image by specifying a selection in our data array.
Let us first make the rectangular crop, save it in a variable named `crop_slim`, and show it.
```python
crop_slim = squirrel[:,500:2500,:]
plt.imshow(crop_slim)
plt.show()
```
![crop slim](/img/posts/img_3_1_cropped_slip.png)

We selected columns 500 to 2500.
Next, we make the square crop, name it `crop_bloc`, and display it.
```python
crop_bloc = squirrel[750:1750,1000:2000,:]
plt.imshow(crop_bloc)
plt.show()
```
![crop bloc](/img/posts/img_3_2_cropped_bloc.png)

We selected rows 750 to 1750 and columns 1000 to 2000. I think it looks right!
### Separating Color Channels
Alright! Now we want to separate the Red, Green, and Blue bands of our little squirrel image (`crop_bloc`). This task is a bit more challenging and at the same time exciting!
Let us first take a closer look at our data, let us see what is stored in one pixel, say, at row 0 and column 0:
```python
squirrel[0,0,:]
```
```
array([146, 198, 123], dtype=uint8)
```
For this single pixel, we have an array with three elements, each corresponding to a color band. We also see the expression `dtype=uint8`. It shows that the type of data in this array is unsigned integer with 8 bits. That means the value of each element can be any whole number between 0 and 255, and that this value will only take 1 byte of storage. We will use this information shortly.
Now, we want to create an image with only the Red band, another image with only the Green band, and a third image with only the Blue band. Here is how we will proceed: We will create three arrays with the same size as our `crop_bloc` array. Initially, our arrays will be filled with 0 as the value for all color bands of all pixels. Then, for each color band, we copy only its corresponding band from `crop_bloc` to the respective new array. For example, for the band, we can simply write:
```python
red = np.zeros(crop_bloc.shape, dtype = "uint8")
red[:,:,0] = crop_bloc[:,:,0]
```
Note that when creating the array with `np.zeros()` we specified that the type of our data is `"uint8"`, which is the type that we observed in our input image. Alright, let us see how this red band image looks!
```python
plt.imshow(red)
plt.show()
```
![red band](/img/posts/img_4_1_red.png)

I think at this moment it is fair to say "Wow!" or express excitement in some other way. 
Let us do the same for green:
```python
green = np.zeros(crop_bloc.shape, dtype = "uint8")
green[:,:,1] = crop_bloc[:,:,1]
plt.imshow(green)
plt.show()
```
![green band](/img/posts/img_4_2_green.png)

And blue:
```python
blue = np.zeros(crop_bloc.shape, dtype = "uint8")
blue[:,:,2] = crop_bloc[:,:,2]
plt.imshow(blue)
plt.show()
```
![blue band](/img/posts/img_4_3_blue.png)

Very well! Now we have three image arrays named `red`, `green`, and `blue`!
### Stacking Images
Next, we are going to create two stacked images. Again, NumPy comes to our help, as our images are indeed arrays. An obvious but critically important point is to make sure that the sizes of the arrays allow them to be stacked together.
We begin with stacking the three arrays `red`, `green`, and `blue` on top of each other, or creating a vertical stack. This requires that all arrays have the same number of columns. In our case all the three images are `1000x1000`, so there is no problem with stacking.
```python
squirrel_rainbow = np.vstack((red,green,blue))
plt.imshow(squirrel_rainbow)
plt.show()
```
![vertical stack](/img/posts/img_5_1_vstack.png)

Very good! Let us just check the size of this new image
```python
squirrel_rainbow.shape
```
```
(3000, 1000, 3)
```
As expected, the stacked image has 3000 rows and 1000 columns.
Next, we want to stack this new image to the left of our `crop_slim`. This is horizontal stacking. Let us just check the size of `crop_slim` to make sure stacking is possible.
```python
crop_slim.shape
```
```
(3000, 2000, 3)
```
The two images have the same number of rows (3000), so we can stack them horizontally.
```python
squirrel_stack = np.hstack((squirrel_rainbow, crop_slim))
plt.imshow(squirrel_stack)
plt.show()
```
![horizontal stack](/img/posts/img_5_2_hstack.png)
### Writing the Output Image
In the final step of our work, we save the stacked image that we just created. I am saving the output in the current working directory, that is why no path is specified.
```python
io.imsave("squirrel_stack.jpg", squirrel_stack)
```
---
And with that, we can conclude our exercise. I enjoyed how we got into details of our images with NumPy's array manipulation capabilities. I hope you found this exercise enjoyable too. Now you can do all of these with an image of your own choice.
Happy learning! 
