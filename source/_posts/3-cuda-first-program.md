---
title: CUDA学习笔记（三）之第一个CUDA程序及计时
date: 2018-07-02 15:06:16
tags: CUDA
---


# 第一个CUDA程序

我们将使用矢量求和的简单运算来学习一下CUDA的简单编程。
<!--more-->

## 矢量求和运算

假设我们有两组数据，我们需要将这两组数据中对应的元素两两相加，并将结果保存在第三个数组中。

### 基于**CPU**的矢量求和

CPU中矢量求和的函数：
```c
void add_CPU(int *a, int *b, int *c)
{
	for (int i = 0; i < N; i++)
	{
		c[i] = a[i] + b[i];
	}
}
```

上面是CPU中的一个矢量求和的函数，采用了for循环来依次执行
```
c[0]=a[0]+b[0];
c[1]=a[1]+b[1];
...
c[N-1]=a[N-1]+b[N-1];
```
我们可以发现每次循环的操作，只是内存地址不一样，但是指令都是一样的，所以可以看作他们每次循环都是一个独立的运算，那么我们将其并行起来是不是更好呢？假设我们有两个CPU计算核心，那么我们希望两个核心同时开始进行计算，那么我们的循环次数也将减半，运行时间将会变少。

对于上诉的问题，在CPU编程中我们也有并行编程的库可以来运行，比如Openmp这个并行工具就可以很好的实现这种操作，不过这将限制与我们计算机的CPU核心数目，我们也知道CPU的核心数目目前是非常有限的，所以对于大程序我们也是无能为力的，这个时候GPU的优势就体现出来了，对于上面每个循环互相独立，操作相同的计算，我们可以在GPU中开出足够多的线程来进行操作，这将大大降低计算时间。

### 基于**GPU**的矢量求和

```cuda
__global__ void add(int *a, int *b, int *c)
{
	int tid = blockIdx.x*blockDim.x+threadIdx.x;//计算该索引处的数据
	if (tid < N)
	{
		c[tid] = a[tid] + b[tid];
	}
}
```

上面是GPU中的一个矢量求和函数。

其中__global__是一个函数声明，说明这个函数是一个全局的函数，在设备上执行的。因为GPU是通过开启大量的线程来并行操作的，所以我们需要计算每个线程的编号，及就是上面程序中的tid.然后我们每个线程在根据这个线程好去读取每个线程需要用到的内存，再去完成计算。该程序的tid计算可以在下面完整的代码看出来因为我们可能不只开了一个块所以我们需要将块也考虑进来。


# 完整代码

