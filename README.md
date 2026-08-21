这里放置ucf101-caffe.pth权重文件

项目简介
C3D（Convolutional 3D）是经典三维卷积神经网络，使用 3D 卷积核同时在空间维度 + 时间维度提取视频时空特征，非常适合视频动作识别任务。
本仓库实现完整 C3D 训练、数据处理、数据集加载、推理全流程：
支持 UCF‑101 动作数据集训练；
支持加载预训练权重ucf101‑caffe.pth迁移学习，加速收敛、提升精度；
支持输入任意 avi 视频，输出动作类别预测结果；
完整的视频抽帧、归一化、数据增强预处理逻辑。
论文参考：Learning Spatiotemporal Features with 3D Convolutional Networks


video‑recognition/
├── C3D_model.py      # C3D网络模型定义，卷积、池化、全连接层网络结构
├── data_process.py   # 视频预处理工具：视频读取、抽帧、resize、归一化、帧采样
├── dataset.py        # PyTorch Dataset数据集类，读取视频，构建训练/验证数据集
├── train.py          # 训练脚本：加载数据集、模型训练、验证、保存模型权重
├── inference.py      # 推理脚本：输入单条视频，输出动作识别预测结果
├── README.md         # 项目说明文档
├── v_YoYo_g01_c01.avi # 示例测试视频（UCF101 YoYo类别样例）
├── file.png          # 项目效果图/示意图
└── ucf101‑caffe.pth  #【需要自行下载放入根目录】C3D预训练权重


环境依赖
建议使用 Python3.8~3.11
bash
pip install torch torchvision opencv‑python numpy
torch >=1.10
opencv‑python >=4.5
numpy >=1.21
推荐 GPU 环境（CUDA）；CPU 也可以运行，但训练速度非常慢。


数据集准备（UCF‑101）
UCF‑101 是视频动作识别数据集，共 101 个动作类别，约 13000 条 avi 视频。
下载 UCF‑101 原始视频数据集；
下载 UCF101 官方训练 / 测试划分标注文件；
修改dataset.py中的数据集路径，指向你的 UCF‑101 视频根目录；
将预训练权重文件ucf101‑caffe.pth放到项目根目录。
如果你仅做推理测试，不需要完整 UCF‑101 数据集，可以直接使用仓库自带示例视频v_YoYo_g01_c01.avi。


 运行指南
1. 克隆仓库
bash
git clone https://github.com/PassionCR7/video‑recognition.git
cd video‑recognition
2. 预训练权重准备
⚠️仓库不包含权重文件，需要自行下载ucf101‑caffe.pth放到项目根目录，否则训练无法加载预训练参数。
3. 模型训练
修改train.py内参数：数据集路径、batch_size、epochs、学习率、设备 cuda/cpu。
bash
python train.py
训练过程：
dataset.py读取 UCF101 视频与标签；
data_process.py做视频抽帧预处理；
加载 C3D 模型，可选加载ucf101‑caffe.pth预训练权重；
循环训练 + 验证，训练结束保存训练好的模型权重。
提示：3DCNN 参数量大，GPU 显存不足需要调小batch_size。
4. 单视频推理预测
使用inference.py对单个视频做动作识别。
修改脚本内视频路径，可以使用示例视频：v_YoYo_g01_c01.avi
bash
python inference.py
程序输出：预测类别 ID、对应动作名称、置信度。


使用说明与注意事项
输入视频要求：支持 avi 格式；代码内部会自动采样固定帧数作为 C3D 网络输入（C3D 标准输入片段为 16 帧）；
预训练权重：强烈建议加载ucf101‑caffe.pth，随机初始化从零训练收敛极慢，效果差；
类别修改：如果要训练自定义数据集，修改C3D_model.py中num_classes参数，改为你的数据集类别总数；
路径报错：所有视频、权重文件路径需要核对，Windows 和 Linux 路径写法有区别；
训练时，训练集 / 验证集划分逻辑在dataset.py中，可按需调整。

常见问题
权重加载报错：检查ucf101‑caffe.pth文件路径是否正确；确认 pth 文件完整没有损坏。
显存 OOM：调小 train.py 中的batch_size；使用更小输入分辨率。
OpenCV 读取视频返回空帧：视频文件损坏、编码不兼容，优先使用 avi 格式视频。
CPU 训练速度极慢：3D 卷积计算量大，建议使用 NVIDIA GPU+CUDA。

拓展方向
更换其他视频数据集 (HMDB51 等)；
增加 TensorBoard 日志可视化训练过程；
增加视频文件批量推理；
对比 I3D、R (2+1) D 等其他视频识别模型。
📄 License
本项目仅供学习研究使用。
