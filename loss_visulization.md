# 解析log日志，可视化训练过程中的loss曲线
## 标准的caffe源码解析训练过程的loss。
- 首先在`train_loss.sh` 脚本后添加 `| tee path/to/train_log.log`，在指定路径下生成`train_log.log`文件。   
- 绘制曲线。训练结束后会在指定路径下生成log文件，然后将文件夹caffe/tools/extra下的parse_log.sh 、extract_seconds.py、plot_training_log.py.example
  复制到log日志文件的保存目录下，将plot_training_log.py.example改为plot_training_log.py，并执行以下命令就可以绘制曲线：
  `python plot_training_log.py 6 train_loss.png train_log.log`
- caffe支持多种曲线绘制，指定不同的类型参数即可，具体参数如下:
   Supported chart types:   
    0: Test accuracy  vs. Iters   
    1: Test accuracy  vs. Seconds   
    2: Test loss  vs. Iters   
    3: Test loss  vs. Seconds   
    4: Train learning rate  vs. Iters   
    5: Train learning rate  vs. Seconds   
    6: Train loss  vs. Iters   
    7: Train loss  vs. Seconds

## 修改版解析L1和L2曲线
- 修改`parse_log.sh` 脚本。
- 修改`plot_training_log.py`文件。
- `python plot_training_log.py 0 train_loss.png train_log.log`
   其中，0可以换为一下几种：（支持4种曲线绘制）

    0. Loss_total vs. Iters
    1. LearningRate vs. Iters
    2. loss_L1 vs. Iters
    3. loss_L2 vs. Iters
