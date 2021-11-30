#How to compile OpenCV with NVIDIA GPU in ubuntu 20.04 Anaconda3

My recent work needs OpenCV DNN Module. But when you used `pip install opencv-python` to install the openCV, it will limited CPU-only inference use, FPS is very low. 

Since OpenCV 4.2, which can acclerated by CUDA and cuDNN. But it needs to compile the OpenCV library with DNN GPU support from source code. 

So, here I recorded some notes during installation. Here I skipped testing part. you can easily find sample codes on the internet.

**Noticed:** you will fail, if you want to install OpenCV through virtual environment like Anaconda following web tutorial (you can not find the CV2 directory)

The final solution is install it in system level, then link it to the virtual environment.

- **Environment**
nvidia-driver-495
cuda 11.5
python 3.8
ubuntu 20.04
Anaconda3

####Install Nvidia driver
  Easiest way: your can got the driver from 'Additional Drivers' app

####Install Cuda
```
$ wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pinsudo 
$ mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
$ wget https://developer.download.nvidia.com/compute/cuda/11.5.1/local_installers/cuda-repo-ubuntu2004-11-5-local_11.5.1-495.29.05-1_amd64.deb
$ sudo dpkg -i cuda-repo-ubuntu2004-11-5-local_11.5.1-495.29.05-1_amd64.deb
$ sudo apt-key add /var/cuda-repo-ubuntu2004-11-5-local/7fa2af80.pub
$ sudo apt-get update
$ sudo apt-get -y install cuda
$ export PATH=/usr/local/cuda-11.5/bin${PATH:+:${PATH}}
$ export LD_LIBRARY_PATH=/usr/local/cuda-11.5/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

####Install Cudnn
  - go to https://developer.nvidia.com/rdp/cudnn-download, you need sign up an account

  - download [Local Installer for Ubuntu20.04 x86_64 (Deb)](https://developer.nvidia.com/compute/cudnn/secure/8.3.1/local_installers/11.5/cudnn-local-repo-ubuntu2004-8.3.1.22_1.0-1_amd64.deb)
  - `$ cd {path to the package}`
  - `$ sudo dpkg -i cudnn-local-repo-ubuntu2004-8.3.1.22_1.0-1_amd64.deb`
  
You can use `nvidia-smi` to check status. If sucess, it will show as below:

  ```
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 495.29.05    Driver Version: 495.29.05    CUDA Version: 11.5     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA GeForce ...  On   | 00000000:01:00.0  On |                  N/A |
| N/A   40C    P8    14W /  N/A |    744MiB /  5946MiB |     11%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A      1222      G   /usr/lib/xorg/Xorg                 77MiB |
|    0   N/A  N/A      1812      G   /usr/lib/xorg/Xorg                402MiB |
|    0   N/A  N/A      2016      G   /usr/bin/gnome-shell               80MiB |
|    0   N/A  N/A      5851      G   ...conda3/envs/cv/bin/python        2MiB |
|    0   N/A  N/A      6044      G   /usr/lib/firefox/firefox          122MiB |
|    0   N/A  N/A      6432      G   ...AAAAAAAAA= --shared-files       39MiB |
|    0   N/A  N/A      6987      G   /usr/lib/firefox/firefox            4MiB |
|    0   N/A  N/A      8727      G   /usr/lib/firefox/firefox            2MiB |
+-----------------------------------------------------------------------------+

  ```

####Install OpenCV
```
$ cd Downloads/
$ mkdir -p opencv_build
$ cd opencv_build
$ git clone https://github.com/opencv/opencv.git
$ git clone https://github.com/opencv/opencv_contrib.git
$ cd ~/opencv_build/opencv
$ mkdir -p build && cd build
$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
$     -D CMAKE_INSTALL_PREFIX=/usr/local \
$     -D INSTALL_C_EXAMPLES=ON \
$     -D INSTALL_PYTHON_EXAMPLES=ON \
$     -D OPENCV_GENERATE_PKGCONFIG=ON \
$     -D OPENCV_EXTRA_MODULES_PATH=../../opencv_build/opencv_contrib/modules \
$     -D ENABLE_FAST_MATH=1 \
$     -D CUDA_FAST_MATH=1 \
$     -D WITH_CUBLAS=1 \
$     -D WITH_CUDA=ON \
$     -D BUILD_opencv_cudacodec=OFF \
$     -D WITH_CUDNN=ON \
$     -D OPENCV_DNN_CUDA=ON \
$     -D CUDA_ARCH_BIN=8.6 \ 
$     -D BUILD_EXAMPLES=ON ..
$ make -j16
$ sudo make install
$ sudo ldconfig
```
- `CUDA_ARCH_BIN=8.6` this value is according to your hardware, you can go to https://developer.nvidia.com/cuda-gpus, checking your NVIDIA GPU Compute Capability
 the cv2 directory is `/usr/local/lib/python3.8/dist-packages/cv2`

####Link Anaconda
```
conda create -n cv python=3.8
conda activate cv
conda install numpy
cd ~/anaconda3/envs/cv/lib/python3.8/site-packages/
ln -s /usr/local/lib/python3.8/dist-packages/cv2/python-3.8/cv2.cpython-38-x86_64-linux-gnu.so cv2.so
```
**Now you can implement the code using OpenCV library with DNN GPU Support under Anaconda.** 

####Reference
  - https://learnopencv.com/opencv-dnn-with-gpu-support/
  - https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html
  - https://medium.com/geekculture/installing-cudnn-and-cuda-toolkit-on-ubuntu-20-04-for-machine-learning-tasks-f41985fcf9b2
  - https://gist.github.com/raulqf/f42c718a658cddc16f9df07ecc627be7