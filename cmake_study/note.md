## 1.bilibili上视频学习

cmake内部:

```cmake
add_executable(main)  //可执行程序名main
file(GLOB sources CONFIGURE_DEPENDS *.cpp *.h)  //添加源文件
target_sources(main PUBLIC ${sources})  //
```


cmake -B build -G Ninja   //在build文件夹中中构建Ninja相关文件，需要提前安装Ninja（
  brew install ninja）

cmake --build build     //在build文件夹中生成可执行文件

cmake内部:

```cmake
add_executable(main)
aux_source_directory(. sources)  //根据编程语言在.目录下自动获取源文件
aux_source_directory(mylib sources)  //在mylib文件夹下自动获取源文件
target_sources(main PUBLIC ${sources})
```

cmake内部:

```cmake
add_executable(main)
file(GLOB_RECURSE sources CONFIGURE_DEPENDS *cpp *.h)  //自动获取包含所有子文件夹下的目录
  缺点是会将build中的临时cpp加入导致报错，通过把源文件放在src中解决，或者将build放在另一个目录中
target_sources(main PUBLIC ${sources})
```

项目配置变量
cmake内部:

```cmake
cmake_minimum_required(VERSION 3.15)
project(hellocmake LANGUAGES CXX)
set(CMAKE_BUILD_TYPE Release) //Debug:完全不优化，生成调试信息，方便调试程序
  Release:发布模式，优化程度最高，性能最佳，但是编译比Debug慢
  MinSizeRel:最小体积发布，生成的文件比Release更小，不完全优化，减少二进制体积
  RelWithDebInfo:带调试信息发布，生成的文件比Release更大
  默认为空字符串，相当于Debug
add_executable(main main.cpp)
```

cmake内部:

```cmake
if (NOT CMAKE_BUILD_TYPE)  //如果没有定义编译类型
  set(CMAKE_BUILD_TYPE Release)  //那么生成Release模式
endif()
```

cmake内部：

```cmake
cmake_minimum_required(VERSION 3.15)
project(hellocmake)
message("PROJECT_NAME: ${PROJECT_NAME}")  //
message("PROJECT_SOURCE_DIR: ${PROJECT_SOURCE_DIR}")  //表示最近一次调用project的CmakeLists.txt所在的源码目录
message("PROJECT_BINARY_DIR: ${PROJECT_BINARY_DIR}")  //
message("CMAKE_CURRENT_SOURCE_DIR: ${CMAKE_CURRENT_SOURCE_DIR}")  //表示当前源码目录的位置，例如～/hellocmake
message("CMAKE_CURRENT_BINARY_DIR: ${CMAKE_CURRENT_BINARY_DIR}")  //表示当前输出目录的位置，例如～/hellocmake/build
add_executable(main main.cpp)
```

·PROJECT_SOURCE_DIR:当前项目源码路径，也就是存放main.cpp的地方
·PROJECT_BINARY_DIR:当前项目输出路径，也就是存放main.exe的地方
·CMAKE_SOURCE_DIR:根项目源码路径，也就是存放main.cpp的地方
·CMAKE_BINARY_DIR：根项目输出路径，也就是存放main.exe的地方
·PROJECT_IS_TOP_LEVEL:bool类型，表示当前项目是否是（最顶层的）根项目
·PROJECT_NAME:当前项目名
·CMAKE_PROJECT_NAME:根项目的项目名

project(项目名 LANGUAGES 使用的语言列表...)
·C:C语言
·CXX:cpp
·ASM:汇编
·Fortran:
·CUDA:英伟达的CUDA
·OBJC:苹果的Objective-C
·OBJCXX:苹果的Objective-C++
·ISPC:
·不指定LANGUAGES，则默认为C和CXX

设置C++标准

