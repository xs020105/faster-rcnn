## 概述
根据RBG大神的faster-rcnn代码调整，主要是去除了标注越界错误，numpy更新错误以及cudnn7的支持。建议使用官方原生的python去编译caffe。具体命令顺序如下
```bash
pip install cython  
pip install easydict  
sudo apt-get install python-opencv
git clone https://github.com/xs020105/faster-rcnn.git
cd py-faster-rcnn/lib
make
cd ../caffe-fast-rcnn
make -j8 && make pycaffe
```
随后将PYTHONPATH添加到环境变量中：
export PYTHONPATH=~/py-faster-rcnn/caffe-fast-rcnn/python:$PYTHONPATH 
## 数据准备
### 数据集放置
自己的数据集可以放在py-faster-rcnn/data/VOCdevkit2007/VOC2007中。
### 修改类别参数
在py-faster-rcnn/models/pascal_voc/ZF/faster_rcnn_alt_opt/stage1_fast_rcnn_train.pt和stage2_fast_rcnn_train.pt中修改所有num_class值为自己的类别数量加一。
bbox_pred层的num_output改为类别数加一再乘以4。
py-faster-rcnn/models/pascal_voc/ZF/faster_rcnn_alt_opt/stage1_rpn_train.pt和stage2_rpn_train.pt修改input-data层的num_class。
py-faster-rcnn/models/pascal_voc/ZF/faster_rcnn_alt_opt/faster_rcnn_test.pt修改cls_score层的num_output为类别数量加一。
bbox_pred的num_output为类别数加一以后再乘4。
### 修改标签参数
pascal_voc中需要修改自己的标签，标签请用小写字母。
### 修改其他参数
学习率等之类的设置，可在py-faster-rcnn/models/pascal_voc/ZF/faster_rcnn_alt_opt中的solve文件设置，迭代次数可在py-faster-rcnn/tools的train_faster_rcnn_alt_opt.py中修改。
## 开始训练
```bash
./experiments/scripts/faster_rcnn_alt_opt.sh 0 ZF pascal_voc
```
## 测试
### 放置模型
将训练得到的py-faster-rcnn/output/faster_rcnn_alt_opt/***_trainval中ZF的caffemodel拷贝至py-faster-rcnn/data/faster_rcnn_models（如果没有这个文件夹，就新建一个）
### 修改标签
修改：py-faster-rcnn/tools/demo.py
其中CLASSES改为自己的标签。
随后执行
```bash
./tools/demo.py --net zf  
```
可以看到各个类的检测结果被保存在第一级目录下 