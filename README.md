# Image Segment C++ with TensorRT deployment on Nvidia GTX1650Ti
图像语义分割算法HRNet使用TensorRT与VS2019，并使用GTX1650显卡加速推理的过程。

## 软件环境
win10, cuda = 11.2，cudnn = 8.1.1，TensorRT= 8.2.5， g++ >= 5.4 (推荐8.2)，Cmake>=3.10， VS2019，其余包都不需要安装了，opencv包会在代码编译过程中自动下载

## 安装与配置
下载提到的安装包并安装，设置环境变量等。关于cuda、cudnn以及TensorRT的安装与环境变量配置，可以参考：[WIN10安装配置TensorRT详解](https://blog.csdn.net/caobin_cumt/article/details/125579033?ops_request_misc=&request_id=&biz_id=102&utm_term=window%E4%B8%8Bscuda%E4%B8%8EtensorRT%E5%AE%89%E8%A3%85&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-125579033.142^v56^js_top,201^v3^add_ask&spm=1018.2226.3001.4187) 

## 下载与编译部署库
1. 下载预先编译CPP部署库 : [FastDeploy 预编译 C++ 库](https://bj.bcebos.com/fastdeploy/release/cpp/fastdeploy-win-x64-0.2.1.zip).
自行编译库的方式可以先下载[压缩包](https://github.com/PaddlePaddle/FastDeploy/archive/refs/heads/develop.zip)，然后再参考：[FastDeploy Windows SDK 编译](https://github.com/PaddlePaddle/FastDeploy/blob/develop/docs/compile/how_to_build_windows.md#Usage)。
2. 为了方便，这里简略地给出使用Cmake自行编译的步骤：
* 首先，在解压下载的压缩包之后，在文件夹里新建build文件，打开CMake GUI，`source code`选择下载的FastDeploy-develop文件夹，`build`目录则选择刚创建的build目录。
* 其次，`Configure`选择x64。
* 编译后的结果以及选择如下，由于我们需要使用TensorRT以及GPU，因此CUDA_DIRECTORY与TRT_DIRECTORY需要填写Cuda与TensorRT安装路径。最重要的是WITH_GPU与ENABLE_TRT_BACKEND需要打勾，`CMAKE_CONFIGURATION_TYPES`修改成`Release`，其余设置可以参考我的做法，opencv等库文件在`Generate`过程中下载以及include路径和lib路径的填充。所有编译选项的解释可以[在此查看](https://github.com/PaddlePaddle/FastDeploy/tree/develop/docs/compile)
![image](https://user-images.githubusercontent.com/114713988/195863570-e03dbe66-e04c-43e4-88ff-8f39a83d0825.png)
![image](https://user-images.githubusercontent.com/114713988/195863707-d47441a0-1bf1-4016-ae48-665abfa38c2c.png)
* 自定义设置SDK安装路径，修改CMAKE_INSTALL_PREFIX，我这里是设置的`D:/D/FastDeploy-develop/build/generate_SDK`
* 点击"Generate"，生成sln解决方案，并用Visual Studio 2019打开。打开build目录，可以看到已经生成fastdeploy.sln解决方案文件，使用Visual Studio 2019打开这个解决方案文件。
* 在Visual Studio 2019点击`ALL BUILD`->右键点击`生成`开始编译。
* 编译完成后，在Visual Studio 2019点击`INSTALL`->右键点击`生成`将编译好的SDK安装到先前指定的目录，到此部署库已经编译完成。

## 图像分割使用TensorRT在GPU上加速推理
代码可以直接使用已给出的代码。步骤具体可以参考[在 Windows 使用 FastDeploy C++ SDK](https://github.com/PaddlePaddle/FastDeploy/blob/develop/docs/compile/how_to_use_sdk_on_windows.md#32-sdk%E4%BD%BF%E7%94%A8%E6%96%B9%E5%BC%8F%E4%BA%8Cvisual-studio-2019-%E5%88%9B%E5%BB%BA-sln-%E5%B7%A5%E7%A8%8B%E4%BD%BF%E7%94%A8-c-sdk)：
1. 首先在VS2019创建一个控制台应用，然后将上面给出的C++推理代码作为源文件。
2. 将工程配置设置成"Release x64"配置。配置头文件include路径，点击项目属性，选择C/C++ —> 常规 —> 附加包含目录，然后在添加 fastdeploy 和 opencv 的头文件路径。如D:\D\FastDeploy-develop\build\generate_SDK\include 以及 D:\D\FastDeploy-develop\build\generate_SDK\third_libs\install\opencv\build\include
3. 配置lib路径和添加库文件，选择链接器—>常规—> 附加库目录，然后在添加 fastdeploy 和 opencv 的lib路径。如 D:\D\FastDeploy-develop\build\generate_SDK\lib 与 D:\D\FastDeploy-develop\build\generate_SDK\third_libs\install\opencv\build\x64\vc15\lib。
4. 添加库文件：只需要 fastdeploy.lib 和 opencv_world3416.lib
5. 此时就可以点击`生成解决方案`就可以编程出exe文件了，文件在D:\D\vs_project\HRNet_segment_trt\x64\Release\HRNet_segment_trt.exe
6. 执行可执行文件，获得推理结果。 首先需要拷贝所有的dll到exe所在的目录下。同时，也需要把深度学习模型文件和测试图片下载解压缩后，拷贝到exe所在的目录。在上传的文件中我直接给出了模型和测试图像，此外，配置dll文件全部放在bin文件中的。
7. 运行以及编译。将上述的dll文件以及模型与测试图像放在exe所在目录后，cmd跳转至exe目录，输入`HRNet_segment_trt FCN_HRNet_W18_cityscapes_without_argmax_infer city_car_demo.png 2`回车即可实现基于GPU与TensorRT的加速推理。输出结果可见当前目录下的vis_result。实验结果如下：

![city_car_demo](https://user-images.githubusercontent.com/114713988/195964794-dabaddf3-b983-445e-8708-14e32b8729b2.png)
![vis_result](https://user-images.githubusercontent.com/114713988/195984140-4f371194-a1ca-479e-92c0-53e5cd4efbf4.jpg)



