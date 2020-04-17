# Super-SloMo

## Introduction
**NOTE :** This is work in progress. Some people have pointed out that with current pretrained model, frame duplication is occuring. However this is a end-to-end working code and can be used to train/re-train a new model (with hyperparameter tuning) on your end to achieve your final goal. Please have a look at [model.py](https://github.com/rmalav15/Super-SloMo/blob/master/model.py) for comparing the model with paper version. Thanks.

This project is a tensorflow implementation of the impressive work 
"Super SloMo: High Quality Estimation of Multiple Intermediate Frames for Video Interpolation". [[Paper]](https://arxiv.org/abs/1712.00080) [[Project]](https://people.cs.umass.edu/~hzjiang/projects/superslomo/) [[Pretrained Model]](https://drive.google.com/file/d/10NYFgOyg-bp0a27V1wruMPBf3ga766m0/view?usp=sharing)

The network is trained on [DeepVideoDeblurring_Dataset_Original_High_FPS_Videos](http://www.cs.ubc.ca/labs/imager/tr/2017/DeepVideoDeblurring/DeepVideoDeblurring_Dataset_Original_High_FPS_Videos.zip) dataset
(30 videos out of 33 are used for training). 

Please wait for gifs to load. :)

<img src='./videos/bike.gif' width="400"> <img src='./videos/bike_slomo.gif' width="400">


## Dependencies

- Python 3.5 (Code can be easily changed to python2.7 by just modifying prints)
- Tensorflow == 1.12.0 (Should work on lower versions with minor changes)
- Opencv-python (Used for video loading and saving)

The code is tested on :- Ubuntu 14.04 LTS with CPU architecture x86_64 + Nvidia Titan X 1070 + cuda9.0.

## Getting Started

### Training

First download the dataset [adobe240fps](http://www.cs.ubc.ca/labs/imager/tr/2017/DeepVideoDeblurring/DeepVideoDeblurring_Dataset_Original_High_FPS_Videos.zip) 
and extract it to appropriate folder. The code is not data format dependent, 
thus can be used with any custom data (Just need to set the width and height flag in train.sh). 

To run training, edit and run train.sh:
```bash
#!/usr/bin/env bash
CUDA_VISIBLE_DEVICES=0 python main.py \
    --output_dir /mnt/069A453E9A452B8D/Ram/slomo_data/experiment_lrelu/ \
    --summary_dir /mnt/069A453E9A452B8D/Ram/slomo_data/experiment_lrelu/log/ \
    --mode train \
    --batch_size 4 \
    --vgg_ckpt /mnt/069A453E9A452B8D/Ram/KAIST/SRGAN_data/vgg_19.ckpt\
    --perceptual_mode VGG54 \
    --learning_rate 0.0001 \
    --decay_step 100000 \
    --decay_rate 0.1 \
    --stair True \
    --beta 0.9 \
    --max_iter 200000 \
    --train_data_count 116241
```

For all available options, check main.py.

If run for first time, it will first convert videos to tfrecord in training format. Please note that for later times, while  it will not extract tfrecords again but will require "train_data_count" to be set to calculate epoch no, step no, etc.   
So note the dataset sample count, which will be printed while training for first time. (TODO: Automate this)

To observe the training losses, learning rate, graph and predicted images, use tensorboard:
```bash
tensorboard --logdir [summary_dir]
```

### Inference

You can download the pre-trained model trained on adobe240fps dataset [here](https://drive.google.com/file/d/10NYFgOyg-bp0a27V1wruMPBf3ga766m0/view?usp=sharing).

To run inference, edit and run slomo_video.sh:
```bash
#!/usr/bin/env bash
CUDA_VISIBLE_DEVICES=0 python slomo_video.py \
    --input_video_path videos/bike.mp4 \
    --output_video_path videos/bike_slomo.mp4 \
    --slomo_rate 12 \
    --checkpoint /mnt/069A453E9A452B8D/Ram/slomo_data/experiment_lrelu/model-200000
```

For all available options, check slomo_video.py.
