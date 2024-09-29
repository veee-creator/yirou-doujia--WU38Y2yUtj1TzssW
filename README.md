
![](https://img2024.cnblogs.com/blog/335778/202409/335778-20240928102410188-1308925335.jpg)


FaceFusion3\.0\.0大抵是现在最强的AI换脸项目，分享一下如何在Win11系统，基于最新的cuda12\.6配合最新的cudnn9\.4本地部署FaceFusion3\.0\.0项目，并且搭配Tensorrt10\.4，提高推理速度和效率，让甜品级显卡也能爆发生产力。


## 安装最新版本Cuda12\.6以及Cudnn9\.4


CUDA是NVIDIA公司开发的一种技术，它能让GPU像CPU一样编程，让GPU也能参与到计算中来，从而加速计算过程。你可以把它想象成一种“语言”，让程序员可以指挥GPU的“工人”们一起工作。


cuDNN则是专门为深度学习设计的“工具箱”。深度学习就像盖房子，需要很多“积木”块，比如卷积、池化等操作。cuDNN提供了这些预先优化好的“积木”，让程序员可以直接使用，而不用自己从头开始编写这些复杂的代码，从而大大提高了深度学习模型的训练和推理速度。 它就像一个经验丰富的建筑工人，能快速高效地完成盖房子的工作。


安装包可以去 Nvidia 官方网站进行下载，但是必须登录Nvidia账号，这里为大家下载好了最新的安装包：



```
https://pan.quark.cn/s/bc3ab3494596

```

首先双击 cuda\_12\.6\.1\_560\.94\_windows.exe 进行安装，注意不要安装到C盘，因为太占地方，建议在别的盘符建立12\.6目录，然后进行安装即可。


安装成功后，运行命令进行检查：



```
(base) PS C:\Users\zcxey> nvcc -V  
nvcc: NVIDIA (R) Cuda compiler driver  
Copyright (c) 2005-2024 NVIDIA Corporation  
Built on Wed_Aug_14_10:26:51_Pacific_Daylight_Time_2024  
Cuda compilation tools, release 12.6, V12.6.68  
Build cuda_12.6.r12.6/compiler.34714021_0  
(base) PS C:\Users\zcxey>

```

可以看到显示的版本是 12\.6


随后打开 cudnn\-windows\-x86\_64\-9\.4\.0\.58\_cuda12\-archive 目录，把其中的 bin、include以及lib目录直接拷贝覆盖到 cuda 的安装目录即可。至此，cuda12\.6和其对应的cudnn9\.4就安装好了，注意版本号必须吻合。


## 安装Tensorrt10\.4


关于Tensorrt，想象一下你训练好了一只非常聪明的狗狗（你的深度学习模型），它已经学会了识别各种猫和狗的图片。但是，这只狗狗每次识别图片都需要很长时间，效率不高。


TensorRT就像一个训练师，它能帮助你把这只狗狗训练得更加高效。它会优化狗狗的识别方法，让它能够更快更准确地识别图片，并且消耗更少的能量。 所以，用TensorRT优化后的模型，就能在你的电脑或服务器上更快地进行推理（识别图片），从而节省时间和资源。


Tensorrt主要针对的是已经训练好的模型，而不是训练模型本身。 它就像一个专业的优化器，让你的模型在实际应用中跑得更快更省力。


打开 TensorRT\-10\.4\.0\.26 目录，把 lib 目录下的所有动态库 dll 文件全部拷贝到 cuda12\.6 安装目录的 bin目录下即可：



```
Directory of D:\12.6\bin  
  
2024/09/27  11:08              .  
2024/09/27  10:48              ..  
2024/08/15  02:14           228,352 bin2c.exe  
2024/08/15  02:01                66 compute-sanitizer.bat  
2024/09/27  10:48              crt  
2024/08/15  02:11           202,752 cu++filt.exe  
2024/08/15  02:34       100,806,656 cublas64_12.dll  
2024/08/15  02:34       510,903,296 cublasLt64_12.dll  
2024/08/15  02:14         7,739,904 cudafe++.exe  
2024/08/15  02:11           556,544 cudart64_12.dll  
2023/11/30  16:26           288,296 cudnn64_8.dll  
2024/09/01  04:24           265,272 cudnn64_9.dll  
2024/09/01  04:24       243,945,512 cudnn_adv64_9.dll  
2023/11/30  16:26       125,217,320 cudnn_adv_infer64_8.dll  
2023/11/30  16:26       116,558,888 cudnn_adv_train64_8.dll  
2024/09/01  04:24         4,002,872 cudnn_cnn64_9.dll  
2023/11/30  16:26       582,690,344 cudnn_cnn_infer64_8.dll  
2023/11/30  16:26       122,242,104 cudnn_cnn_train64_8.dll  
2024/09/01  04:24       432,804,904 cudnn_engines_precompiled64_9.dll  
2024/09/01  04:24        16,297,000 cudnn_engines_runtime_compiled64_9.dll  
2024/09/01  04:25         2,063,400 cudnn_graph64_9.dll  
2024/09/01  04:25        44,681,784 cudnn_heuristic64_9.dll  
2024/09/01  04:25       107,492,904 cudnn_ops64_9.dll  
2023/11/30  16:26        89,759,272 cudnn_ops_infer64_8.dll  
2023/11/30  16:26        70,162,472 cudnn_ops_train64_8.dll  
2024/08/15  03:03       275,258,368 cufft64_11.dll  
2024/08/15  03:03           163,328 cufftw64_11.dll  
2024/08/15  02:45         1,513,984 cuinj64_126.dll  
2024/08/15  02:11        11,713,024 cuobjdump.exe  
2024/08/15  02:25        63,279,104 curand64_10.dll  
2024/08/15  04:12       116,768,256 cusolver64_11.dll  
2024/08/15  04:11        77,813,248 cusolverMg64_11.dll  
2024/08/15  03:09       287,497,216 cusparse64_12.dll  
2024/08/15  02:14           881,664 fatbinary.exe  
2024/08/15  03:20           292,352 nppc64_12.dll  
2024/08/15  03:20        16,235,008 nppial64_12.dll  
2024/08/15  03:20         6,234,624 nppicc64_12.dll  
2024/08/15  03:20         9,865,728 nppidei64_12.dll  
2024/08/15  03:20        96,892,416 nppif64_12.dll  
2024/08/15  03:20        39,228,416 nppig64_12.dll  
2024/08/15  03:20         9,341,952 nppim64_12.dll  
2024/08/15  03:20        36,831,232 nppist64_12.dll  
2024/08/15  03:20           265,728 nppisu64_12.dll  
2024/08/15  03:20         4,221,440 nppitc64_12.dll  
2024/08/15  03:20        12,687,872 npps64_12.dll  
2024/08/15  02:34           331,776 nvblas64_12.dll  
2024/08/15  02:14        14,029,824 nvcc.exe  
2024/08/15  02:14               343 nvcc.profile  
2024/08/15  02:11        50,708,480 nvdisasm.exe  
2024/08/15  02:14           838,656 nvfatbin_120_0.dll  
2024/08/30  19:47       215,426,088 nvinfer_10.dll  
2024/08/30  19:46             5,688 nvinfer_10.lib  
2024/08/30  19:48     1,436,593,704 nvinfer_builder_resource_10.dll  
2024/08/30  19:47           616,488 nvinfer_dispatch_10.dll  
2024/08/30  19:46             4,362 nvinfer_dispatch_10.lib  
2024/08/30  19:46        29,457,448 nvinfer_lean_10.dll  
2024/08/30  19:46             5,104 nvinfer_lean_10.lib  
2024/08/30  19:47        30,986,792 nvinfer_plugin_10.dll  
2024/08/30  19:46             2,564 nvinfer_plugin_10.lib  
2024/08/30  19:47           565,288 nvinfer_vc_plugin_10.dll  
2024/08/30  19:46             2,374 nvinfer_vc_plugin_10.lib  
2024/08/15  02:13        38,856,192 nvJitLink_120_0.dll  
2024/08/15  02:23         4,901,888 nvjpeg64_12.dll  
2024/08/15  02:14        20,608,000 nvlink.exe  
2024/08/30  19:47         3,064,872 nvonnxparser_10.dll  
2024/08/30  19:46             2,524 nvonnxparser_10.lib  
2024/08/15  02:45         2,210,304 nvprof.exe  
2024/08/15  02:11           254,464 nvprune.exe  
2024/08/15  02:11         5,345,792 nvrtc-builtins64_126.dll  
2024/08/15  02:11        45,535,744 nvrtc64_120_0.alt.dll  
2024/08/15  02:11        45,475,328 nvrtc64_120_0.dll  
2024/08/15  03:45               129 nvvp.bat  
2024/08/15  02:14        20,220,416 ptxas.exe  
2024/08/15  02:14            84,480 __nvcc_device_query.exe  
              71 File(s)  5,612,029,986 bytes  
               3 Dir(s)  128,267,644,928 bytes free

```

至此，就完成了 Tensorrt10\.4 的安装。


## 安装和部署FaceFusion3\.0\.0


首先确保本地已经安装好 Python3\.11 的开发环境，随后克隆官方项目:



```
git clone https://github.com/facefusion/facefusion.git
cd facefusion

```

安装基础依赖:



```
pip3 install -r requirements.txt

```

接着安装 onnxruntime\-gpu:



```
pip3 install onnxruntime-gpu

```

ONNX Runtime\-GPU 是一个高性能的推理引擎，它能够运行使用 ONNX (Open Neural Network Exchange) 格式表示的机器学习模型。 关键在于“GPU”部分，这意味着它专门针对 NVIDIA 的图形处理器 (GPU) 进行优化，以实现比在 CPU 上运行模型更快的速度和更高的效率。


注意默认安装的onnxruntime\-gpu版本是19\.2，它专门是为cuda12适配的。


安装 tensorrt 库：



```
pip3 install tensorrt==10.4.0 --extra-index-url https://pypi.nvidia.com

```

这里是安装 tensorrt 的python3\.11运行库


最后安装torch:



```
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu124

```

注意后缀是cu124，而不是cu118或者cu121


安装成功后，进入 python3\.11 的终端：



```
>>> import onnxruntime as ort  
>>> print(ort.get_available_providers())  
['TensorrtExecutionProvider', 'CUDAExecutionProvider', 'CPUExecutionProvider']

```

如果三种后端支持都被打印出来了，分别是 cpu、cuda以及Tensorrt 那么说明配置和安装都成功了。


运行命令:



```
python3 facefusion.py run

```

进入换脸主界面:


![](https://v3u.cn/v3u/Public/js/editor/attached/20240927130915_55137.png)


由于有了Tensorrt的加持，也支持实时换脸，进入摄像头换脸界面：



```
python3 facefusion.py run --ui-layouts webcam

```

![](https://v3u.cn/v3u/Public/js/editor/attached/20240927130922_20655.png)


摄像头换脸效果：


![](https://v3u.cn/v3u/Public/js/editor/attached/20240927130921_14516.png)


最后，需要注意的是，FaceFusion3\.0\.0需要本地安装ffmpeg软件：



```
winget install -e --id Gyan.FFmpeg

```

 本博客参考[悠兔机场](https://xinnongbo.com)。转载请注明出处！
