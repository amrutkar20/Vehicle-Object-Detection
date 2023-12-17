# Road_Traffic_Object_Detection (YOLOv4_large model)

## How to train (to detect your Traffic objects)

Training Yolo v4:

For training cfg/yolov4.cfg download the pre-trained weights-file (162 MB): yolov4.conv.137 (Google drive mirror yolov4.conv.137 )
Create file yolo-obj.cfg with the same content as in yolov4.cfg (or copy yolov4.cfg to yolo-obj.cfg) and:
change line batch to batch=64
change line subdivisions to subdivisions=16
change line max_batches to (classes*2000, but not less than number of training images and not less than 6000), f.e. max_batches=6000 if you train for 3 classes
change line steps to 80% and 90% of max_batches, f.e. steps=4800,5400
set network size width=416 height=416 or any value multiple of 32:
darknet/cfg/yolov3.cfg

Lines 8 to 9 in 0039fd2

 width=416 
 height=416 
change line classes=80 to your number of objects in each of 3 [yolo]-layers:
darknet/cfg/yolov3.cfg

Line 610 in 0039fd2

 classes=80 
darknet/cfg/yolov3.cfg

Line 696 in 0039fd2

 classes=80 
darknet/cfg/yolov3.cfg

Line 783 in 0039fd2

 classes=80 
change [filters=255] to filters=(classes + 5)x3 in the 3 [convolutional] before each [yolo] layer, keep in mind that it only has to be the last [convolutional] before each of the [yolo] layers.
darknet/cfg/yolov3.cfg

Line 603 in 0039fd2

 filters=255 
darknet/cfg/yolov3.cfg

Line 689 in 0039fd2

 filters=255 
darknet/cfg/yolov3.cfg

Line 776 in 0039fd2

 filters=255 
when using [Gaussian_yolo] layers, change [filters=57] filters=(classes + 9)x3 in the 3 [convolutional] before each [Gaussian_yolo] layer
darknet/cfg/Gaussian_yolov3_BDD.cfg

Line 604 in 6e5bdf1

 filters=57 
darknet/cfg/Gaussian_yolov3_BDD.cfg

Line 696 in 6e5bdf1

 filters=57 
darknet/cfg/Gaussian_yolov3_BDD.cfg

Line 789 in 6e5bdf1

 filters=57 
So if classes=1 then should be filters=18. If classes=2 then write filters=21. (Do not write in the cfg-file: filters=(classes + 5)x3)

(Generally filters depends on the classes, coords and number of masks, i.e. filters=(classes + coords + 1)*<number of mask>, where mask is indices of anchors. If mask is absence, then filters=(classes + coords + 1)*num)

So for example, for 2 objects, your file yolo-obj.cfg should differ from yolov4-custom.cfg in such lines in each of 3 [yolo]-layers:

[convolutional]
filters=21

[region]
classes=2
Create file obj.names in the directory build\darknet\x64\data\, with objects names - each in new line
Create file obj.data in the directory build\darknet\x64\data\, containing (where classes = number of objects):
classes = 2
train  = data/train.txt
valid  = data/test.txt
names = data/obj.names
backup = backup/
Put image-files (.jpg) of your objects in the directory build\darknet\x64\data\obj\
You should label each object on images from your dataset. Use this visual GUI-software for marking bounded boxes of objects and generating annotation files for Yolo v2 & v3: https://github.com/AlexeyAB/Yolo_mark
It will create .txt-file for each .jpg-image-file - in the same directory and with the same name, but with .txt-extension, and put to file: object number and object coordinates on this image, for each object in new line:

<object-class> <x_center> <y_center> <width> <height>

Where:

<object-class> - integer object number from 0 to (classes-1)

<x_center> <y_center> <width> <height> - float values relative to width and height of image, it can be equal from (0.0 to 1.0]

for example: <x> = <absolute_x> / <image_width> or <height> = <absolute_height> / <image_height>

attention: <x_center> <y_center> - are center of rectangle (are not top-left corner)

For example for img1.jpg you will be created img1.txt containing:

1 0.716797 0.395833 0.216406 0.147222
0 0.687109 0.379167 0.255469 0.158333
1 0.420312 0.395833 0.140625 0.166667
Create file train.txt in directory build\darknet\x64\data\, with filenames of your images, each filename in new line, with path relative to darknet.exe, for example containing:
data/obj/img1.jpg
data/obj/img2.jpg
data/obj/img3.jpg
Download pre-trained weights for the convolutional layers and put to the directory build\darknet\x64

for yolov4.cfg, yolov4-custom.cfg (162 MB): yolov4.conv.137 (Google drive mirror yolov4.conv.137 )
for yolov4-tiny.cfg, yolov4-tiny-3l.cfg, yolov4-tiny-custom.cfg (19 MB): yolov4-tiny.conv.29
for csresnext50-panet-spp.cfg (133 MB): csresnext50-panet-spp.conv.112
for yolov3.cfg, yolov3-spp.cfg (154 MB): darknet53.conv.74
for yolov3-tiny-prn.cfg , yolov3-tiny.cfg (6 MB): yolov3-tiny.conv.11
for enet-coco.cfg (EfficientNetB0-Yolov3) (14 MB): enetb0-coco.conv.132
Start training by using the command line: darknet.exe detector train data/obj.data yolo-obj.cfg yolov4.conv.137

To train on Linux use command: ./darknet detector train data/obj.data yolo-obj.cfg yolov4.conv.137 (just use ./darknet instead of darknet.exe)

(file yolo-obj_last.weights will be saved to the build\darknet\x64\backup\ for each 100 iterations)
(file yolo-obj_xxxx.weights will be saved to the build\darknet\x64\backup\ for each 1000 iterations)
(to disable Loss-Window use darknet.exe detector train data/obj.data yolo-obj.cfg yolov4.conv.137 -dont_show, if you train on computer without monitor like a cloud Amazon EC2)
(to see the mAP & Loss-chart during training on remote server without GUI, use command darknet.exe detector train data/obj.data yolo-obj.cfg yolov4.conv.137 -dont_show -mjpeg_port 8090 -map then open URL http://ip-address:8090 in Chrome/Firefox browser)
8.1. For training with mAP (mean average precisions) calculation for each 4 Epochs (set valid=valid.txt or train.txt in obj.data file) and run: darknet.exe detector train data/obj.data yolo-obj.cfg yolov4.conv.137 -map

8.2. One can also set the -mAP_epochs in the training command if less or more frequent mAP calculation is needed. For example in order to calculate mAP for each 2 Epochs run darknet.exe detector train data/obj.data yolo-obj.cfg yolov4.conv.137 -map -mAP_epochs 2

After training is complete - get result yolo-obj_final.weights from path build\darknet\x64\backup\

After each 100 iterations you can stop and later start training from this point. For example, after 2000 iterations you can stop training, and later just start training using: darknet.exe detector train data/obj.data yolo-obj.cfg backup\yolo-obj_2000.weights
(in the original repository https://github.com/pjreddie/darknet the weights-file is saved only once every 10 000 iterations if(iterations > 1000))

Also you can get result earlier than all 45000 iterations.
Note: If during training you see nan values for avg (loss) field - then training goes wrong, but if nan is in some other lines - then training goes well.

Note: If you changed width= or height= in your cfg-file, then new width and height must be divisible by 32.

Note: After training use such command for detection: darknet.exe detector test data/obj.data yolo-obj.cfg yolo-obj_8000.weights

Note: if error Out of memory occurs then in .cfg-file you should increase subdivisions=16, 32 or 64
