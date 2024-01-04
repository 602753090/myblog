---
title: yolov5训练手部识别
date: 2024-01-04 14:50:26
categories:
- 机器学习
tags:
- yolo
- 代码
- 学习
---

采用yolov5s预训练模型进行训练，训练集为19张电脑前置摄像头采集的720p图片。

## 环境

使用python3.11，pytorch，CUDA11.2，cuDNN8.1.0

通过gpu进行训练，gpu型号：NVIDIA RTX 2080s

### 安装项目依赖

在项目文件夹下执行以下命令安装yolov5及相应依赖

```cmd
git clone https://github.com/ultralytics/yolov5  # clone
cd yolov5
pip install -r requirements.txt  # install
```

## 图像标注

训练集为19张电脑前置摄像头采集的720p图片，使用[makesense](https://www.makesense.ai/)进行图片在线标注，导出对应的jpg和txt文件，txt文件即为yolo格式的标签文件。标签数量为1，标签名为hand。

## 模型训练

修改yaml文件中相关配置

```cmd
# Train/val/test sets as 1) dir: path/to/imgs, 2) file: path/to/imgs.txt, or 3) list: [path/to/imgs1, path/to/imgs2, ..]
path: mydata  # dataset root dir
train: images  # train images (relative to 'path') 128 images
val: images  # val images (relative to 'path') 128 images
test:  # test images (optional)

# Classes
names:
  0: hand
```

修改train.py文件中相关配置，训练200个epoch

```python
def parse_opt(known=False):
    parser = argparse.ArgumentParser()
    parser.add_argument('--weights', type=str, default=ROOT / 'yolov5s.pt', help='initial weights path')
    parser.add_argument('--cfg', type=str, default=ROOT / 'models/yolov5s.yaml', help='model.yaml path')
    parser.add_argument('--data', type=str, default=ROOT / 'data/mydata.yaml', help='dataset.yaml path')
    parser.add_argument('--hyp', type=str, default=ROOT / 'data/hyps/hyp.scratch-low.yaml', help='hyperparameters path')
    parser.add_argument('--epochs', type=int, default=200, help='total training epochs')
    parser.add_argument('--batch-size', type=int, default=-1, help='total batch size for all GPUs, -1 for autobatch')
    parser.add_argument('--imgsz', '--img', '--img-size', type=int, default=640, help='train, val image size (pixels)')
```

训练完成后输出pt模型文件，加载后即可进行检测。
