## caffe-ssd+vs2015编译（Microsoft版本）

### 1. 配置环境

- 系统：windows7/10 64位
- 编译：Visual Studio 2015

### 2. 准备工作

#### 2.1 caffe-ssd-microsoft下载

本教程使用[caffe-ssd-microsoft](https://github.com/conner99/caffe)版本，进入github(https://github.com/conner99/caffe)并选择ssd-microsoft分支，下载并解压。

![1550998453972](https://github.com/Zdafeng/caffe-ssd-microsoft-vs2015/tree/master/img/1550998453972.png)

#### 2.2 配置CommonSettings.props

进入caffe-ssd-microsoft\windows文件夹，找到CommonSettings.props.example文件，然后复制一份并更改文件名为CommonSettings.props

![1550998766139](https://github.com/Zdafeng/caffe-ssd-microsoft-vs2015/tree/master/img/1550998766139.png)

打开CommonSettings.props文件，根据需求更改相应配置。

![1550998881106](https://github.com/Zdafeng/caffe-ssd-microsoft-vs2015/tree/master/img/1550998881106.png)

![1550999031749](https://github.com/Zdafeng/caffe-ssd-microsoft-vs2015/tree/master/img/1550999031749.png)

#### 2.3 注释掉含有regex或者rv的代码

找到下面三个文件，注释掉含有regex或者rv的代码。

**detection_output_layer.hpp** 

```c++
//#include <boost/regex.hpp>
```

**detection_output_layer.cpp** 

```c++
//boost::regex exp("\"(null|true|false|-?[0-9]+(\\.[0-9]+)?)\"");
ptree output;
output.add_child("detections", detections_);
std::stringstream ss;
write_json(ss, output);
//std::string rv = boost::regex_replace(ss.str(), exp, "$1");
//outfile << rv.substr(rv.find("["), rv.rfind("]") - rv.find("["))
//    << std::endl << "]" << std::endl;
```

**detection_output_layer.cu**

```c++
//boost::regex exp("\"(null|true|false|-?[0-9]+(\\.[0-9]+)?)\"");
ptree output;
output.add_child("detections", detections_);
std::stringstream ss;
write_json(ss, output);
//std::string rv = boost::regex_replace(ss.str(), exp, "$1");
//outfile << rv.substr(rv.find("["), rv.rfind("]") - rv.find("["))
//    << std::endl << "]" << std::endl;
```

#### 2.4 注释掉所有含thrust的语句

打开bbox_util.cu文件，注释掉所有含thrust的语句

**bbox_util.cu**

```c++
//#include "thrust/functional.h"
//#include "thrust/sort.h"


  //thrust::sort_by_key(&confidences[0], &confidences[0] + num_remain, &idx[0],
  //    thrust::greater<Dtype>());
```

#### 2.5 添加3rdparty文件

##### 2.5.1 添加3rdparty文件

在caffe-ssd-microsoft\include\caffe\3rdparty下添加hungarian.h文件；

在caffe-ssd-microsoft \src \caffe \3rdparty下添加hungarian.cpp文件；

在libcaffe项目中创建的src和include下分别创建3rdparty文件夹，并将hungarian.cpp和hungarian.h文件添加进项目中；

下载链接: https://pan.baidu.com/s/1GSdyjCoF3H59IUp7awwAfQ 提取码: l6p5

##### 2.5.2 下载thirdparty依赖库

下载thirdparty依赖库，解压至caffe-ssd-microsoft\windows\thirdparty目录下；

下载链接: https://pan.baidu.com/s/1xf8p6TDEfR3e-HESyTeqrg 提取码: j903

将caffe-ssd-microsoft\windows\thirdparty\bins添加环境变量；

打开Caffe.sln文件，配置thirdparty依赖库的[包含目录]、[库目录]、[附加依赖项]；

![1551000599275](https://github.com/Zdafeng/caffe-ssd-microsoft-vs2015/tree/master/img/1551000599275.png)

![1551000624473](https://github.com/Zdafeng/caffe-ssd-microsoft-vs2015/tree/master/img/1551000624473.png)

#### 2.6 取消官方NuGet库下载

点击[项目]—>[管理NuGet程序包]—>[设置]—>[NuGet包管理器]—>[允许NuGet下载缺少的程序包]

![1551001069471](https://github.com/Zdafeng/caffe-ssd-microsoft-vs2015/tree/master/img/1551001069471.png)

![1551001091014](https://github.com/Zdafeng/caffe-ssd-microsoft-vs2015/tree/master/img/1551001091014.png)

![1551001117711](https://github.com/Zdafeng/caffe-ssd-microsoft-vs2015/tree/master/img/1551001117711.png)

#### 2.7 C/C++ –> 常规 里的将警告提示为错误修改 为 **否**

将C/C++ –> 常规 里的[将警告提示为错误]修改 为 **否**

![1551001506284](https://github.com/Zdafeng/caffe-ssd-microsoft-vs2015/tree/master/img/1551001506284.png)

#### 2.8 删除官方NugetPackages配置

在caffe-ssd-microsoft\windows下搜索所有**.vcxproj**文件，并修改PlatformToolset位v140或者删除NugetPackagesx相关的内容：

修改1

```html
<PlatformToolset>v140</PlatformToolset>
```

删除1

```html
<Import Project="..\..\..\NugetPackages\glog.0.3.3.0\build\native\glog.props" Condition="Exists('..\..\..\NugetPackages\glog.0.3.3.0\build\native\glog.props')" />
<Import Project="..\..\..\NugetPackages\gflags.2.1.2.1\build\native\gflags.props" Condition="Exists('..\..\..\NugetPackages\gflags.2.1.2.1\build\native\gflags.props')" />
<Import Project="..\..\..\NugetPackages\OpenCV.2.4.10\build\native\OpenCV.props" Condition="Exists('..\..\..\NugetPackages\OpenCV.2.4.10\build\native\OpenCV.props')" />
```

删除2

```html
<ItemGroup>
  <None Include="packages.config" />
</ItemGroup>
```

删除3

```html
<ImportGroup Label="ExtensionTargets">
    <Import Project="..\..\..\NugetPackages\OpenBLAS.0.2.14.1\build\native\openblas.targets" Condition="Exists('..\..\..\NugetPackages\OpenBLAS.0.2.14.1\build\native\openblas.targets')" />
    <Import Project="..\..\..\NugetPackages\OpenCV.2.4.10\build\native\OpenCV.targets" Condition="Exists('..\..\..\NugetPackages\OpenCV.2.4.10\build\native\OpenCV.targets')" />
    <Import Project="..\..\..\NugetPackages\hdf5-v120-complete.1.8.15.2\build\native\hdf5-v120.targets" Condition="Exists('..\..\..\NugetPackages\hdf5-v120-complete.1.8.15.2\build\native\hdf5-v120.targets')" />
    <Import Project="..\..\..\NugetPackages\boost_chrono-vc120.1.59.0.0\build\native\boost_chrono-vc120.targets" Condition="Exists('..\..\..\NugetPackages\boost_chrono-vc120.1.59.0.0\build\native\boost_chrono-vc120.targets')" />
    <Import Project="..\..\..\NugetPackages\boost_date_time-vc120.1.59.0.0\build\native\boost_date_time-vc120.targets" Condition="Exists('..\..\..\NugetPackages\boost_date_time-vc120.1.59.0.0\build\native\boost_date_time-vc120.targets')" />
    <Import Project="..\..\..\NugetPackages\boost_filesystem-vc120.1.59.0.0\build\native\boost_filesystem-vc120.targets" Condition="Exists('..\..\..\NugetPackages\boost_filesystem-vc120.1.59.0.0\build\native\boost_filesystem-vc120.targets')" />
    <Import Project="..\..\..\NugetPackages\boost_regex-vc120.1.59.0.0\build\native\boost_regex-vc120.targets" Condition="Exists('..\..\..\NugetPackages\boost_regex-vc120.1.59.0.0\build\native\boost_regex-vc120.targets')" />
	<Import Project="..\..\..\NugetPackages\boost_system-vc120.1.59.0.0\build\native\boost_system-vc120.targets" Condition="Exists('..\..\..\NugetPackages\boost_system-vc120.1.59.0.0\build\native\boost_system-vc120.targets')" />
  	<Import Project="..\..\..\NugetPackages\boost.1.59.0.0\build\native\boost.targets" Condition="Exists('..\..\..\NugetPackages\boost.1.59.0.0\build\native\boost.targets')" />
    <Import Project="..\..\..\NugetPackages\boost_thread-vc120.1.59.0.0\build\native\boost_thread-vc120.targets" Condition="Exists('..\..\..\NugetPackages\boost_thread-vc120.1.59.0.0\build\native\boost_thread-vc120.targets')" />
    <Import Project="..\..\..\NugetPackages\boost_python2.7-vc120.1.59.0.0\build\native\boost_python-vc120.targets" Condition="Exists('..\..\..\NugetPackages\boost_python2.7-vc120.1.59.0.0\build\native\boost_python-vc120.targets')" />
    <Import Project="..\..\..\NugetPackages\gflags.2.1.2.1\build\native\gflags.targets" Condition="Exists('..\..\..\NugetPackages\gflags.2.1.2.1\build\native\gflags.targets')" />
    <Import Project="..\..\..\NugetPackages\glog.0.3.3.0\build\native\glog.targets" Condition="Exists('..\..\..\NugetPackages\glog.0.3.3.0\build\native\glog.targets')" />
    <Import Project="..\..\..\NugetPackages\protobuf-v120.2.6.1\build\native\protobuf-v120.targets" Condition="Exists('..\..\..\NugetPackages\protobuf-v120.2.6.1\build\native\protobuf-v120.targets')" />
    <Import Project="..\..\..\NugetPackages\LevelDB-vc120.1.2.0.0\build\native\LevelDB-vc120.targets" Condition="Exists('..\..\..\NugetPackages\LevelDB-vc120.1.2.0.0\build\native\LevelDB-vc120.targets')" />
    <Import Project="..\..\..\NugetPackages\lmdb-v120-clean.0.9.14.0\build\native\lmdb-v120-clean.targets" Condition="Exists('..\..\..\NugetPackages\lmdb-v120-clean.0.9.14.0\build\native\lmdb-v120-clean.targets')" />
  </ImportGroup>
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\..\NugetPackages\OpenBLAS.0.2.14.1\build\native\openblas.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\OpenBLAS.0.2.14.1\build\native\openblas.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\OpenCV.2.4.10\build\native\OpenCV.props')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\OpenCV.2.4.10\build\native\OpenCV.props'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\OpenCV.2.4.10\build\native\OpenCV.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\OpenCV.2.4.10\build\native\OpenCV.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\hdf5-v120-complete.1.8.15.2\build\native\hdf5-v120.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\hdf5-v120-complete.1.8.15.2\build\native\hdf5-v120.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\boost_chrono-vc120.1.59.0.0\build\native\boost_chrono-vc120.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\boost_chrono-vc120.1.59.0.0\build\native\boost_chrono-vc120.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\boost_date_time-vc120.1.59.0.0\build\native\boost_date_time-vc120.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\boost_date_time-vc120.1.59.0.0\build\native\boost_date_time-vc120.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\boost_filesystem-vc120.1.59.0.0\build\native\boost_filesystem-vc120.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\boost_filesystem-vc120.1.59.0.0\build\native\boost_filesystem-vc120.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\boost_regex-vc120.1.59.0.0\build\native\boost_regex-vc120.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\boost_regex-vc120.1.59.0.0\build\native\boost_regex-vc120.targets'))" />
	<Error Condition="!Exists('..\..\..\NugetPackages\boost_system-vc120.1.59.0.0\build\native\boost_system-vc120.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\boost_system-vc120.1.59.0.0\build\native\boost_system-vc120.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\boost.1.59.0.0\build\native\boost.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\boost.1.59.0.0\build\native\boost.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\boost_thread-vc120.1.59.0.0\build\native\boost_thread-vc120.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\boost_thread-vc120.1.59.0.0\build\native\boost_thread-vc120.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\boost_python2.7-vc120.1.59.0.0\build\native\boost_python-vc120.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\boost_python2.7-vc120.1.59.0.0\build\native\boost_python-vc120.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\gflags.2.1.2.1\build\native\gflags.props')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\gflags.2.1.2.1\build\native\gflags.props'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\gflags.2.1.2.1\build\native\gflags.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\gflags.2.1.2.1\build\native\gflags.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\glog.0.3.3.0\build\native\glog.props')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\glog.0.3.3.0\build\native\glog.props'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\glog.0.3.3.0\build\native\glog.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\glog.0.3.3.0\build\native\glog.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\protobuf-v120.2.6.1\build\native\protobuf-v120.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\protobuf-v120.2.6.1\build\native\protobuf-v120.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\LevelDB-vc120.1.2.0.0\build\native\LevelDB-vc120.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\LevelDB-vc120.1.2.0.0\build\native\LevelDB-vc120.targets'))" />
    <Error Condition="!Exists('..\..\..\NugetPackages\lmdb-v120-clean.0.9.14.0\build\native\lmdb-v120-clean.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\..\NugetPackages\lmdb-v120-clean.0.9.14.0\build\native\lmdb-v120-clean.targets'))" />
  </Target>
```

### 3. 编译

先编译libcaffe，再编译caffe



**参考资料：**

1. https://github.com/Microsoft/caffe
2. https://github.com/conner99/caffe
3. https://github.com/rb0518/caffe-windows-ssd/tree/ssd-microsoft
4. https://github.com/Zdafeng/caffe-windows
5. https://blog.csdn.net/sinat_30627475/article/details/81326234
