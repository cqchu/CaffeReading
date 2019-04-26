# Caffe网络的初始化流程

caffe的`main()`函数位于tools/caffe.cpp中, 进入其中后是初始化其所用的几个google库中的一些变量. 经过这些无关紧要的设置之后, caffe中出现了这样一句代码: `GetBrewFunction(caffe::string(argv[1]))()`. 其根据命令行参数: "train", "test", "device_query", "time", 返回对应的函数指针, 这些函数的注册在[00_ThingsBeforeMain](00_ThingsBeforeMain.md)中有说明.

此处以"train"为例, caffe.cpp中的`train()`函数被调用