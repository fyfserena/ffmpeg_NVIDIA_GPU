# ffmpeg_NVIDIA_GPU

I am trying to find a faster way to encode raw data to mp4 file. ffmpeg with cpu takes too long but I have a NVIDIA Ampere GPU so I can use it to process data.
Check their official sdk info: https://developer.nvidia.com/nvidia-video-codec-sdk

However the NVIDIA official tutorial for using ffmpeg with NIVIDIA codec sdk is a bit outdated (the tutorial Using_FFmpeg_with_NVIDIA_GPU_Hardware_Acceleration_v01.4.pdf).

The problem is that it asks me to use a wrong msvc (Microsorft C compiler) by "export PATH="/c/Program Files (x86)/Microsoft Visual Studio 
12.0/VC/BIN/amd64/":$PATH". 

When I run step 10: ./configure --enable-nonfree –disable-shared --enable-cuda-sdk --
enable-libnpp –-toolchain=msvc --extra-cflags=-I../nv_sdk --extra-ldflags=-libpath:../nv_sdk

I got error like 

cl is unable to create an executable file.
C compiler test failed.


But after I view the configure log file in the /c/FFmpegGPU/ffmpeg/ffbuilds I find there is some errors about the linker I am using. So I changed the MSVC to the actual one my Visual Studio is using which is in the Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.29.30037/bin/Hostx64/x64.


This is the workflow I follow instead after I finish step 7 for the * 2.2.2.2 Compiling for Windows *:

cd /c/FFmpegGPU/ffmpeg

export PATH="/c/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.29.30037/bin/Hostx64/x64/":$PATH
export PATH="/c/Program Files/NVIDIA GPU Computing Toolkit/CUDA/v11.4/bin/":$PATH

./configure --enable-nonfree --enable-cuda-nvcc --enable-libnpp --toolchain=msvc --extra-cflags=-I../nv_sdk --extra-ldflags=-libpath:../nv_sdk

make -j 8

export PATH="/c/FFmpegGPU/ffmpeg/":$PATH

ffmpeg -f image2 -c:v rawvideo -framerate 120 -video_size 1936x1464 -pixel_format bayer_rggb8 -i ../input/flir_01_%07d.raw ../output/output_gpu.mp4
