# Detecting solar panels from satellite images 

## Abstract

This study leverages deep learning for segmenting solar panels in satellite imagery to enable tracking of installation process on large solar farms. A custom segmentation ResNet-based model was developed in PyTorch, to achieved 71% Jaccard index (aka IoU) on a validation dataset. ColorJitter was shown to be a good augmentation to combat influence of clouds and shadows in the images. [GitHub repo](https://github.com/nesaboz/solar).

## Data 

Raw images of solar farms were provided using Airbus Defence and Space satelites, followed by manual labeling using [LabelMe](https://github.com/wkentaro/labelme) software.
The large (~1GB) satellite images were split into smaller ones (256x256 pixels size), and total of 449 labels of 3 classes: racks, common panels, and dense panels were obtained.

![label1.png](assets/label1.png)
![label2.png](assets/label2.png)
![label3.png](assets/label3.png)

## Augmentation

It was insightful to plot mean and standard deviation of labeled and unlabeled images to identified range for 
ColorJitter as augmentation:
![normalization.png](assets/normalization.png)

Labeled and unlabeled data have decent overlap, with non-covered datapoints typically being clouds 
(high mean, low stdev) or with partial out-of-bounds, i.e. black, zones (low mean). 
See `main_experimentation.py` for examples. 

## Training

I used `Adam` optimizer and weighed `CrossEntropyLoss`. Segmentation was based on UNet-like architecture 
and [this](https://arxiv.org/abs/1505.04366) paper (see models.py):

![losses.png](assets/losses.png)

Training for 120 epoch took about 6 minutes (RTX5000). Achieved 71% Jaccard index (aka IoU) on validation dataset.

## Inference

Finally, I evaluated and stitched together predicted images back into large satellite images (size ~1Gb, 
showing here only smaller section):

![img.png](assets/img.png)

(Note: some missing patches in the image were used for training).

**Shadows and clouds are probably the biggest obstacle for precise counting**. While augmentations can help to some extent,
ideally multiple images of the same solar farm should be obtained and combined for thorough coverage.

The following are some notable examples.

Handling multiple classes: 

![img_2.png](assets/img_2.png) 
![img_3.png](assets/img_3.png)
![img_6.png](assets/img_6.png)
![img_8.png](assets/img_8.png) 

Handling different backgrounds:

![img_4.png](assets/img_4.png) 
![img_7.png](assets/img_7.png)
![img_17.png](assets/img_17.png)
![img_10.png](assets/img_10.png) 
 

And here are the common failures, mostly shades and clouds:

![img_9.png](assets/img_9.png) 
![img_11.png](assets/img_11.png)
![img_13.png](assets/img_13.png)
![img_15.png](assets/img_15.png) 
![img_16.png](assets/img_16.png)
![img_18.png](assets/img_18.png)
![img_19.png](assets/img_19.png)

## Conclusion

Object segmentation has shown to perform well for solar panel detection. Augmentation using brightness and contrast
improved detection significantly.

Number of solar panels is calculated knowing pixel resolution is 50cm/pixel and panels width of about 100 cm (i.e. 2 pixels).

Small edge improvements can be done by allowing for some overlap when cropping large images and combining masks subsequently.

References:
Boiler-plate code based on a [book]((https://pytorchstepbystep.com/)) "PyTorch: Step by Step " by Daniel Voigt Godoy.