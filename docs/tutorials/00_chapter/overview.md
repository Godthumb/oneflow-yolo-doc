# 0x0 动机

为了说明使用 OneFlow 训练目标检测模型的可行性以及性能的优越性，最近我们将 ultralytics 版 YOLOv5（https://github.com/ultralytics/yolov5）通过import oneflow as torch的方式迁移为了OneFlow后端（对应YOLOv5的commit号为：`48a85314bc80d8023c99bfb114cea98d71dd0591`）。并对 YOLOv5 中相关的教程进行了汉化，添加了一系列详细的代码解读，原理讲解以及部署教程，希望使得 YOLOv5 项目对用户更加透明化。另外我们也将在性能这个角度进行深入探索，本次我们发布的OneFlow后端的YOLOv5只是一个基础版本，没有用上任何的优化技巧。目前我们在小 Batch 进行训练时相比于 PyTorch 有5%-10%左右的性能优势，而对于大 Batch 则性能和 PyTorch 持平。相信在后续的一些定制化的性能优化技巧下（比如nn.Graph加持，算子的优化），我们可以继续提升YOLOv5在COCO等数据集的训练速度，更有效缩短目标检测模型的训练时间。

喜欢的话请多多为我投票吧。


<img width="256" alt="图片" src="https://user-images.githubusercontent.com/35585791/197813929-79e0ca2b-dcb1-4a5e-91e3-d244e03af0fd.png">


- 🎉代码仓库地址：https://github.com/Oneflow-Inc/one-yolov5
- 🎉文档网站地址：https://start.oneflow.org/oneflow-yolo-doc/index.html

