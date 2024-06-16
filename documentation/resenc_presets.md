# nnU-Net 中的残差编码器预设

使用这些预设时，请引用我们最近关于3D医学图像分割中严格验证需求的论文：

> Isensee, F.<sup>* </sup>, Wald, T.<sup>* </sup>, Ulrich, C.<sup>* </sup>, Baumgartner, M.<sup>* </sup>, Roy, S., Maier-Hein, K.<sup>†</sup>, Jaeger, P.<sup>†</sup> (2024). nnU-Net Revisited: A Call for Rigorous Validation in 3D Medical Image Segmentation. arXiv preprint arXiv:2404.09556.

*: 共同第一作者\
<sup>†</sup>: 共同最后作者

[论文链接](https://arxiv.org/pdf/2404.09556.pdf)

自我们参加 KiTS2019 以来，nnU-Net 就已经支持残差编码器 UNet，但一直没有受到广泛关注。随着我们新的 nnUNetResEncUNet 预设的推出，这一情况将发生改变 :raised_hands:！尤其是在 KiTS2023 和 AMOS2022 等大数据集上，它们提供了改进的分割性能！

|                        | BTCV  | ACDC  | LiTS  | BraTS | KiTS  | AMOS  |  VRAM |  RT | Arch. | nnU |
|------------------------|-------|-------|-------|-------|-------|-------|-------|-----|-------|-----|
|                        | n=30  | n=200 | n=131 | n=1251| n=489 | n=360 |       |     |       |     |
| nnU-Net (org.) [1]     | 83.08 | 91.54 | 80.09 | 91.24 | 86.04 | 88.64 |  7.70 |  9  |  CNN  | Yes |
| nnU-Net ResEnc M       | 83.31 | 91.99 | 80.75 | 91.26 | 86.79 | 88.77 |  9.10 |  12 |  CNN  | Yes |
| nnU-Net ResEnc L       | 83.35 | 91.69 | 81.60 | 91.13 | 88.17 | 89.41 | 22.70 |  35 |  CNN  | Yes |
| nnU-Net ResEnc XL      | 83.28 | 91.48 | 81.19 | 91.18 | 88.67 | 89.68 | 36.60 |  66 |  CNN  | Yes |
| MedNeXt L k3 [2]       | 84.70 | 92.65 | 82.14 | 91.35 | 88.25 | 89.62 | 17.30 |  68 |  CNN  | Yes |
| MedNeXt L k5 [2]       | 85.04 | 92.62 | 82.34 | 91.50 | 87.74 | 89.73 | 18.00 | 233 |  CNN  | Yes |
| STU-Net S [3]          | 82.92 | 91.04 | 78.50 | 90.55 | 84.93 | 88.08 |  5.20 |  10 |  CNN  | Yes |
| STU-Net B [3]          | 83.05 | 91.30 | 79.19 | 90.85 | 86.32 | 88.46 |  8.80 |  15 |  CNN  | Yes |
| STU-Net L [3]          | 83.36 | 91.31 | 80.31 | 91.26 | 85.84 | 89.34 | 26.50 |  51 |  CNN  | Yes |
| SwinUNETR [4]          | 78.89 | 91.29 | 76.50 | 90.68 | 81.27 | 83.81 | 13.10 |  15 |   TF  | Yes |
| SwinUNETRV2 [5]        | 80.85 | 92.01 | 77.85 | 90.74 | 84.14 | 86.24 | 13.40 |  15 |   TF  | Yes |
| nnFormer [6]           | 80.86 | 92.40 | 77.40 | 90.22 | 75.85 | 81.55 |  5.70 |  8  |   TF  | Yes |
| CoTr [7]               | 81.95 | 90.56 | 79.10 | 90.73 | 84.59 | 88.02 |  8.20 |  18 |   TF  | Yes |
| No-Mamba Base          | 83.69 | 91.89 | 80.57 | 91.26 | 85.98 | 89.04 |  12.0 |  24 |  CNN  | Yes |
| U-Mamba Bot [8]        | 83.51 | 91.79 | 80.40 | 91.26 | 86.22 | 89.13 | 12.40 |  24 |  Mam  | Yes |
| U-Mamba Enc [8]        | 82.41 | 91.22 | 80.27 | 90.91 | 86.34 | 88.38 | 24.90 |  47 |  Mam  | Yes |
| A3DS SegResNet [9,11]  | 80.69 | 90.69 | 79.28 | 90.79 | 81.11 | 87.27 | 20.00 |  22 |  CNN  |  No |
| A3DS DiNTS [10, 11]    | 78.18 | 82.97 | 69.05 | 87.75 | 65.28 | 82.35 | 29.20 |  16 |  CNN  |  No |
| A3DS SwinUNETR [4, 11] | 76.54 | 82.68 | 68.59 | 89.90 | 52.82 | 85.05 | 34.50 |  9  |   TF  |  No |

结果取自我们的论文（见上文），报告的值是每个数据集上5折交叉验证计算的Dice分数。所有模型都从头开始训练。

RT: 训练运行时间（在1x Nvidia A100 PCIe 40GB上测量）\
VRAM: 训练期间使用的GPU显存，如nvidia-smi所报告\
Arch.: CNN = 卷积神经网络；TF = 变压器；Mam = Mamba\
nnU: 该架构是否在nnU-Net框架中进行集成和测试（由我们或原作者）

## 如何使用新预设

我们提供了三个新的预设，每个预设针对不同的GPU显存和计算预算：
- **nnU-Net ResEnc M**: 类似于标准UNet配置的GPU预算。最适合具有9-11GB显存的GPU。训练时间：A100上约12小时
- **nnU-Net ResEnc L**: 需要具有24GB显存的GPU。训练时间：A100上约35小时
- **nnU-Net ResEnc XL**: 需要具有40GB显存的GPU。训练时间：A100上约66小时

### **:point_right: 我们推荐 **nnU-Net ResEnc L** 作为新的默认nnU-Net配置！ :point_left:**

新预设的使用方式如下（M/L/XL = 任选一个！）：
1. 在运行实验计划和预处理时指定所需的配置： 
`nnUNetv2_plan_and_preprocess -d DATASET -pl nnUNetPlannerResEnc(M/L/XL)`。这些计划器使用与标准2d和3d_fullres配置相同的预处理数据文件夹，因为预处理的数据是相同的。只有3d_lowres不同，并将保存在不同的文件夹中，以允许所有配置共存！如果您只计划运行3d_fullres/2d，并且您已经预处理了这些数据，您可以直接运行 
`nnUNetv2_plan_experiment -d DATASET -pl nnUNetPlannerResEnc(M/L/XL)` 来避免重新预处理！ 
2. 现在，只需在运行 `nnUNetv2_train`，`nnUNetv2_predict` 等时指定正确的计划。所有nnU-Net命令的接口是一致的：`-p nnUNetResEncUNet(M/L/XL)Plans`  

新预设的训练结果将存储在一个专用文件夹中，不会覆盖标准nnU-Net结果！所以不用担心，尽管尝试吧！

## 超越预设扩展ResEnc nnU-Net
预设与 `ResEncUNetPlanner` 的两个区别在于：
- 它们为 `gpu_memory_target_in_gb` 设置了新的默认值，以达到相应的显存消耗
- 它们移除了批量大小上限0.05（= 之前一个批次不能覆盖整个数据集的像素超过5%，现在可以任意大）

预设只是为了让生活更轻松，并提供标准化的配置供人们进行基准测试。
您可以轻松调整GPU内存目标以匹配您的GPU，并扩展到超过40GB的GPU内存。

以下是如何在Dataset003_Liver上扩展到80GB VRAM的示例：

`nnUNetv2_plan_experiment -d 3 -pl nnUNetPlannerResEncM -gpu_memory_target 80 -overwrite_plans_name nnUNetResEncUNetPlans_80G`

只需按照上述方法使用 `-p nnUNetResEncUNetPlans_80G`！运行上述示例将产生一个警告（“您正在以非标准gpu_memory_target_in_gb运行nnUNetPlannerM”）。这里可以忽略这个警告。
**在处理VRAM目标时总是使用 `-overwrite_plans_name NEW_PLANS_NAME` 更改计划标识符，以免覆盖预设计划！**

为什么不使用 `ResEncUNetPlanner` -> 因为那个仍然有5%的上限！

### 扩展到多个GPU
在扩展到多个GPU时，不要只为 `nnUNetv2_plan_experiment` 指定组合的VRAM量，因为这可能导致补丁大小过大，无法被单个GPU处理。最好让此命令针对一个GPU的VRAM预算运行，然后手动编辑计划文件以增加批量大小。您可以使用[配置继承](explanation_plans_files.md)。
在生成的计划JSON文件的配置字典中，添加以下条目：

```json
        "3d_fullres_bsXX": {
            "inherits_from": "3d_fullres",
            "batch_size": XX
        },
```
其中 XX 是新的批处理大小。如果 3d_fullres 在一个 GPU 上的批处理大小为 2，而您计划扩展到 8 个 GPU，则将新批处理大小设为 2x8=16！然后，您可以使用 nnU-Net 的多 GPU 设置训练新配置：
```
nnUNetv2_train DATASETID 3d_fullres_bsXX FOLD -p nnUNetResEncUNetPlans_80G -num_gpus 8
```

