---
title: CMakeLists的编写
date: 2017-11-01 15:42:27
tags: 工具的使用
---

# CMake工具的理解

CMake是一个很方便的跨平台编译的软件，他可以输出各种各样的makefile或者project文件。
<!--more-->
因为本人经常需要将windows上编写的程序放到linux上进行编译运行等，所以如果自己手动去一个一个编译，去链接各种库；这个时候CMake就可以帮助我们来解决这些麻烦的问题，他不仅仅可以帮助我们生成相应的makefile和project，还会帮助我们去查找系统中所安装的一些依赖库，帮我们做好链接工作【当然，如果我们有些库没有设置环境变量的话，可能会找不到，但是这个时候在windows上我们可以手动添加，在linux上面我们可以通过ccmake来进入可视化界面进行添加或者使用cmake后面加上指令进行添加】。

当然CMake工具也要知道我们的工程需要干什么等，这个时候我们就需要编写CMakeLists.txt文件来告诉计算机我这个工程用到了哪些库，里面的文件是如何链接的等。

# CMakeLists.txt的编写

下面是我自己编写的一个例子：

在这个例子中，我生成了一个名字叫VNL的工程，并且在该工程中我链接了VXL这个库,并且有一个IO接口，这个IO中我们可以编写自己的函数然后生成lib供主函数来调用。

```bash
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

其他库的调用跟上面的方法类似，如果不会也可以自行百度。

例如下面是打开openmp并行的编写：

```bash
FIND_PACKAGE( OpenMP REQUIRED)  
if(OPENMP_FOUND)  
message("OPENMP FOUND")  
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")  
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")  
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")  
endif()
```

# 注：

编写CMakelist的时候要注意一下各个的包含关系等，以及库的调用，还有名字等。