```cmake
set(CMAKE_CXX_STANDARD 17)  //设置c++标准为17
set(CMAKE_CXX_STANDARD_REQUIRED ON)  //默认为OFF，表示是否一定要支持的你的c++标准，若为OFF的情况下，发现编译器不支持c++17，会默认调整到14给你用
set(CMAKE_CXX_EXTENSIONS ON)  //默认为ON，设为ON表示启用GCC有的一些扩展功能，OFF则关闭GCC的扩展功能，只使用标准的c++，要兼容其他编译器如MSVC的项目，会设为OFF防止不小心用了GCC才有的特性

project(项目名 VERSION x.y.z)  //可以把当前项目的版本号设定为x.y.z，之后可以用过PROJECT_VERSION来获取当前项目的版本号
·PROJECT_VERSION_MAJOR获取x
·PROJECT_VERSION_MINOR获取y
·PROJECT_VERSION_PATCH获取z
```

一个标准的CmakeLists.txt模板----------

```cmake
cmake_minimum_required(VERSION 3.15)

set(CAMKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

project(zeno LANGUAGES C CXX)

if (PROJECT_BINARY_DIR STREQUAL PROJECT_SOURCE_DIR)
  message(WARNING "The binary directory of Cmake cannot be the same as source directory!")
endif()

if (NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()

if (WIN32)
  add_definitions(-DNOMINMAX -D_USE_MATH_DEFINES)
endif()

if (NOT MSVC)
  find_program(CCACHE_PROGRAM ccache)
  if (CCACHE_PROGRAM)
    message(STATUS "Found CCache: ${CCACHE_PROGRAM}")
    set_property(GLOBAL PROPERTY RULE_LAUNCH_COMPILE ${CCACHE_PROGRAM})
    set_property(GLOBAL PROPERTY RULE_LAUNCH_LINK ${CCACHE_PROGRAM})
  endif()
endif()
```

一个标准的CmakeLists.txt模板----------

使用静态库，这样会生成一个libmylib.a文件
cmake内部:

```cmake
addd_library(mylib STATIC mylib.cpp)
add_executable(main main.cpp)
target_link_libraries(main PUBLIC mylib)
```

使用动态库
cmake内部:

```cmake
add_library(mylib SHARED mylib.cpp) #shared生成动态库，否则生成静态库
add_executable(main main.cpp)
target_link_libraries(main PUBLIC mylib)
```

使用对象库，对象库类似于静态库，但是不生成.a文件，只由CMake记住该库生成了哪些对象文件
对象库是CMake自创的，绕开了编译器和操作系统的各种繁琐规则，保证了跨平台的统一性
对象库仅仅作为组织代码的方式，而实际生成的可执行文件只有一个，减轻了部署的困难
cmake内部:

```cmake
add_library(mylib OBJECT mylib.cpp)
add_executable(main main.cpp)
target_link_libraries(main PUBLIC mylib)
```

当add_library无参数时，根据BUILD_SHARED_LIBS这个变量的值决定是动态库还是静态库
ON则相当于SHARED，OFF则相当于STATIC
如果未指定该变量，则默认为STATIC
若项目里的add_library都是无参数的，则可以用
cmake -B build -DBUILD_SHARED_LIBS:BOOL=ON
来让项目全部生成动态库

动态库无法链接静态库，因为动态库会变换地址
解决1:让静态库编译时也生成位置无关的代码(PIC)

```cmake
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
add_library(otherlib STATIC otherlib.cpp)
add_library(mylib SHARED mylib.cpp)
target_link_libraries(mylib PUBLIC otherlib)
add_executable(main main.cpp)
target_link_libraries(main PUBLIC mylib)
```

解决2:也可以只针对一个库，只对它启用位置无关的代码(PIC)

```cmake
add_library(other STATIC otherlib.cpp)
set_property(TARGET otherlib PROPERTY POSITION_INDEPENDENT_CODE ON)
add_library(mylib SHARED mylib.cpp)
target_link_libraries(mylib PUBLIC otherlib)
add_executable(main main.cpp)
target_link_libraries(main PUBLIC mylib)
```

cmake内部:
add_executable(main main.cpp)

