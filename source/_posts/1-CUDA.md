---
title: CUDA学习笔记（一）之CUDA安装及GPU硬件介绍
date: 2018-06-30 14:01:19
tags: CUDA
---

# 一、CUDA安装与环境配置
<!--more-->
## Linux环境下的CUDA的安装

因为之前的博客中有涉及到这一部分所以这里就不在详述。[ubuntu16.04 Server install CUDA](https://huanghailiang.github.io/2017/07/22/ubuntu16-04-Server-install-CUDA/ "")

不过我们在linux下使用GPU编程的时候有时候会遇到nvidia驱动挂掉的情况，这个时候我们需要修复它，在之前的博客中我们使用dkms来进行管理的方法。[nvidia驱动挂掉](https://huanghailiang.github.io/2018/01/16/nvidia%E9%A9%B1%E5%8A%A8%E6%8C%82%E6%8E%89/ "")

在linux下面我们经常会使用nvidia-smi指令对GPU的使用情况进行监测，但是有时候GPU监测时会不准确或者有时候程序退出了但是现存还被占用着，这种情况之前的博客也有解决方法。[GPU监测时问题解决](https://huanghailiang.github.io/2017/10/19/GPU%E7%9B%91%E6%B5%8B%E6%97%B6%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3/ "")

## Windows环境下的CUDA安装

Windows环境下的安装，我们需要先安装Visual Studio编译器用于后面的编程使用，然后去nvidia的官网查找适用与该版本的Visual Studio的CUDA版本，剩下就比较简单了，直接开始安装，安装软件会自动帮你配置好环境变量等，安装好就可以直接使用了。可以去安装的VS中直接建立CUDA的工程进行编程使用了。

# 二、GPU硬件

掌握部门的硬件知识，可以有助于我们更好的编写CUDA程序，可以更好的优化CUDA程序的性能。

首先我们需要先知道一些基本的一些专业名词：

英文|中文
---|---|
Stream Processor（SP）|流处理器
Stream MultiProcessor（SM）|流处理器组
Thread|线程
Block|线程块
Grid|线程网格
Warp|线程束
shared memory|共享内存
kernel function	|核函数


SP（Stream Processor）和SM（Stream MultiProcessor）是硬件层面的，Thread、Block、grid和warp是软件层面上的。

> ***SP（streaming processor）：***最基本的处理单元，也称为CUDA core。最后具体的指令和任务都是在SP上处理的。GPU进行并行计算，也就是很多个SP同时做处理。

> ***SM（Stream MultiProcessor）：***多个SP加上其他的一些资源组成一个SM。也叫GPU大核，其他资源如：warp scheduler，register，shared memory等。SM可以看做GPU的心脏（对比CPU核心），register和shared memory是SM的稀缺资源。CUDA将这些资源分配给所有驻留在SM中的threads。因此，这些有限的资源就使每个SM中active warps有非常严格的限制，也就限制了并行能力。

> ***thread：***一个CUDA的并行程序会被以许多个threads来执行。

> ***block：***数个threads会被群组成一个block，同一个block中的threads可以同步，也可以通过shared memory通信。

> ***grid：***多个blocks则会再构成grid。

> ***warp：***GPU执行程序时的调度单位，目前cuda的warp的大小为32，同在一个warp的线程，以不同数据资源执行相同的指令,这就是所谓 SIMT。

## 注
对于硬件结构的图网上有很多，还有CUDA的指导手册中也有很多，这里为了简单就先不列出来了，如果未来有时间有兴趣，将会进一步进行优化。也可以去参看后面第四部分中的参考中的网址进行参考。

# 三、使用代码获取GPU的参数信息
因为我们对于不用的GPU型号，其构架等信息都不一样，如果我们要想优化一个更好的CUDA程序，我们就需要更好的了解我们的硬件信息，然后根据其进行一些优化处理。对于GPU硬件参数的获取，当然我们可以根据GPU提供的手册进行查找，我们也可以使用程序进行查找。

我们可以使用cudaGetDeviceCount()函数来查看当前计算机中拥有多少个GPU设备，然后可以对每个设备进行迭代，并查询各个设备的相关信息。CUDA运行的时将返回一个cudaDeviceProp类型结构，其中包含了设备的相关属性。

**在cudaDeviceProp结构中包含以下信息(本人的CUDA版本是8.0)：**

```c
struct __device_builtin__ cudaDeviceProp
{
    char   name[256];                  /**< ASCII string identifying device ASCII字符串识别设备*/
    size_t totalGlobalMem;             /**< Global memory available on device in bytes 设备上可用的全局内存(以字节为单位)*/
    size_t sharedMemPerBlock;          /**< Shared memory available per block in bytes 以字节为单位的每个块可用的共享内存*/
    int    regsPerBlock;               /**< 32-bit registers available per block 每个块可用的32位寄存器*/
    int    warpSize;                   /**< Warp size in threads 线程束的大小*/
    size_t memPitch;                   /**< Maximum pitch in bytes allowed by memory copies 内存拷贝允许的最大字节间距*/
    int    maxThreadsPerBlock;         /**< Maximum number of threads per block 每个块的最大线程数*/
    int    maxThreadsDim[3];           /**< Maximum size of each dimension of a block 块的每个维度的最大大小*/
    int    maxGridSize[3];             /**< Maximum size of each dimension of a grid 网格的每个维度的最大大小*/
    int    clockRate;                  /**< Clock frequency in kilohertz 时钟频率（赫兹）*/
    size_t totalConstMem;              /**< Constant memory available on device in bytes 设备上可用的常量内存(以字节为单位)*/
    int    major;                      /**< Major compute capability 主要的计算能力*/
    int    minor;                      /**< Minor compute capability 次要的计算能力*/
    size_t textureAlignment;           /**< Alignment requirement for textures 纹理对齐要求*/
    size_t texturePitchAlignment;      /**< Pitch alignment requirement for texture references bound to pitched memory 对纹理引用的调距要求绑定到倾斜内存。*/
    int    deviceOverlap;              /**< Device can concurrently copy memory and execute a kernel. Deprecated. Use instead asyncEngineCount. 设备可以同时复制内存并执行内核。弃用。使用相反asyncEngineCount。*/
    int    multiProcessorCount;        /**< Number of multiprocessors on device 设备上多处理器的数量*/
    int    kernelExecTimeoutEnabled;   /**< Specified whether there is a run time limit on kernels 指定内核是否有运行时限制。*/
    int    integrated;                 /**< Device is integrated as opposed to discrete 设备是集成的，而不是离散的*/
    int    canMapHostMemory;           /**< Device can map host memory with cudaHostAlloc/cudaHostGetDevicePointer 设备可以使用cudaHostAlloc/cudaHostGetDevicePointer映射主机内存*/
    int    computeMode;                /**< Compute mode (See ::cudaComputeMode) 计算模式*/
    int    maxTexture1D;               /**< Maximum 1D texture size 最大1 d纹理大小*/
    int    maxTexture1DMipmap;         /**< Maximum 1D mipmapped texture size 最大1D的纹理错误处理*/
    int    maxTexture1DLinear;         /**< Maximum size for 1D textures bound to linear memory 绑定到线性内存的一维纹理的最大大小*/
    int    maxTexture2D[2];            /**< Maximum 2D texture dimensions 最大的2 d纹理维度*/
    int    maxTexture2DMipmap[2];      /**< Maximum 2D mipmapped texture dimensions 最大二维的纹理尺寸*/
    int    maxTexture2DLinear[3];      /**< Maximum dimensions (width, height, pitch) for 2D textures bound to pitched memory 最大尺寸(宽度，高度，pitch)的二维纹理绑定到pitch内存*/
    int    maxTexture2DGather[2];      /**< Maximum 2D texture dimensions if texture gather operations have to be performed 如果必须执行纹理收集操作，则最大的二维纹理尺寸*/
    int    maxTexture3D[3];            /**< Maximum 3D texture dimensions 最大的3 d纹理维度*/
    int    maxTexture3DAlt[3];         /**< Maximum alternate 3D texture dimensions 最大的替代3D纹理尺寸。*/
    int    maxTextureCubemap;          /**< Maximum Cubemap texture dimensions 最大Cubemap纹理维度*/
    int    maxTexture1DLayered[2];     /**< Maximum 1D layered texture dimensions 最大1D层状纹理尺寸*/
    int    maxTexture2DLayered[3];     /**< Maximum 2D layered texture dimensions 最大二维分层纹理尺寸*/
    int    maxTextureCubemapLayered[2];/**< Maximum Cubemap layered texture dimensions 最大立方体地图分层纹理尺寸*/
    int    maxSurface1D;               /**< Maximum 1D surface size 最大1 d表面大小*/
    int    maxSurface2D[2];            /**< Maximum 2D surface dimensions 最大的2 d表面尺寸*/
    int    maxSurface3D[3];            /**< Maximum 3D surface dimensions 最大的3 d表面尺寸*/
    int    maxSurface1DLayered[2];     /**< Maximum 1D layered surface dimensions 最大一维分层表面尺寸。*/
    int    maxSurface2DLayered[3];     /**< Maximum 2D layered surface dimensions 最大二维分层表面尺寸。*/
    int    maxSurfaceCubemap;          /**< Maximum Cubemap surface dimensions 最大Cubemap表面尺寸*/
    int    maxSurfaceCubemapLayered[2];/**< Maximum Cubemap layered surface dimensions 最大体积层状表面尺寸*/
    size_t surfaceAlignment;           /**< Alignment requirements for surfaces 表面对齐要求*/
    int    concurrentKernels;          /**< Device can possibly execute multiple kernels concurrently 设备可以同时执行多个内核*/
    int    ECCEnabled;                 /**< Device has ECC support enabled 设备已启用ECC支持*/
    int    pciBusID;                   /**< PCI bus ID of the device 设备的PCI总线ID*/
    int    pciDeviceID;                /**< PCI device ID of the device 设备的PCI设备ID。*/
    int    pciDomainID;                /**< PCI domain ID of the device 设备的PCI域ID*/
    int    tccDriver;                  /**< 1 if device is a Tesla device using TCC driver, 0 otherwise 如果设备是使用TCC驱动的特斯拉设备为1，则为0*/
    int    asyncEngineCount;           /**< Number of asynchronous engines 异步的引擎*/
    int    unifiedAddressing;          /**< Device shares a unified address space with the host 设备与主机共享统一的地址空间*/
    int    memoryClockRate;            /**< Peak memory clock frequency in kilohertz 峰值记忆时钟频率，单位为千赫兹*/
    int    memoryBusWidth;             /**< Global memory bus width in bits 全局内存总线宽度的位。*/
    int    l2CacheSize;                /**< Size of L2 cache in bytes 以字节为单位的L2缓存大小*/
    int    maxThreadsPerMultiProcessor;/**< Maximum resident threads per multiprocessor 每个多处理器的最大驻留线程。*/
    int    streamPrioritiesSupported;  /**< Device supports stream priorities 设备支持流的优先级*/
    int    globalL1CacheSupported;     /**< Device supports caching globals in L1 设备支持在L1中缓存全局变量*/
    int    localL1CacheSupported;      /**< Device supports caching locals in L1 设备支持L1中的缓存局部变量*/
    size_t sharedMemPerMultiprocessor; /**< Shared memory available per multiprocessor in bytes 每个多处理器可用的共享内存(以字节为单位)*/
    int    regsPerMultiprocessor;      /**< 32-bit registers available per multiprocessor 每个多处理器可使用32位寄存器*/
    int    managedMemory;              /**< Device supports allocating managed memory on this system 设备支持在这个系统上分配托管内存*/
    int    isMultiGpuBoard;            /**< Device is on a multi-GPU board 设备在一个多gpu板上。*/
    int    multiGpuBoardGroupID;       /**< Unique identifier for a group of devices on the same multi-GPU board 一组设备的唯一标识符，在同一个多gpu板上。*/
    int    hostNativeAtomicSupported;  /**< Link between the device and the host supports native atomic operations 设备和主机之间的链接支持本地原子操作*/
    int    singleToDoublePrecisionPerfRatio; /**< Ratio of single precision performance (in floating-point operations per second) to double precision performance 单精度性能(每秒浮点运算)与双精度性能的比值*/
    int    pageableMemoryAccess;       /**< Device supports coherently accessing pageable memory without calling cudaHostRegister on it 设备支持连贯地访问可分页内存，而不调用cudaHostRegister*/
    int    concurrentManagedAccess;    /**< Device can coherently access managed memory concurrently with the CPU 设备可以与CPU同步访问托管内存*/
};

```


**使用CUDA程序读取GPU参数：**

```c
//CUDA头文件
#include "cuda_runtime.h"
#include "device_launch_parameters.h"
//C语言的头文件
#include "stdio.h"

int main()
{
    //定义cudaDeviceProp结构用于读取参数
	cudaDeviceProp prop;

	int count;
	//读取设备的数量
	cudaGetDeviceCount(&count);
	printf("GPU_count = %d\n\n",count);
	for(int i=0;i<count;i++)
	{
	    //获取相应设备号的设备信息
		cudaGetDeviceProperties(&prop,i);
		printf("   --- General Information for device %d ---\n",i);
		//显卡名称
		printf("Name:\t%s\n",prop.name);
		//计算能力		
		printf("Compute capability:\t%d.%d\n",prop.major,prop.minor);
		//时钟频率
		printf("Clock rate:\t%d\n",prop.clockRate);
		//设备复制重叠
		printf("Device copy overlap:\t");
		if(prop.deviceOverlap)
		{
			printf("Enable\n");
		}
		else
		{
			printf("Disable\n");
		}
		//内核执行超时
		printf("Kernel execition timeout:\t");
		if(prop.kernelExecTimeoutEnabled)
		{
			printf("Enable\n");
		}
		else
		{
			printf("Disable\n");
		}
		printf("   --- Memory Information for device %d ---\n",i);
		//设备上全局内存的总量，单位为字节
		printf("Total global Mem:\t%ld\n",prop.totalGlobalMem);
		//设备上的常量内存的总量，单位为字节
		printf("Total constant Mem:\t%ld\n",prop.totalConstMem);
		//内存拷贝允许的最大字节间距
		printf("Max men pitch:\t%ld\n",prop.memPitch);
		//纹理对齐要求
		printf("Texture Alignment:\t%ld\n",prop.textureAlignment);
		printf("   ---MP Information for device %d ---\n",i);
		//设备上多处理器的数量
		printf("Multiprocessor count:\t%d\n",prop.multiProcessorCount);
		//以字节为单位的每个块可用的共享内存
		printf("Shared mem per mp:\t%ld\n",prop.sharedMemPerBlock);
		//每个块可用的32位寄存器
		printf("Registers per mp:\t%d\n",prop.regsPerBlock);
		//每个warp包含的线程数量
		printf("Threads in warp:\t%d\n",prop.warpSize);
		//每个块包含的最大线程数量
		printf("Max threads per block:\t%d\n",prop.maxThreadsPerBlock);
		//块的每个维度的最大大小
		printf("Max threads dimensions:\t(%d, %d, %d)\n",prop.maxThreadsDim[0],prop.maxThreadsDim[1],prop.maxThreadsDim[3]);
		//网格的每个维度的最大大小
		printf("Max grid dimensions:\t(%d, %d, %d)\n",prop.maxGridSize[0],prop.maxGridSize[1],prop.maxGridSize[3]);
		printf("\n\n");
	}
}

```

# 四、参考

《GPU高性能编程CUDA实战》

https://blog.csdn.net/zhangfuliang123/article/details/78115163

https://blog.csdn.net/q583956932/article/details/78700942



























