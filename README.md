# Mask_R-CNN
This is an implementation of Mask R-CNN on Python 3, Keras, and TensorFlow. The model generates bounding boxes and segmentation masks for each instance of an object in the image. It's based on Feature Pyramid Network (FPN) and a ResNet101 backbone.




![image](https://user-images.githubusercontent.com/54206381/161395690-5716f5eb-5152-4a72-97dd-b0b1a1f2ee07.png)




Image Resizing: To support training multiple images per batch we resize all images to the same size. For example, 1024x1024px on MS COCO. We preserve the aspect ratio, so if an image is not square we pad it with zeros. In the paper the resizing is done such that the smallest side is 800px and the largest is trimmed at 1000px.

Bounding Boxes: Some datasets provide bounding boxes and some provide masks only. To support training on multiple datasets we opted to ignore the bounding boxes that come with the dataset and generate them on the fly instead. We pick the smallest box that encapsulates all the pixels of the mask as the bounding box. This simplifies the implementation and also makes it easy to apply image augmentations that would otherwise be harder to apply to bounding boxes, such as image rotation.

To validate this approach, we compared our computed bounding boxes to those provided by the COCO dataset. We found that ~2% of bounding boxes differed by 1px or more, ~0.05% differed by 5px or more, and only 0.01% differed by 10px or more.

Learning Rate: The paper uses a learning rate of 0.02, but we found that to be too high, and often causes the weights to explode, especially when using a small batch size. It might be related to differences between how Caffe and TensorFlow compute gradients (sum vs mean across batches and GPUs). Or, maybe the official model uses gradient clipping to avoid this issue. We do use gradient clipping, but don't set it too aggressively. We found that smaller learning rates converge faster anyway so we go with that.
