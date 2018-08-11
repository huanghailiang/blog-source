---
title: C语言编写程序计时
date: 2017-08-22 20:45:04
tags: 常用的C语言小程序代码
---
# C语言编写程序计时
在我们编程的时候，有时候需要我们去监测程序运行的时间问题，尤其是在做加速或者做优化的时候，一个程序运行的时间也许就是我们判断程序好坏的标准，所以我们有时候要进行计时。（因为用到了，所以做个记录方便下次查看）


<!--more-->
## 在windows上进行计时

```c
#include "stdio.h"
#include "time.h"

clock_t start_time, finish_time;/*计时*/
double cord_time;/*计时*/

int main(int argc, char* argv[])
{
	start_time = clock();/*****************************计时开始***********************************/
    /*******************/
    /*这里编写程序*/
    /*******************/
    finish_time = clock();/***************************计时结束***********************************/
     //显示总的用时
	cord_time = (double)(finish_time - start_time) ;
	printf("time=%f ms\n", cord_time);
	return 0;
}
```

## 在linux上进行计时

```c
#include "stdio.h"
#include <sys/time.h>

int time_substract(struct timeval *result, struct timeval *begin,struct timeval *end)

{
    if(begin->tv_sec > end->tv_sec)    return -1;
    if((begin->tv_sec == end->tv_sec) && (begin->tv_usec > end->tv_usec)) return -2;
    result->tv_sec = (end->tv_sec - begin->tv_sec);
    result->tv_usec = (end->tv_usec - begin->tv_usec);
    if(result->tv_usec < 0)
    {
        result->tv_sec--;
        result->tv_usec += 1000000;
    }
    return 0;
}

int main(int argc, char* argv[])
{
    struct timeval start,stop,diff;
    memset(&start,0,sizeof(struct timeval));
    memset(&stop,0,sizeof(struct timeval));
    memset(&diff,0,sizeof(struct timeval));
    gettimeofday(&start,0);
    /*******************/
    /*这里编写程序*/
    /*******************/
    gettimeofday(&stop,0);
    time_substract(&diff,&start,&stop);
    printf("Total time : %d s,%d us\n",(int)diff.tv_sec,(int)diff.tv_usec);
	return 0;
}
    
```