---
title: C语言读取系统当前时间
date: 2017-09-06 15:03:02
tags: 常用的C语言小程序代码
---

# C语言读取系统当前时间

之前编程的时候我们需要来读取当前的时间，或者使用当前的时间对文件进行一个命名等，所以做一个笔记用来备忘。

<!--more-->

## 读取时间代码

**C语言读取系统时间的代码如下：**

```c
#include <stdio.h>  
#include <time.h>  
#include <stdlib.h>
#include <string.h>  
int main()
{
	time_t timep,Tim;
	struct tm *p;
	time(&timep);
	p =  localtime(&timep); //此函数获得的tm结构体的时间，是已经进行过时区转化为本地时间     
	//p = gmtime(&timep); //把日期和时间转换为格林威治(GMT)时间的函数 
	int Year = 1900 + p->tm_year;
	int Month = 1 + p->tm_mon;
	int Day = p->tm_mday;
	int Hour = p->tm_hour;
	int Minute = p->tm_min;
	int Second = p->tm_sec;	
	char year[20];
	char month[20];
	char day[20];
	char hour[20];
	char minute[20];
	char second[20];	
	printf("year=%d\n",Year);
	printf("month=%d\n", Month);
	printf("day=%d\n", Day);
	printf("hour=%d\n", Hour);
	printf("minute=%d\n", Minute);
	printf("second=%d\n", Second);
	return 0;	
}

```
以上为简单的系统时间读取。
在Windows环境下好像运行是有问题的，这个时候需要在预编译头那里添加 _CRT_SECURE_NO_WARNINGS即可。

**localtime**函数获得的tm结构体的时间，是已经进行过时区转化为本地时间，然而gmtime函数是把日期和时间转换为格林威治(GMT)时间的函数。

## 要用这个时间对文件进行命名

在我们进行编程的时候有时候需要对文件进行命名，有时候一个程序是不停的循环运行的，我们还要区分程序跑出来的文件结果，所以这个时候我们如果使用时间来对文件命名的话就会方便很多，而且一定不会因为重名而被覆盖。

这个时候我们可以用sprintf和strcat这两个函数进行一个组合。
简单的例子如下：


```c
    char name[100] = "1616_";
	char year[20];
	char month[20];
	char day[20];
	char hour[20];
	char minute[20];
	char second[20];
	char txt[5] = ".txt";	
	sprintf(year, "%d", Year);
	sprintf(month, "%d", Month);
	sprintf(day, "%d", Day);
	sprintf(hour, "%d", Hour);
	sprintf(minute, "%d", Minute);
	sprintf(second, "%d", Second);
	strcat(name, year);
	strcat(name, month);
	strcat(name, day);
	strcat(name, hour);
	strcat(name, minute);
	strcat(name, second);
	strcat(name, txt);
```