```cmake
set_property(TARGET main PROPERTY CXX_STANDARD 17)  //采用c++17标准
set_property(TARGET main PROPERTY CXX_STANDARD_REQUIRED ON)  //若编译器不支持c++17则报错
set_property(TARGET main PROPERTY WIN32_ESECUTABLE ON)  //在windows系统中，运行时不启动控制台窗口，只有gui界面，默认OFF
set_property(TARGET main PROPERTY LINK_WHAT_YOU_USE ON)  //告诉编译器不要自动剔除没有引用符号的链接库，默认OFF
set_property(TARGET main PROPERTY LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/lib)  
  //设置动态链接库的输出路径，默认${CMAKE_BINARY_DIR}
set_property(TARGET main PROPERTY ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/lib)
  //设置静态链接库的输出路径，默认${CMAKE_BINARY_DIR}
set_property(TARGET main PROPERTY RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)
  //设置可执行文件的输出路径，默认${CMAKE_BINARY_DIR}
```

链接第三方库
在linux下可以直接链接
cmake内部:

```cmake
add_executable(main main.cpp)
target_link_libraries(main PUBLIC tbb)  //链接第三方库tbb，前提是tbb的头文件在/usr/include
```

更通用的方式:使用find_package(使用了缓存)
cmake内部:

```cmake
add_executable(main main.cpp)
find_package(TBB REQUIRED)  //会查找/usr/lib/cmake/TBB/TBBConfig.cmake这个配置文件
  并根据里面的配置信息创建TBB:tbb这个伪对象(它实际指向真正的tbb库文件路径/usr/lib/libtbb.so)
target_link_libraries(main PUBLIC TBB::tbb)  //然后通过该指令链接TBB:tbb就能正常的工作了
```

cmake内部:

```cmake
message("hello world")  //只输出hello world
message(STATUS "hello world")  //输出-- hello world
message(WARNING "This is a warning sign!")  //表示是警告信息
message(AUTHOR_WARNING "...")  //仅给项目作者看到的警告信息，可以通过-Wno-dev关闭
  使用 cmake -B build -Wno-dev 进行关闭
message(FATAL_ERROR "...")  //表示是错误信息，会终止CMake的运行
message(SEND_ERROR "...")  //表示是错误信息，但之后的语句仍继续执行
set(myVar "hello world")  //设置变量 string myVar = "hello world "
message("myVar is: ${myVar}")  //打印变量
```

cmake在使用cmake -B build的时候，会把一部分信息进行缓存(例如编译器支持的c++版本信息，编译器版本等等)
删除build可以清除缓存
安装过依赖包以后，想再次运行CMake，可是尝试删除～/build/CMakeCache.txt文件试试

cmake内部:

```cmake
set(myvar "hello" CACHE STRING "this is the docstring")  //设置缓存变量，会出现在build/CMakeCache.txt里
  set(变量名 "变量值" CHCHE 变量类型 "注释")
message("myvar is: ${myvar}")
```

更新缓存变量的正确方法
cmake -B build -Dmyvar=world
cmake -B build -DWITH_TBB:BOOL=OFF

变量类型
·STRING:字符串
·FILEPATH:文件路径,/xxx/xxx/xxx/xxx
·PATH:目录路径,/xxx/xxx/
·BOOL:ON和OFF

option(变量名 "描述" 变量值)  //CMake对BOOl类型缓存的set指令提供的一个简写
等价于
set(变量名 CACHE BOOL 变量值 "描述")

变量于作用域
·父模块里定义的变量，会传递给子模块
·子模块定义的变量，
·如果父模块里本来就定义了同名变量，则离开子模块后仍保持父模块原来设置的值
·使用set的PARENT_SCOPE选项，把子模块的变量传递到父模块
  set(MYVAR ON PARENT_SCOPE)

创建一个run伪目标，其执行main的可执行程序
使用$<TARGET_FILE:main>，会让run依赖于mian
cmake内部:

```cmake
add_executalbe(main main.cpp)
add_custom_target(run COMMAND $<TARGET_FILE:main>)
```

cmake内部:

```cmake
add_custom_target(run COMMAND $<TARGET_FILE:main>)
if (CMAKE_EDIT_CPMMAND)
  add_custom_target(configure COMMAND ${CMAKE_EDIT_COMMAND} -B ${CMAKE_BINARY_DIR})
endif()
```



## 2.cmake官方教程学习

