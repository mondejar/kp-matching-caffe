# kp_matching_caffe

Repository to train a convolutional neural network to be able to predict 

if two patches given correspond to the same keypoint (under different changes) or not.  

​
(imagen descriptiva)

​

How to use

----------

First of all to generate the dataset for training we use images from MIRFLICKR-25000. Then we extract several

keypoints and crop them. Next we generate some random transformations for each patch and label them with the same class
using (https://github.com/mondejar/create_image_dataset/)

​

A great way to work with Caffe is using LMDB files. To convert out previous dataset to LMDB files we adapt the file 
*caffe/tools/convert_imageset.cpp* to store two images and the label 1 (if both images are extracted from the same keypoint) or 0 (if not ).

​​
Convolutional neural network structure
--------------------------------------
Next the CNN model presents the next shape

prototxt



Evaluating the net
------------------
We evaluate the results of our trained CNN model against the well-know descriptor SIFT. 
For that we use the oxford dataset 


Results
-------

(grafica comparando con descriptores css? para pasar el raton y que se vea chulo...)


(imagen)



Requirements
------------
<ul>
<li> CUDA (optional) </li>
<li> Caffe </li>
<li> OpenCV </li>
</ul>

​