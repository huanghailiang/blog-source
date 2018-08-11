---
title: VXL库的简单使用
date: 2017-11-07 13:58:53
tags: 工具的使用
---

# VXL库

该库是进行矩阵运算的一个矩阵库，个人感觉使用起来跟eigen库还是比较像，毕竟都是基于C++编写的矩阵运算库。
<!--more-->
# 资源

该库是ITK这个开源项目中的数学计算部分，ITK是一个开源的跨平台的图像分析算法研究平台。感觉国内使用的人好像并不是很多，所以之前找到的资料也比较少，下面是自己找到的资源的链接：

http://xueshu.baidu.com/s?wd=%E5%9F%BA%E4%BA%8EITK%E6%95%B0%E5%AD%A6%E5%BA%93%E7%9A%84%E7%9F%A9%E9%98%B5%E5%9F%BA%E6%9C%AC%E8%BF%90%E7%AE%97&rsv_bp=0&tn=SE_baiduxueshu_c1gjeupa&rsv_spt=3&ie=utf-8&f=8&rsv_sug2=1&sc_f_para=sc_tasktype%3D%7BfirstSimpleSearch%7D&rsv_n=2


https://public.kitware.com/vxl/doc/release/


## VXL库的下载

我使用的是vxl-1.17.0可以直接百度下载：

https://zh.osdn.net/projects/sfnet_vxl/downloads/vxl/1.17b/vxl-1.17.0-b.tar.gz/

或者可以在我的github上进行下载：

https://github.com/huanghailiang/vxl-1.17.0.git

# VXL库的简单使用

在上面资源的链接中都有讲vxl这个库的使用，尤其VXL Documentation中更加全面。

下面是自己给的一些最常用到的一些简单的例子，如果后面有时间有兴趣的话会接着更新，如果没有就.....

编写CMakeList.txt来进行链接库生成工程，省去自己链接库的麻烦：

```C
#=========================================================================
# $Author$
# $Date$
# $Revision$
#=========================================================================

CMAKE_MINIMUM_REQUIRED(VERSION 2.4)
IF(COMMAND cmake_policy)
    cmake_policy(SET CMP0003 NEW)
ENDIF(COMMAND cmake_policy)

PROJECT(VNL)


FIND_PACKAGE(VXL)
   

IF(VXL_FOUND)
  INCLUDE(${VXL_CMAKE_DIR}/UseVXL.cmake)
ENDIF(VXL_FOUND)

IF(WIN32)
ADD_DEFINITIONS(-D_ALLOW_KEYWORD_MACROS)
ENDIF(WIN32)


#IF( VXL_VNL_FOUND )  
INCLUDE_DIRECTORIES( ${VXL_VNL_INCLUDE_DIR} )

                          
SET(IO_API_SRCS
		common.cpp
		
		 )
		 
file(GLOB_RECURSE IO_API_HDRS 
		common.h
		
		 )
		 

ADD_LIBRARY(io_api ${API_LIBRARY_TYPE} ${IO_API_SRCS} ${IO_API_HDRS})


SET( VNL ${VNL} vnl_example.cpp )
ADD_EXECUTABLE( VNL ${VNL})
TARGET_LINK_LIBRARIES( VNL io_api vnl_algo vnl vcl netlib)
```

自己照着编写的一些简单的使用例子：

```C
/**************************************************/
//该程序主要是为了学习使用VXL这个库而制作的
//数字vnl库旨在为数字编程提供一个环境，结合了Mathematica和Matlab等软件的易用性，C的速度和C++的优雅。
/**************************************************/

/*************************************************/
//矩阵的模板类
/*************************************************/
#include <vnl/vnl_matrix.h>
#include <vcl_iostream.h>
int main()
{
	//定义一个矩阵A而不给它设定大小
	vnl_matrix<double> A;
	printf("A_rows=%d \t A_cols=%d\n", A.rows(), A.cols());
	//定义一个矩阵B设定大小为3*5的矩阵,但是不给初值
	vnl_matrix<double> B(3, 5);
	printf("B_rows=%d \t B_cols=%d\n", B.rows(), B.cols());
	//定义一个矩阵C设定大小为3*5的矩阵，并且给任意的初值，这里设置初值为1
	vnl_matrix<double> C(3, 5, 1);
	printf("C_rows=%d \t C_cols=%d\n", C.rows(), C.cols());
	//矩阵输出，这个格式跟C++很像，不过需要包含 vcl_iostream.h 这个头文件
	vcl_cerr <<" C 初值为1"<< vcl_endl;
	printf("C_rows=%d \t C_cols=%d\n", C.rows(), C.cols());
	vcl_cerr << C << vcl_endl;
	//定义一个矩证D，初值为0
	vnl_matrix<double> D(3, 3, vnl_matrix_null);
	vcl_cerr <<" D 初值为0  vnl_matrix_null"<< vcl_endl;
	printf("D_rows=%d \t D_cols=%d\n", D.rows(), D.cols());
	vcl_cerr << D << vcl_endl;
	//单位矩阵
	//定义一个 单位矩证 E ，单位矩阵的话，矩阵的长宽一定是一样大的
	vnl_matrix<double> E(3, 3, vnl_matrix_identity);
	vcl_cerr <<" E 单位阵  vnl_matrix_identity"<< vcl_endl;
	printf("E_rows=%d \t E_cols=%d\n", E.rows(), E.cols());
	vcl_cerr << E << vcl_endl;
	//矩阵的前n位(第三个参数)自动被数组赋值，其他位不变，行优先
	double f[] = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15 };
	vnl_matrix<double> F(3, 5, 10, f);
	vcl_cerr << " F 前n位被数组自动赋值" << vcl_endl;
	printf("F_rows=%d \t F_cols=%d\n", F.rows(), F.cols());
	vcl_cerr << F << vcl_endl;
	//矩阵
	double g[] = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15 };
	vnl_matrix<double> G(g, 3, 5);
	vcl_cerr << " G 被一块内存初始化" << vcl_endl;
	printf("G_rows=%d \t G_cols=%d\n", G.rows(), G.cols());
	vcl_cerr << G << vcl_endl;
}
```