不过即使你对 OneFlow 带来的性能提升不太感兴趣，我们相信[文档网站](https://start.oneflow.org/oneflow-yolo-doc/index.html)中对 YOLOv5 教程的汉化以及源码剖析也会是从零开始深入学习 YOLOv5 一份不错的资料。欢迎在仓库给我们提出宝贵的意见。🌟🌟🌟

欢迎star [one-yolov5项目](https://github.com/Oneflow-Inc/one-yolov5) 获取最新的动态。


# 0x1. 差异

我们将 YOLOv5 的后端从 PyTorch 换成 OneFlow 之后除了性能优势外还做了一些差异化的内容，其中一些内容已经完成，还有一些正在进行中，下面简单展示一下：

![](https://user-images.githubusercontent.com/35585791/196579121-76c6246e-5793-491e-bf96-86dd5ce06290.png)


- [1. YOLOv5 网络结构解析](https://start.oneflow.org/oneflow-yolo-doc/tutorials/01_chapter/yolov5_network_structure_analysis.html)
- [2. 如何准备yolov5模型训练数据](https://start.oneflow.org/oneflow-yolo-doc/tutorials/02_chapter/how_to_prepare_yolov5_training_data.html)
- [3. 快速开始](https://start.oneflow.org/oneflow-yolo-doc/tutorials/03_chapter/quick_start.html)
- [4. 模型训练](https://start.oneflow.org/oneflow-yolo-doc/tutorials/03_chapter/model_train.html)
- [5. 测试时增强 (TTA)](https://start.oneflow.org/oneflow-yolo-doc/tutorials/03_chapter/TTA.html)
- [6. 模型融合 (Model Ensembling)](https://start.oneflow.org/oneflow-yolo-doc/tutorials/03_chapter/model_ensembling.html)
- [7. 从 OneFlow Hub 加载 YOLOv5](https://start.oneflow.org/oneflow-yolo-doc/tutorials/03_chapter/loading_model_from_oneflowhub.html)
- [8. 数据增强](https://start.oneflow.org/oneflow-yolo-doc/tutorials/04_chapter/mosaic.html)
- [9. 矩形推理](https://start.oneflow.org/oneflow-yolo-doc/tutorials/05_chapter/rectangular_reasoning.html)
- [10. IOU深入解析](https://start.oneflow.org/oneflow-yolo-doc/tutorials/05_chapter/iou_in-depth_analysis.html)
- [11. 模型精确度评估](https://start.oneflow.org/oneflow-yolo-doc/tutorials/05_chapter/map_analysis.html)
- [12. ONNX模型导出](https://start.oneflow.org/oneflow-yolo-doc/tutorials/06_chapter/export_onnx_tflite_tensorrt.html)

这一系列的文章我们将逐步开发，Review 以及发布并且会有相应的视频讲解，我们将这个系列的文章叫作：**《YOLOv5全面解析教程》** 🎉🎉🎉

# 0x2. 在COCO上的精度表现

[result.csv](https://oneflow-static.oss-cn-beijing.aliyuncs.com/one-yolo/YOLOv5n_results.csv) 这个日志展示了我们基于 one-yolov5 在 COCO 上从零开始训练 YOLOv5n 网络的日志。

需要扩展...

# 0x3. 在COCO上的性能表现

以下的性能结果都是直接将 PyTorch 切换为 OneFlow 之后测试的，**并没有做针对性优化**，后续会在此基础上继续提升 OneFlow 后端 YOLOv5 的训练速度。

## 在 3080Ti 的性能测试结果

### 单卡测试结果

- 以下为GTX 3080ti(12GB) 的yolov5测试结果（oneflow后端 vs PyTorch后端）
- 以下测试结果的数据配置均为coco.yaml，模型配置也完全一样，并记录训练完coco数据集1个epoch需要的时间
- 由于oneflow eager目前amp的支持还不完善，所以我们提供的结果均为fp32模式下进行训练的性能结果
- PyTorch版本 yolov5 code base链接：https://github.com/ultralytics/yolov5
- OneFlow版本 yolov5 code base链接：https://github.com/Oneflow-Inc/one-yolov5
- cuda 版本 11.7, cudnn 版本为 8.5.0
- 测试的命令为：`python train.py --batch 16 --cfg models/yolov5n.yaml --weights '' --data coco.yaml --img 640 --device 0` ，其中 batch 参数是动态变化的

![图片](https://user-images.githubusercontent.com/35585791/196843664-ceaabc3c-aae9-40dc-9972-60254f8b2549.png)

可以看到，在 batch 比较小的时候 OneFlow 后端的 YOLOv5 相比于 PyTorch 有 5%-10% 左右的性能优势，这可能得益于 OneFlow 的 Eager 运行时系统可以更快的做 Kernel Launch。而 batch 比较大的时候 OneFlow 后端的 YOLOv5 相比于 PyTorch 的性能差不多是持平，这可能是因为当 Batch 比较大的时候 Kernel Launch 的开销相比于计算的开销会比较小。

### 两卡DDP测试结果

- 配置和单卡均一致
- 测试的命令为：`python -m oneflow.distributed.launch --nproc_per_node 2 train.py --batch 16 --data coco.yaml --weights '' --device 0,1` ，其中 batch 参数是动态变化的

![图片](https://user-images.githubusercontent.com/35585791/196844299-3f6c169d-4606-4e94-9edb-95c1c8935234.png)

得益于单卡的性能优势，在 2 卡DDP模式下，OneFlow 后端的 YOLOv5 在小 batch 的训练时间也是稍微领先 PyTorch 后端的 YoloV5 ，而对于大 Batch 来说性能和 PyTorch 相比也是差不多持平。

# 0x4. 总结

我们基于 OneFlow 移植了 ultralytics 版的 YOLOv5 ，在精度训练达标的情况下还可以在 Batch 比较小时取得一些性能优势。此外，我们对 YOLOv5 中相关的教程进行了汉化，添加了一系列详细的代码解读，原理讲解以及部署教程，希望使得 YOLOv5 项目对用户更加透明化。相信对想深入了解 YOLOv5 的读者我们的 **《YOLOv5全面解析教程》** 也是一份不错的学习资料。

