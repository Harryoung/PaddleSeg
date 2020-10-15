简体中文 | [English]()

# 快速开始

本教程提供了一个快速入门的使用示例，用于训练一个基于CityScapes数据集的BiSeNetv2模型。为了快速完成整体教程，我们将训练的迭代次数设置为4000（以Tesla V100为例，训练时间大约为0.5小时），在训练结束时，模型远远没有达到收敛的情况。因此，在了解完整体使用流程后，我们建议您参考[模型库](../configs)的训练配置，动手训练一个模型以体验实际训练效果。

## 模型训练

我们可以通过PaddleSeg提供的脚本对模型进行训练，请确保完成了PaddleSeg的安装工作，并且位于PaddleSeg/dygraph目录下，执行以下脚本：

```shell
export CUDA_VISIBLE_DEVICES=0 # 设置1张可用的卡
python train.py \
       --config configs/bisenetv2/bisenetv2_cityscapes_1024x512_160k.yml \
       --do_eval \
       --use_vdl \
       --save_interval_iters 1000 \
       --iters 4000 \
```

### 训练参数解释

|参数名|用途|是否必选项|默认值|
|-|-|-|-|
|iters|训练迭代次数|否|配置文件中指定值|
|batch_size|单卡batch size|否|配置文件中指定值|
|learning_rate|初始学习率|否|配置文件中指定值|
|config|配置文件|是|-|
|save_dir|模型和visualdl日志文件的保存根路径|否|output|
|num_workers|用于异步读取数据的进程数量|否|0|
|use_vdl|是否开启visualdl记录训练数据|否|否|
|save_interval_iters|模型保存的间隔步数|否|1000|
|do_eval|是否在保存模型时启动评估|否|否|
|log_iters打印日志的间隔步数|否|10|

  
**注意**：如果想要使用多卡训练的话，需要将环境变量CUDA_VISIBLE_DEVICES指定为多卡，并使用paddle.distributed.launch启动训练脚本:
```shell
export CUDA_VISIBLE_DEVICES=0,1,2,3 # 设置4张可用的卡
python -m paddle.distributed.launch train.py \
       --config configs/bisenetv2/bisenetv2_cityscapes_1024x512_160k.yml \
       --do_eval \
       --use_vdl \
       --save_interval_iters 1000 \
       --iters 4000 \
```

## 训练可视化

PaddleSeg会将训练过程中的数据写入VisualDL文件，并实时的查看训练过程中的日志，记录的数据包括：
1. loss变化趋势
2. 学习率变化趋势
3. 训练时间
4. 数据读取时间
5. mean IoU变化趋势（当打开了`do_eval`开关后生效）
6. mean pixel Accuracy变化趋势（当打开了`do_eval`开关后生效）

使用如下命令启动VisualDL查看日志
```shell
# 下述命令会在127.0.0.1上启动一个服务，支持通过前端web页面查看，可以通过--host这个参数指定实际ip地址
visualdl --logdir output/
```

## 模型评估

当保存完模型后，我们可以通过PaddleSeg提供的脚本对模型进行评估
```shell
python val.py \
       --config configs/bisenetv2/bisenetv2_cityscapes_1024x512_160k.yml \
       --model_dir output/iter_4000
```