# Realtime_Multi-Person_Pose_Estimation 
## 训练

-   `git clone https://github.com/ZheC/Realtime_Multi-Person_Pose_Estimation.git;  cd training`
-   下载[COCO2014](http://cocodataset.org/#download)，解压后test2014,test2015,train2014,val2014放入(mkdir创建)`dataset/COCO/images/` ,两个json文件放入(mkdir创建） `dataset/COCO/annotations/`, 
     `git clone https://github.com/cocodataset/cocoapi.git` , 放入（mkdir创建）`dataset/COCO/coco/`中。
-    安装matlab[安装matlab教程](http://blog.csdn.net/u014696921/article/details/70053059) ，在终端输入matlab启动matlab。
-    在matlab软件中运行`getANNO.m `文件，把标注文件格式由json 转换成.mat 格式，会存在`dataset/COCO/mat/`。
-    在matlab中运行`genCOCOMask.m `，获得未标注的人的图像的mask， 可以把程序中第2个for循环的for改成 parfor来获得并行计算的加速。
-    在终端运行`genJSON('COCO')`，在`/COCO/json/`目录下生成json文件， json文件中包含需要训练的原始信息。
-    在终端运行 `python genLMDB.py` 来生成LMDB（caffe训练需要的数据格式）文件，. 
-    下载修改后的caffe，并编译安装，在环境配置中已经完成。
-    在终端运行`python setLayers.py --exp 1` 来获得 prototxt文件和训练所需要的 shell 文件，`setLayers.py`中把对应的路径换成自己的路径，源代码中有几处需要改动（修正）：

     ` --weights = ../../../../model`, 多加了一个`../` , dataFolder和source路径最后`/lmdb_trainVal`改成`/lmdb`。
     
-    下载[VGG-19 model](<script src="https://gist.github.com/ksimonyan/3785162f95cd2d5fee77.js"></script>), 用来初始化前10层，将vgg模型文件放在`~/model/vgg`中。
-    进入对应目录（生成shell文件和prototxt文件的目录），运行`bash train_pose.sh 0,1,2,3` ，原文用两块GPU，这里要改成4块。
-    重写`train_pose.sh`文件，主要作用是添加纪录训练过程的loss日志，如果中间中断训练，从中断处重新开始的话，去掉`--weight=`语句段，换成`--snapshot=`语句段，生成的log日志文件
     如何解析可视化，详细在`loss可视化.md`。

-    将用mobilenet的前10层取代vgg，重写`pose_solver_mobile.prototxt` 和`pose_train_test_mobile.prototxt`文件，然后进行训练。

## 测试
