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
https://github.com/FeiGeChuanShu/ncnn-android-yolov8/issues/8#issuecomment-1610283859
https://github.com/Digital2Slave/ncnn-android-yolov8-seg/wiki/Convert-yolov8-model-to-ncnn-model
https://zhuanlan.zhihu.com/p/606536868
 -->
### Introduction
- Learning `ncnn` and `cpp` with `yolo`. My device is`cpu`, so feel easy to run
- Folder Tree:
```
|__ cpp_root
|       |__ ncnn
|       |__ eigen-3.3.9
|       |__ opencv_setup
|       |__ yolo_ncnn_cpp  --> this repository
|       |__ export_to_ncnn
```

### Install NCNN

To install `ncnn`, please follow [build tutorial of ncnn](https://github.com/Tencent/ncnn/wiki/how-to-build) to build on your own device or follow me below 
<!-- https://waittim.github.io/2020/11/10/build-ncnn/ -->

- Install dependencies:

```bash
sudo apt update
sudo apt install build-essential git cmake libprotobuf-dev protobuf-compiler libvulkan-dev vulkan-utils libopencv-dev
sudo apt-get install libprotobuf-dev protobuf-compiler
sudo apt-get install libopencv-dev
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

- Install Opencv if needed ([quick install opencv](https://docs.opencv.org/4.x/d7/d9f/tutorial_linux_install.html))

```bash
mkdir opencv_setup && cd opencv_setup
sudo apt update && sudo apt install -y cmake g++ wget unzip
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.x.zip
unzip opencv.zip
mkdir -p build && cd build
cmake  ../opencv-4.x
cmake --build .
```

<!-- - To install `ncnn`, please following [build tutorial of ncnn](https://github.com/Tencent/ncnn/wiki/how-to-build) to build on your own device. -->
<!-- https://waittim.github.io/2020/11/10/build-ncnn/ -->


- If you want to install ncnn with cpu (ubuntu) (after installing all required dependencies in `ncnn` repo):

```bash
git clone https://github.com/Tencent/ncnn.git
cd ncnn
git submodule update --init
mkdir -p build
cd build
# cmake -DCMAKE_BUILD_TYPE=Release -DNCNN_VULKAN=OFF -DNCNN_CUDA=OFF -DNCNN_RUNTIME_CPU=ON  -DNCNN_SYSTEM_GLSLANG=ON -DNCNN_SHARED_LIB=ON ..
cmake -DCMAKE_BUILD_TYPE=Release -DNCNN_VULKAN=OFF -DNCNN_RUNTIME_CPU=ON -DNCNN_SYSTEM_GLSLANG=ON -DNCNN_BUILD_EXAMPLES=OFF -DNCNN_SHARED_LIB=ON ..
make
make install
```

### Export to ncnn
Follow this [tutorial](https://zhuanlan.zhihu.com/p/606536868) or follow me bellow:

- Clone [Ultralytics](https://github.com/ultralytics/ultralytics):
```bash
cd export_to_ncnn
git clone https://github.com/ultralytics/ultralytics.git  # clone
cd ultralytics
git checkout d99e04daa1290c226a3fae825401361b17ce164c # switch to commit id
pip install -r requirements.txt  # install
pip install -e . # install ultralytics as package
```

- Update code in `ultralytics/ultralytics/nn/modules.py` at line 398-411:
```python
# def forward(self, x):
    #     shape = x[0].shape  # BCHW
    #     for i in range(self.nl):
    #         x[i] = torch.cat((self.cv2[i](x[i]), self.cv3[i](x[i])), 1)
    #     if self.training:
    #         return x
    #     elif self.dynamic or self.shape != shape:
    #         self.anchors, self.strides = (x.transpose(0, 1) for x in make_anchors(x, self.stride, 0.5))
    #         self.shape = shape
    # 
    #     box, cls = torch.cat([xi.view(shape[0], self.no, -1) for xi in x], 2).split((self.reg_max * 4, self.nc), 1)
    #     dbox = dist2bbox(self.dfl(box), self.anchors.unsqueeze(0), xywh=True, dim=1) * self.strides
    #     y = torch.cat((dbox, cls.sigmoid()), 1)
    #     return y if self.export else (y, x)

def forward(self, x):
    z = []  # inference output
    for i in range(self.nl):
        boxes = self.cv2[i](x[i]).permute(0, 2, 3, 1)
        scores = self.cv3[i](x[i]).sigmoid().permute(0, 2, 3, 1)
        feat = torch.cat((boxes, scores), -1)
        z.append(feat)
    return tuple(z)
```

- Export model to torchscript:
```bash
cd ..
yolo export model=yolov8s.pt format=torchscript
```

- Download pnnx to export from onnx to ncnn:
```bash
wget https://github.com/pnnx/pnnx/releases/download/20230217/pnnx-20230217-ubuntu.zip
unzip pnnx-20230217-ubuntu.zip
```

```bash
./pnnx-20230217-ubuntu/pnnx yolov8s.torchscript inputshape=[1,3,640,640] inputshape2=[1,3,320,320]
```
### Build

- To build yolov5:
```bash
cd yolov5
mkdir build
cd build
cmake ..
make
```

- To build yolov8:
```bash
cd yolov8
mkdir build
cd build
cmake ..
make
```

### Run

- To infer yolov5:
```bash
cd build
./yolov5 ../../test.jpg
```

- To infer yolov8:
```bash
cd build
./yolov8 ../../test.jpg
```

### TODO
- [ ] build ncnn with `-DNCNN_CUDA=ON` to test working with GPU
- [x] step by step to export .pt to ncnn
- [ ] check this line `yolov5.opt.use_vulkan_compute = true;`