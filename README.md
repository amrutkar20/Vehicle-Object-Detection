# YOLOv4 Large Model - Vehicle Object Detection

![page64](https://github.com/amrutkar20/Road_Traffic_Object_Detection/assets/104386663/50430acc-fb80-46d2-b618-cd5989d5adc0)

## Medium blog
[Navigating-the-Roads-A-Deep-Dive-into-Vehicle-Object-Detection](https://medium.com/@prathameshamrutkar3/navigating-the-roads-a-deep-dive-into-vehicle-object-detection-c3c1ac284cc5)

## Darknet Command
[Darknet Command](https://medium.com/@prathameshamrutkar3/training-a-yolo-object-detector-with-darknet-a-comprehensive-guide-c23636a2e288)

## Github 
[Vehicle_Object_Detection](https://github.com/amrutkar20/Vehicle-Object-Detection.git)

## Kaggle Dataset

[Road Traffic Object Detection Dataset](https://www.kaggle.com/datasets/pamrutkar20/road-traffic-object-detection-dataset)

## Overview
This guide provides instructions on how to train the YOLOv4_large model for detecting Vehicle objects. Follow these steps to train the model with your own dataset.


## Prerequisites
- Download the YOLOv4_large pre-trained weights file: [yolov4.conv.137](insert_google_drive_link)
- Install Darknet: [Darknet GitHub](https://github.com/AlexeyAB/darknet)

## Labeling Images
1. Use [Yolo_mark](https://github.com/AlexeyAB/Yolo_mark) for marking bounded boxes on images and generating annotation files.
2. Create a `.txt` file for each image with object information in the format: `<object-class> <x_center> <y_center> <width> <height>`.


## Training Configuration
1. Copy `yolov4.cfg` to `yolo-obj.cfg`.
2. Modify `yolo-obj.cfg`:
   - change line batch to batch=64
   - change line subdivisions to subdivisions=16
   - change line max_batches to (classes*2000, but not less than number of training images and not less than 6000), f.e. max_batches=6000 if you train for 3 classes
   - change line steps to 80% and 90% of max_batches, f.e. steps=4800,5400
   - set network size width=416 height=416 or any value multiple of 32.
   - Change `classes=80` in three [yolo]-layers to your number of objects.
   - change [filters=255] to filters=(classes + 5)x3 in the 3 [convolutional] before each [yolo] layer, keep in mind that it only has to be the last [convolutional] before each of the [yolo] layers.
     

## Data Setup
1. Create `obj.names` with object names, each on a new line.
2. Create `obj.data` with the following content:
          classes = <Number_Of_Object>
          train = data/train.txt
          valid = data/test.txt
		  names = data/obj.names
		  backup = backup/

3. Put image files (.jpg) in `build/darknet/x64/data/obj/`.
4. Create `train.txt` with filenames of images, each on a new line.

## Download Pre-trained Weights
- Download pre-trained weights for the convolutional layers and put to the directory build\darknet\x64

- for yolov4.cfg, yolov4-custom.cfg (162 MB): yolov4.conv.137 (Google drive mirror yolov4.conv.137 )
- for yolov4-tiny.cfg, yolov4-tiny-3l.cfg, yolov4-tiny-custom.cfg (19 MB): yolov4-tiny.conv.29
- for csresnext50-panet-spp.cfg (133 MB): csresnext50-panet-spp.conv.112
- for yolov3.cfg, yolov3-spp.cfg (154 MB): darknet53.conv.74
- for yolov3-tiny-prn.cfg , yolov3-tiny.cfg (6 MB): yolov3-tiny.conv.11
- for enet-coco.cfg (EfficientNetB0-Yolov3) (14 MB): enetb0-coco.conv.132

## Training
- Start training using the command:
darknet.exe detector train data/obj.data yolo-obj.cfg yolov4.conv.137

## Training on Linux
- For Linux use command:
  !./darknet detector train data/obj.data yolo-obj.cfg yolov4.conv.137

## Monitoring Training
- To disable Loss-Window, use: `-dont_show`.
- To see mAP & Loss-chart on a remote server, use:
  -dont_show -mjpeg_port 8090 -map
Open URL `http://ip-address:8090` in Chrome/Firefox browser.

## After Training
- Get the trained weights file `yolo-obj_final.weights` from `build/darknet/x64/backup/`.

## Resume Training
- After stopping at a certain iteration, resume training using:
!./darknet.exe detector train data/obj.data yolo-obj.cfg backup/yolo-obj_2000.weights

## Note
- If nan values appear in the avg (loss) field during training, there may be an issue.
- If an "Out of memory" error occurs, increase `subdivisions=16`, 32, or 64 in the .cfg file.

# Conclusion

In conclusion, this guide provides a comprehensive walkthrough for training the YOLOv4_large model for Vehicle object detection. By following the outlined steps, users can configure the model, label their dataset, and initiate the training process. Key considerations include adjusting configuration files, labeling images using Yolo_mark, and managing data setup.

The guide covers essential commands for starting and monitoring training, as well as considerations for resuming training from a specific iteration. Users are encouraged to be mindful of potential issues such as nan values during training and out-of-memory errors, with corresponding solutions provided.

By leveraging this guide, users can harness the power of YOLOv4_large for their specific traffic object detection needs, enhancing accuracy and efficiency in real-world scenarios.