```cuda
/*
矢量求和运算
a[i]+b[i]=c[i];
*/

//CUDA的头文件
#include "cuda_runtime.h"
#include "device_launch_parameters.h"
//C语言的头文件
#include "stdio.h"
#include "time.h"

//宏定义 N为数组的长度 thread_num表示每个块中的线程数
#define N 60000
#define thread_num  1024

//GPU函数申明
__global__ void add(int *a, int *b, int *c);

//CPU函数申明
void add_CPU(int *a, int *b, int *c);

int main()
{
    //GPU方法计时申明
	float time_CPU, time_GPU;
	cudaEvent_t start_GPU, stop_GPU, start_CPU, stop_CPU;
    //CPU方法计时申明
	float time_cpu, time_gpu;
	clock_t start_cpu, stop_cpu, start_gpu, stop_gpu;


	int a[N], b[N], c[N],c_CPU[N];
	int *dev_a, *dev_b, *dev_c;
	//知道了每个块中所开的线程数量，那么我们就要知道我们需要开启多少个块
	int block_num;
	
    //线程块中的数量需要向上取整，下面两个操作有可以/*********/
	//block_num = (N % thread_num == 0) ? (N / thread_num) : (N / thread_num + 1);
	block_num = (N + thread_num - 1) / thread_num;

	//在GPU上分配内存
	cudaMalloc((void**)&dev_a, N*sizeof(int));
	cudaMalloc((void**)&dev_b, N*sizeof(int));
	cudaMalloc((void**)&dev_c, N*sizeof(int));

	//在CPU上为a和b进行赋值操作
	for (int i = 0; i < N; i++)
	{
		a[i] = -i;
		b[i] = i*i;
	}


	//创建Event
	cudaEventCreate(&start_CPU);
	cudaEventCreate(&stop_CPU);
	//记录当前时间
	cudaEventRecord(start_CPU, 0);
	start_cpu = clock();
	//CPU计算/*******************************************************/
	add_CPU(a, b, c_CPU);
	stop_cpu = clock();
	//记录当前时间
	cudaEventRecord(stop_CPU, 0);
	cudaEventSynchronize(start_CPU);    //等待事件完成。
	cudaEventSynchronize(stop_CPU);    //等待事件完成。记录之前的任务
	cudaEventElapsedTime(&time_CPU, start_CPU, stop_CPU);    //计算时间差
	printf("The time for CPU:\t%f(ms)\n", time_CPU);
	
	cudaEventDestroy(start_CPU);    //消除Event
	cudaEventDestroy(stop_CPU);

	//输出CPU结果
	printf("\nResult from CPU:\n"); 
	for (int i = 0; i < N; i++)
	{
		printf("CPU:\t%d+%d=%d\n",a[i],b[i],c_CPU[i]);
	}


	//GPU计算/*******************************************************/
	//内存拷贝CPU-TO-GPU
	cudaMemcpy(dev_a, a, N*sizeof(int), cudaMemcpyHostToDevice);
	cudaMemcpy(dev_b, b, N*sizeof(int), cudaMemcpyHostToDevice);

	//创建Event
	cudaEventCreate(&start_GPU);
	cudaEventCreate(&stop_GPU);
	//记录当前时间
	cudaEventRecord(start_GPU, 0);

	start_gpu = clock();
	//调用cuda的核函数
	//printf("block_num=%d\tthread_num=%d\n", block_num, thread_num);
	add << <block_num, thread_num >> >(dev_a, dev_b, dev_c);

	stop_gpu = clock();

	//记录当前时间
	cudaEventRecord(stop_GPU, 0);
	cudaEventSynchronize(start_GPU);    //等待事件完成。
	cudaEventSynchronize(stop_GPU);    //等待事件完成。记录之前的任务
	cudaEventElapsedTime(&time_GPU, start_GPU, stop_GPU);    //计算时间差
	printf("\nThe time for GPU:\t%f(ms)\n", time_GPU);

	//将GPU中的结果拷贝出来
	cudaMemcpy(c, dev_c, N*sizeof(int), cudaMemcpyDeviceToHost);	

	//输出GPU结果
	printf("\nResult from GPU:\n");
	for (int i = 0; i < N; i++)
	{
		printf("GPU:\t%d+%d=%d\n",a[i],b[i],c[i]);
	}


	cudaEventDestroy(start_GPU);    //消除Event
	cudaEventDestroy(stop_GPU);

	//释放GPU上的内存
	cudaFree(dev_a);
	cudaFree(dev_b);
	cudaFree(dev_c);


	printf("\nThe time for CPU by event:\t%f(ms)\n", time_CPU);
	printf("The time for GPU by event:\t%f(ms)\n", time_GPU);
	
	time_cpu = (float)(stop_cpu - start_cpu) / CLOCKS_PER_SEC;
	time_gpu = (float)(stop_gpu - start_gpu) / CLOCKS_PER_SEC;
	printf("\nThe time for CPU by host:\t%f(ms)\n", time_cpu);
	printf("The time for GPU by host:\t%f(ms)\n", time_gpu);
	
	return 0;
}

//GPU函数
__global__ void add(int *a, int *b, int *c)
{
	int tid = blockIdx.x*blockDim.x+threadIdx.x;//计算该索引处的数据
	if (tid < N)
	{
		c[tid] = a[tid] + b[tid];
	}
}

//CPU函数
void add_CPU(int *a, int *b, int *c)
{
	for (int i = 0; i < N; i++)
	{
		c[i] = a[i] + b[i];
	}
}
```

## 完整代码的注解
该代码中我使用了两种计时方式使用主机计时和使用时GPU计时。

#### **使用主机计时**

```c
#include “time.h”
...

	float time;
	clock_t start, stop;
	
    ...
    
    start=clock();
    
    ...//需要计时的那一部分
    
    stop=clock();
    time=(float)(stop-start)/CLOCKS_PER_SEC;
    printf("The time by host:\t%f(ms)\n", time);
```
#### **使用GPU计时**
```c
    float time;
    cudaEvent_t start, stop;
    
    ...
    
    //创建Event
    cudaEventCreate(&start);
    cudaEventCreate(&stop);
    //记录当前时间
    cudaEventRecord(start, 0);
    
    ...//需要计时的那一部分
    
    //记录当前时间
    cudaEventRecord(stop, 0);
    cudaEventSynchronize(start);    //等待事件完成。
    cudaEventSynchronize(stop);    //等待事件完成。记录之前的任务
    cudaEventElapsedTime(&time, start, stop);    //计算时间差
    printf("The time by event:\t%f(ms)\n", time);
    cudaEventDestroy(start);    //消除Event
    cudaEventDestroy(stop);
```

# CUDA程序编写流程

通过上面一个简单的小程序，我们应该也可以体会到一个基本的CUDA程序编写的一个流程：

- 1、从CPU拷贝内存到GPU中；
- 2、调用GPU核函数进行并行计算；
- 3、从GPU拷贝计算结果的内存到CPU中。

## 注：

- 在2步调用GPU核函数的时候我们需要，分配一下我们需要多少个线程块参与计算，并且每个线程块中需要有多少个线程。这些跟硬件有一定的关系，同时一个好的线程规划可能会优化你的程序，这部分因为我也是小白，所以暂时也没有什么特别多的经验，按照之前的经验，只觉得每个线程块开的线程数应该是16或者32的倍数，这个应该跟warp中包含32个线程（目前我用的GPU是这样的）有关。目前大部分的GPU已经支持每个线程块有1024个线程，当然以前比较老的GPU有些只支持到512，所以这些我们需要自己注意一下。
