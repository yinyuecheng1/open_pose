# ubuntu16.0+cuda8.0+cudnn5.0+caffe_train(修改过的）安装配置
By yinyuecheng

## cuda8.0安装教程
 - 下载cuda [cuda8.0](https://developer.nvidia.com/cuda-downloads)
 - 安装cuda

    （1）.为了方便开始安装过程的路径查找，把下载的 CUDA 安装文件移动到 HOME 路径下，然后通过 Ctrl + Alt + F1 进入文本模式，
     输入帐号密码登录，通过 Ctrl + Alt + F7 可返回图形化模式，在文本模式登录后首先关闭桌面服务：
    
    sudo service lightdm stop

    然后，然后通过 Ctrl + Alt + F7 发现已无法成功返回图形化模式，说明桌面服务已成功关闭，注意此步对接下来的 nvidia 驱动安装尤为重要（已安装显卡驱动请忽略），
    必需确保桌面服务已关闭。

    （2）Ctrl + Alt + F1 进入文本模式，然后运行 CUDA 安装文件进行安装，之前我们已经把 CUDA 安装文件移动至 HOME，
    直接通过 sh 命令运行安装文件即可：

    sudo sh cuda_8.0.61_375.26_linux.run --no-opengl-libs

     执行此命令约1分钟后会出现 0%信息，此时长按回车键让此百分比增长，直到100%，然后按照提示操作即可，先输入 accept ，
     然后让选择是否安装 nvidia 驱动，这里的选择对应第5步开头，若未安装则输入 “y”，若确保已安装正确驱动则输入“n”。
     剩下的选择则都输入“y”确认安装或确认默认路径安装，开始安装，
     此时若出现安装失败提示则可能为未关闭桌面服务或在已安装 nvidia 驱动的情况下重复再次安装 nvidia 驱动，安装完成后输入重启命令重启：

     reboot

     重启后登录进入系统，配置 CUDA 环境变量，使用 gedit 命令打开配置文件：

     sudo gedit ~/.bashrc

     在该文件最后加入以下两行并保存：

     export PATH=/usr/local/cuda/bin:$PATH

     export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH

     使该配置生效：

     source ~/.bashrc

    （3）验证cuda 是否安装成功
     分别执行如下命令：

     cd /usr/local/cuda/samples/1_Utilities/deviceQuery

     sudo make

    ./deviceQuery

     若看到如下信息，则说明cuda已经成功安装
     ./deviceQuery Starting...

     deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 8.0, CUDA Runtime Version = 8.0, NumDevs = 1, Device0 = GeForce GT 740M
     ...

     Result = PASS


## cudnn5.0 安装教程
- 下载cudnn5.0 [cudnn](https://developer.nvidia.com/rdp/cudnn-download) 注意官网下载 cudnn 需要注册帐号并登录

- 解压并复制文件，建立软连接:

   $ cp cudnn-8.0-linux-x64-v6.0.solitairetheme8 cudnn-8.0-linux-x64-v6.0.tgz
   $ tar -xvf cudnn-8.0-linux-x64-v6.0.tgz

   下载完成后解压，得到一个 cudn 文件夹，该文件夹下include 和 lib64 两个文件夹，命令行进入 cudn/include 路径下，然后进行以下操作：

   sudo cp cudnn.h /usr/local/cuda/include/ #复制头文件

   然后命令行进入 /lib64 路径下，运行以下命令：
 
   sudo cp lib* /usr/local/cuda/lib64/ #复制动态链接库
   
   cd /usr/local/cuda/lib64/sudo rm -rf libcudnn.so libcudnn.so.5 #删除原有动态文件
   
   sudo ln -s libcudnn.so.5.1.10 libcudnn.so.5 #生成软衔接
   
   sudo ln -s libcudnn.so.5 libcudnn.so #生成软链接

   就是把cudnn中的文件复制到对应的cuda安装路径下就行，现在就等着caffe编译的时候用到它。
- Tip1：装完cuda会发现/usr/local下有两个文件夹cuda和cuda-8.0，cuda是软链接到cuda-8.0的，所以这两个文件夹可以看成一个。往任意一个文件夹中添加东西，另一个文件夹都会有相同的东西。

- Tip2：解压出来的/home/*(用户名)/下载/lib64下面有3个.so文件。分别是libcudnn.so和 libcudnn.so.5以及 libcudnn.so.5.1.10文件。并且这3个.so文件大小都一样。其实都是软连接！libcudnn.so链接到libcudnn.so.5，而libcudnn.so.5.又链接到libcudnn.so.5.1.10。真正的文件只有libcudnn.so.5.1.10。其中libcudnn.so.5的尾数5代表cudnn的版本号，这个后面会用到。


## 编译caffe_train
- git clone https://github.com/CMU-Perceptual-Computing-Lab/caffe_train.git
- 生成Makefile.config

   进入 caffe ，将 Makefile.config.example 文件复制一份并更名为 Makefile.config ，也可以在 caffe 目录下直接调用以下命令完成复制操作 ：

   sudo cp Makefile.config.example Makefile.config

   复制一份的原因是编译 caffe 时需要的是 Makefile.config 文件，而Makefile.config.example 只是caffe 给出的配置文件例子，不能用来编译 caffe。
- 修改 Makefile.config 文件，在 caffe 目录下打开该文件：

   sudo gedit Makefile.config

   修改 Makefile.config 文件内容：

   （1）应用 cudnn
   将USE_CUDNN := 1前的注释去掉

   （2）.使用 python 接口
   将WITH_PYTHON_LAYER := 1前的注释去掉

   （3）修改 python 路径
   将
   INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include
   LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib 
   修改为： 
   INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial
   LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial

- 然后修改 caffe 目录下的 Makefile 文件：
   

   将NVCCFLAGS +=-ccbin=$(CXX) -Xcompiler-fPIC $(COMMON_FLAGS)
   替换为：
   NVCCFLAGS += -D_FORCE_INLINES -ccbin=$(CXX) -Xcompiler -fPIC $(COMMON_FLAGS)

   将LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_hl hdf5
   改为：
   LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_serial_hl hdf5_serial
   
- 修改 /usr/local/cuda/include/host_config.h 文件 :


   将#error-- unsupported GNU version! gcc versions later than 4.9 are not supported!
   改为//#error-- unsupported GNU version! gcc versions later than 4.9 are not supported!

- 开始编译

 （1）在 caffe 目录下执行 ：

  sudo make all -j8

 （2）编译成功后运行测试

  sudo make runtest -j8

 （3）编译pycaffe

  sudo make pycaffe -j8

 （4）进入python环境，import caffe
  出现error：File "<stdin>", line 1, in <module>   ImportError: No module named caffe
  解决方法：

  sudo echo export PYTHONPATH="~/caffe/python" >> ~/.bashrc

  source ~/.bashrc

  出现error：ImportError: No module named skimage.io
  解决方法：pip install -U scikit-image #若没有安装pip: sudo apt install python-pip

## caffe_train编译过程中出现的问题
- cblas:no such file or directory

   [解决办法](https://github.com/BVLC/caffe/issues/559)
- cuda程序执行出错: libcudart.so.8.0: cannot open shared object file: No such file or directory

   [解决办法](http://blog.csdn.net/u010454261/article/details/71268325)

- Realtime_Multi-Person_Pose_Estimation-master repo 不支持cudnn6 ， 只能使用cudnn5 ，
  make all 成功后，进行make runtest 的时候出现“check failed: statatus == CUDNN_STATUS_SUCCESS (3 vs. o) CUDNN_STATUS_BAD_PARAM”

  解决方法：把cudnn删除干净，再重新安装

- 环境变量更改：

  sudo gedit /etc/profile

  在文件下方写入

  export PYTHONPATH=~/caffe/python:$PYTHONPATH

  source /etc/profile使其生效

- ./usr/bin/ld: cannot find -lcudnn

  解决方法：命令行下输入

  export LD_LIBRARY_PATH=/usr/local/cuda/lib64/
- 运行caffe过程中出现bug：Check failed:error == cudaSuccess(30 vs. 0)  unknown error.

  解决办法：bash pose_train.sh 0,1,2,3 的前面加sudo
  
## 官方caffe安装教程
(http://caffe.berkeleyvision.org/installation.html)
