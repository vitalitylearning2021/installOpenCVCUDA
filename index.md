## Building OpenCV for Windows with CUDA

<p align="center">
  <img src="appendixA.png" width="400">
</p>

1.  Install CUDA from `https://developer.nvidia.com/cuda-downloads`;

2.  Download cuDNN from `https://developer.nvidia.com/cudnn` (this requires registration);

3.  Install git for windows; for example, use
    `https://gitforwindows.org/`;

4.  From the command prompt, type `git clone
    https://github.com/opencv/opencv.git`; this will create an opencv
    directory under the current directory (`C:\Users\bookWriter`, for
    example);

5.  From the command prompt, type `git clone
    https://github.com/opencv/opencv_contrib`; this will create an
    `opencv_contrib` directory under the current directory;

6.  Add `C:\Program Files (x86)\Windows Kits\8.1\bin\x86` to the `Path`
    environment variables, if not yet included;

7.  Install CMake from `cmake.org/download`; use the `.msi` file;

8.  From the command line, type
    
    ``` powershell
    "C:\Program Files\CMake\bin\cmake.exe"
        -B"C:\Users\bookWriter\opencv\build" -
        H"C:\Users\bookWriter\opencv" -
        DOPENCV_EXTRA_MODULES_PATH="C:\Users\bookWriter\opencv_contrib\
        modules" -G"Visual Studio 14 2015 Win64" -
        DBUILD_opencv_world=ON -DWITH_CUDA=ON -DCUDA_FAST_MATH=ON -
        DWITH_CUBLAS=ON -DINSTALL_TESTS=ON -DINSTALL_C_EXAMPLES=ON -
        DBUILD_EXAMPLES=ON -DCMAKE_BUILD_TYPE=Release -
        DBUILD_opencv_gapi=OFF -DWITH_NVCUVID=OFF -DWITH_OPENGL=ON -
        DOPENCV_ENABLE_NONFREE=ON -
        DCUDA_ARCH_PTX=5.2 
    ```
    
    `C:\Users\bookWriter` is the directory within which `opencv` and
    `opencv_contrib` have been downloaded and created; `OpenCV` is built
    using `Visual Studio 2015` (`-G"Visual Studio 14 2015 Win64"`
    option), but other versions of Visual Studio are possible; the code
    is built for a `5.2` architecture (option `DCUDA_ARCH_PTX=5.2`);
    this operation may take several minutes and will create, among
    others, the `OpenCV.sln` file under
    `C:\Users\bookWriter\opencv\build`;

9.  Double click on the `OpenCV.sln` file;

10. Under `Solution` `'OpenCV' -> CMakeTargets -> INSTALL -> right click
    -> build`;

11. Add `C:\Users\bookWriter\opencv\build\install\x64\vc14\bin` to the
    environment variables.

Please, be aware that OpenCV 4.1.1 requires at least CUDA 10.1.

## First OpenCV CUDA example

A first OpenCV CUDA example is reported in Listing [1](#firstExampleOpenCV).

``` c++
#include <opencv2\opencv.hpp>
#include <opencv2\cudaimgproc.hpp>

#include <opencv2\opencv.hpp>
#include <opencv2\cudaimgproc.hpp>
/********/
/* MAIN */
/********/
int main(){
    std::cout << cv::getBuildInformation() << std::endl;
    
    auto idx = cv::cuda::getCudaEnabledDeviceCount();
    
    if (idx > 0) cv::cuda::printCudaDeviceInfo(0);
    else printf("OpenCV CUDA error!");

    return 0;}
```
<p align="center" id="firstExampleOpenCV" >
     <em>Listing 1. First OpenCV CUDA example.</em>
</p>

The code in this example checks whether the OpenCV CUDA installation is in order.  
To compile such an example under Visual Studio 2015, do not forget to:

1.  add `Project -> Properties -> Configuration Properties -> VC++
    Directories -> Include Directories ->
    C:\Users\bookWriter\opencv\build\install\include`;

2.  add `Project -> Properties -> Configuration Properties -> Linker -> General -> C:\Users\bookWriter\opencv\build\install\x64\vc14\lib`;

3.  add `Project -> Properties -> Configuration Properties -> Linker -> Input -> Additional Dependencies -> opencv_world411d.lib for OpenCV version 4.1.1`;

## Second OpenCV CUDA example

A second OpenCV CUDA example is reported in Listing [2](#secondExampleOpenCV) .  

``` c++
#include <iostream>
#include "opencv2\opencv.hpp"
#include "opencv2\core.hpp"
#include "opencv2\highgui.hpp"
#include "opencv2\cudaarithm.hpp"
/********/
/* MAIN */
/********/
int main(){
    cv::Mat h_src = cv::imread("C:\\Users\\bookWriter\\Packt\\
        OpenCVImageTest\\file.png", cv::IMREAD_GRAYSCALE);
    cv::cuda::GpuMat d_dst, d_src;
    d_src.upload(h_src);
    cv::cuda::threshold(d_src, d_dst, 128.0, 255.0, cv::THRESH_BINARY);
    cv::Mat h_dst(d_dst);
    cv::imshow("Processing result", h_dst);
    cv::waitKey();
    return 0; }
```
<p align="center" id="secondExampleOpenCV" >
     <em>Listing 2. Second OpenCV CUDA example.</em>
</p>

The code loads a `.png` image to CPU memory, transfers it to GPU, applies a threshold, moves the result back to the CPU and shows it.

