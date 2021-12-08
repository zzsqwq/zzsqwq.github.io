---
title: "如何使用CenterNet做3D目标检测测试"
categories: [ "杂学","Ubuntu" ]
tags: [ "CenterNet","神经网络" ]
draft: false
slug: "164"
date: "2021-01-27 11:50:00"
---

## CenterNet—Objects as Points介绍

​	[CenterNet](https://github.com/xingyizhou/CenterNet)是一个anchor-free的目标检测网络，与YOLOv3相比，精度有所提升，此外他不仅能够用于2D目标检测，也能够用于人体姿态识别，3D目标检测等···

### 安装CenterNet

​	其实安装[CenterNet](https://github.com/xingyizhou/CenterNet)的过程就是一个配置环境的问题，直接跟着官方给出的这里[Install.md](https://github.com/xingyizhou/CenterNet/blob/master/readme/INSTALL.md)配置一下即可，十分推荐使用Conda来管理环境，这里给出我的环境给大家参考一下：

> Ubuntu = 18.04 LTS
>
> pytorch = 1.2.0
>
> python = 3.6.12
>
> torchvision = 0.4.0
>
> cuda = 10.2

​	需要注意的是：

+ 官方给出的教程里面使用的是 `pytorch 0.4.1`，但是我个人在实测过程中遇到了一些问题，遂安装网上的教程更改为 `pytorch 1.2.0`，并且需要把 `${CenterNet_Root}/src/lib/models/networks/DCNv2` 中的这个[DCNv2](https://github.com/CharlesShang/DCNv2)网络更改为官方的最新版。
+ 这里使用的cuda版本最好和你的显卡匹配，之前因为显卡驱动的一些问题导致重装了电脑，根据我们学长学姐的建议，最好直接去cuda官网那边去下载deb包直接安装。 
+ 遇到环境配置问题可以先去Google一下，一般作者都在CenterNet's Issues中给出了回复，如果没有，可以发邮件给作者询问，当然也可以发消息/邮箱给我，大家一起探讨一下~

### 运行CenterNet的demo

​	想要运行demo，首先要去 [Model zoo](https://github.com/xingyizhou/CenterNet/blob/master/readme/MODEL_ZOO.md) 中下载一下我们需要使用的model，2D目标检测使用的是 [ctdet_coco_dla_2x.pth](https://drive.google.com/open?id=1pl_-ael8wERdUREEnaIfqOV_VF2bEVRT) ，人体姿态评估使用的是 [multi_pose_dla_3x.pth](https://drive.google.com/open?id=1PO1Ax_GDtjiemEmDVD7oPWwqQkUu28PI) ，下载后统一将他们放在CenterNet根目录中的model文件夹中。

​	然后使用conda切换到CenterNet的环境，在终端中运行：

```shell
python demo.py ctdet --demo ${CenterNet_Root}/images/17790319373_bd19b24cfc_k.jpg --load_model ../models/ctdet_coco_dla_2x.pth 
```

​	这里需要注意的是 `--demo` 后面的 `${CenterNet_Root}/images/17790319373_bd19b24cfc_k.jpg` ，这里我使用的是官方给出的实例图片，它位于CenterNet根目录的images文件夹中，前面的 `${CenterNet_Root} ` 代表的是 CenterNet根目录，好比我的就位于 `/home/zs/CenterNet` 。

​	如果不出意外的话效果应该如下图所示：

![2D目标检测效果][1]

### 运行CenterNet的3D目标检测

#### 配置数据集和模型

​	我们可以直接参考官方的 `DATA.md` 来配置我们的数据集。 

​	然后到 [Model zoo](https://github.com/xingyizhou/CenterNet/blob/master/readme/MODEL_ZOO.md) 下载3D检测使用的模型 [ddd_3dop.pth](https://drive.google.com/open?id=1znsM6E-aVTkATreDuUVxoU0ajL1az8rz) 。

​	这里说一下遇到的几个坑：

+ 首先是配置数据集的过程中，我们需要配置的目录结构如图所示（官方给出的结构树有点模糊不清的感觉）

  ```
  .
  ├── ImageSets_3dop
  │   ├── test.txt
  │   ├── train.txt
  │   ├── trainval.txt
  │   └── val.txt
  ├── ImageSets_subcnn
  │   ├── test.txt
  │   ├── train.txt
  │   ├── trainval.txt
  │   └── val.txt
  └── training
         ├── calib
         ├── image_2
         └── label_2
  ```

+ 然后去到 `${CenterNet_ROOT}/src/tools`目录下，运行 `python convert_kitti_to_coco.py ` 将 **kitti** 数据集转换为 **coco** 数据集的格式，不出意外应该会报错如下：

  ![转换时报错][2]

  这里的解决方案参考CenterNet中的一个Issue , [How to generate the image dir in kitti?](https://github.com/xingyizhou/CenterNet/issues/54) ，我们需要回到 `data/kitti` 目录下手动创建一个 `annotations` 文件夹，然后再回去运行转换程序。转换后目录结构如下：

  ```
  .
  ├── annotations
  │   ├── kitti_3dop_train.json
  │   ├── kitti_3dop_val.json
  │   ├── kitti_subcnn_train.json
  │   └── kitti_subcnn_val.json
  ├── ImageSets_3dop
  │   ├── test.txt
  │   ├── train.txt
  │   ├── trainval.txt
  │   └── val.txt
  ├── ImageSets_subcnn
  │   ├── test.txt
  │   ├── train.txt
  │   ├── trainval.txt
  │   └── val.txt
  └── training
         ├── calib
         ├── image_2
         └── label_2
  ```

+ 然后根据官方的教程，我们需要创建一个images文件夹，然后将其 `training/image_2` 链接到 `images/trainval`，我在实际的测试中，发现此方法并不可行。参考CenterNet中的一个Issue: [Evaluate kitti--AttributeError: 'NoneType' object has no attribute 'shape'](https://github.com/xingyizhou/CenterNet/issues/575#) ，其中 juanmed给出了解决方案：

  > I had the same problem. For some reason the simlinks that are created during the data preparation process described in DATA.md are not working. So instead of creating simlinks I simply copied the actual data into the directories indicated in DATA.md. In other words the folders `data/kitti/images/test` and `data/kitti/images/trainval` do contain the actual images.

  意思就是说，我们在 **images** 中的图片必须都是真实的照片，而不能只是软链接过去。

  解决方案很显然，只需要在 **images** 文件夹中建立一个 **trainval** 文件夹，将 `training/image_2` 中的所有图像都移入其中即可。如果有test的照片，那么也照规在 **images** 新建一个 **test** 文件夹，把测试的照片移入其中即可。

  

#### 运行测试程序

​	接下来我们就可以根据官方给出的 [GETTING_STARTED.md](https://github.com/xingyizhou/CenterNet/blob/master/readme/GETTING_STARTED.md) 来进行我们的检测了。

​	即先编译一下评估工具，然后运行测试程序，但其实还是有一点点小问题。

​	具体问题可以参考 Issus: [kitti test: Couldn't read: 006042.txt of ground truth.](https://github.com/xingyizhou/CenterNet/issues/55)

​	Issue下 **lhyfst** 已经给出了解决方案 ：

> The solution is quite simple.
> `cd data/kitti`
> `mv label_2 label_val`

​    更改后，运行成功~

​	我们应该可以在 `${CenterNet_ROOT}/exp/ddd/3dop/results` 看到我们得到的结果，只不过运行得到的是点的坐标，而不是图像，如果需要图像的话可能还需要自己绘制一下。


  [1]: https://www.zzsqwq.cn/usr/uploads/2021/01/2469782097.jpg
  [2]: https://www.zzsqwq.cn/usr/uploads/2021/01/3420056939.png