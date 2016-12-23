# kp_matching_caffe

Repository to train a convolutional neural network to be able to predict 
if two patches given correspond to the same keypoint (under different changes) or not.  

How to use
----------

First of all to generate the dataset for training we use images from MIRFLICKR-25000. Then we extract several
keypoints and crop them. Next we generate some random transformations for each patch and label them with the same class
using [create_image_dataset](https://github.com/mondejar/create_image_dataset)

A great way to work with Caffe is using LMDB files. To convert out previous dataset to LMDB files we adapt the file 
*caffe/tools/convert_imageset.cpp* to store two images and the label 1 (if both images are extracted from the same keypoint) or 0 (if not).

Convolutional neural network structure
--------------------------------------
In [matchCNN.prototxt](https://github.com/mondejar/kp_matching_caffe/blob/master/models/16/matchCNN.prototxt)
we define our network structure. We create a siamese CNN which given two images of 16x16 pixels return a prediction [0,1]. The model contains two branches of:

<li> 1 convolutional layer </li>
```
layer {
  name: "conv1_s"
  type: "Convolution"
  bottom: "data_source"
  top: "conv1_s"
  param {
    name: "conv1_w"
    lr_mult: 1
  }
  param {
    name: "conv1_b"
    lr_mult: 2
  }
  convolution_param {
    num_output: 16
    kernel_size: 4
    stride: 1
    weight_filler {
      type: "gaussian"
      std: 0.01
    }
    bias_filler {
      type: "constant"
    }
  }
}
```
<li>followed by 1 pooling layer </li>
```
layer {
  name: "pool1_s"
  type: "Pooling"
  bottom: "conv1_s"
  top: "pool1_s"
  pooling_param {
    pool: MAX
    kernel_size: 2
    stride: 1
  }
}
```
<li>and 1 full connected</li>
```
layer {
  name: "ip1_s"
  type: "InnerProduct"
  bottom: "pool1_s"
  top: "ip1_s"
  param {
    name: "ip1_w"
    lr_mult: 1
  }
  param {
    name: "ip1_b"
    lr_mult: 2
  }
  inner_product_param {
    num_output: 256
    weight_filler {
      type: "gaussian"
      std: 0.01
    }
    bias_filler {
      type: "constant"
    }
  }
}
```
<li>Finally the loss is computed with a ContrastiveLoss layer that give the final prediction.</li>
```
layer {
    name: "loss"
    type: "ContrastiveLoss"
    contrastive_loss_param {
        margin: 1.0
    }
    bottom: "ip1_s"
    bottom: "ip1_t"
    bottom: "sim"
    top: "loss"
}
```

In [solver.prototxt](https://github.com/mondejar/kp_matching_caffe/blob/master/models/16/solver.prototxt)
we define the learning params of the network. 


Evaluating the net
------------------
To train our model we just run:
```
./models/16/train_matchCNN.sh 
```

Once the training has completed. We evaluate the results of our trained CNN model against the well-know descriptor SIFT. 
For that purpose we use the  [oxford dataset](http://www.robots.ox.ac.uk/~vgg/research/affine/).


Results
-------



Requirements
------------

[CUDA](http://www.nvidia.es/object/cuda-parallel-computing-es.html) (optional) 

[Caffe](https://github.com/BVLC/caffe)

[OpenCV](http://opencv.org/)

