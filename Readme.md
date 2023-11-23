<!-- Everylink I used to install =))
https://github.com/Tencent/ncnn/issues/3936
https://medium.com/mlearning-ai/deployment-of-pytorch-model-using-ncnn-bceff5d846b0
https://github.com/freshtechyy/NCNN-Deployment-image-classification-example/tree/main
https://docs.ultralytics.com/modes/export/#key-features-of-export-mode
https://convertmodel.com/#outputFormat=ncnn
https://github.com/daquexian/onnx-simplifier
https://github.com/nihui/ncnn-assets/tree/master/models
https://github.com/Tencent/ncnn/discussions/4541
https://github.com/triple-Mu/ncnn-examples/blob/main/cpp/yolov5/CMakeLists.txt
https://docs.opencv.org/4.x/d7/d9f/tutorial_linux_install.html
https://github.com/Tencent/ncnn/issues/3578
https://github.com/atanmarko/ncnn-with-cuda
 -->
### Introduction
- Learning `ncnn` and `cpp` with `yolo`. My device is`cpu`, so feel easy to run
### Install NCNN

- To install `ncnn`, please following [build tutorial of ncnn](https://github.com/Tencent/ncnn/wiki/how-to-build) to build on your own device.
<!-- https://waittim.github.io/2020/11/10/build-ncnn/ -->

- If you want to install ncnn with cpu (ubuntu) (after installing all required dependencies in `ncnn` repo):

```bash
git clone https://github.com/Tencent/ncnn.git
cd ncnn
mkdir -p build
cd build
cmake -DCMAKE_BUILD_TYPE=Release -DNCNN_VULKAN=OFF -DNCNN_CUDA=OFF -DNCNN_RUNTIME_CPU=ON  -DNCNN_SYSTEM_GLSLANG=ON -DNCNN_SHARED_LIB=ON ..
make
make install
```

- Install eigen-3.3.9 [[google]](https://drive.google.com/file/d/1rqO74CYCNrmRAg8Rra0JP3yZtJ-rfket/view?usp=sharing), [[baidu(code:ueq4)]](https://pan.baidu.com/s/15kEfCxpy-T7tz60msxxExg).

```shell
unzip eigen-3.3.9.zip
cd eigen-3.3.9
mkdir build
cd build
cmake ..
sudo make install
```

- Install Opencv if needed ([quick installopencv](https://docs.opencv.org/4.x/d7/d9f/tutorial_linux_install.html))

```bash
sudo apt update && sudo apt install -y cmake g++ wget unzip
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.x.zip
unzip opencv.zip
mkdir -p build && cd build
cmake  ../opencv-4.x
cmake --build .
```

### Build yolov5

- To build yolov5s:
```bash
mkdir build
cd build
cmake ..
make
```

### Inferncen

- To infer:
```bash
cd build
./yolov5 ../test.jpg
```



