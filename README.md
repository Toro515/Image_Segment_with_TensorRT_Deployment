# Real-time video image super-resolution C++ with TensorRT deployment on Nvidia GTX1650Ti
This is an experimental project. Due to the poor clarity of my camera and the fact that I have recently read many papers on deep learning image hyper-segmentation, I wanted to use a deep learning model with TensorRT inference acceleration to process video and recover pixels in real time.
# Introduce the experimental procedure
1. Using openCV, the video captured by the camera is read and saved in video format for preliminary verification of the effectiveness of the image super-resolution algorithm.
2. Choosing a deep learning based image super-resolution algorithm and validating it using the video files obtained in step 1.
3. Using ONNX to convert image super-resolution models to .onnx files for preliminary inference using python.
4. Writing C++ project using VS2017 to implement accelerated inference for TensorRT. Implementing image super-resolution and outputting clear video files.

# Required environments
*I will gradually update the configuration information according to the progress of the project.*  
The known configurations are as follows,
1. Nvidia GTX1650Ti 4GB
2. onnx 1.12.0
3. onnxruntime-gpu 1.12.1
4. pytorch 1.12.1
5. torchvision 0.13.1
6. 
