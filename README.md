# YOLOv4 Large Model - Road Traffic Object Detection

## Overview
This guide provides instructions on how to train the YOLOv4_large model for detecting road traffic objects. Follow these steps to train the model with your own dataset.

## Prerequisites
- Download the YOLOv4_large pre-trained weights file: [yolov4.conv.137](insert_google_drive_link)
- Install Darknet: [Darknet GitHub](https://github.com/AlexeyAB/darknet)

## Training Configuration
1. Copy `yolov4.cfg` to `yolo-obj.cfg`.
2. Modify `yolo-obj.cfg`:
   - Set `batch=64`.
   - Set `subdivisions=16`.
   - Adjust `max_batches` based on the number of classes and training images.
   - Set network size `width=416` and `height=416` (or multiples of 32).
   - Change `classes=80` in three [yolo]-layers to your number of objects.
   - Update [filters=255] to `filters=(classes + 5)x3` in [convolutional] before each [yolo] layer.

## Gaussian YOLO Configuration
- If using [Gaussian_yolo] layers, update [filters=57] to `filters=(classes + 9)x3` in [convolutional] before each [Gaussian_yolo] layer.

## Labeling Images
1. Use [Yolo_mark](https://github.com/AlexeyAB/Yolo_mark) for marking bounded boxes on images and generating annotation files.
2. Create a `.txt` file for each image with object information in the format: `<object-class> <x_center> <y_center> <width> <height>`.

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
- Download the pre-trained weights for the convolutional layers and place them in `build/darknet/x64`.
- For yolov4.cfg: [yolov4.conv.137](insert_google_drive_link)

## Training
- Start training using the command:
darknet.exe detector train data/obj.data yolo-obj.cfg yolov4.conv.137

## Training on Linux
- For Linux use command:
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

