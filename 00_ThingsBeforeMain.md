# main函数运行之前
### BrewFunction的注册
在`caffe.cpp`中有这样一个宏:
```C++
#define RegisterBrewFunction(func) \
namespace { \
class __Registerer_##func { \
    public: \
    __Registerer_##func() { \
        g_brew_map[#func] = &func; \
    } \
}; \
__Registerer_##func g_registerer_##func; \
}
```
其中`##var`拼接##前面的字符串和后面的`func`变量对应的变量名字符串, 而`g_brew_map`是一个全局的string -> BrewFunction的std::map, 即一个由string到函数指针的映射.</br>
而在caffe.cpp中四个函数: `int device_query(); int train(); int test(); int time();`
后面都调用了一次此宏, 即把字符串: "device_query", "train", "test", "time"分别映射到了上述四个函数. 该过程发生在main函数之前.

### Layer的工厂模式
在`layer_factory.hpp`中有两个类: 
* `LayerRegistry`</br>
    其中有一个`static map<string, Creator>`类型的变量, 而`Creator`是一个函数指针, 其功能是创建一个`Layer`并返回这个`Layer`的指针. 显然这个`map`维护的就是当前caffe框架可以支持的Layer, 每一种`string`对应一个`Layer`的构造并获取其指针.</br>
    而该类中主要有两个函数:
    ```C++
    // 在上述`map`中添加一个新元素, 即一种新的`Layer`.
    static void AddCreator(const string& type, Creator creator);
    // 利用para.type找到对应Layer的Creator函数, 利用此函数实例化一个Layer
    static shared_ptr<Layer<Dtype>> CreateLayer(const LayerParameter& param)
    ```

* `LayerRegisterer`</br>
    其功能就是封装了一下`LayerRegistry`中的`AddCreator`, 为网络注册一个新的层

此外该文件中还由两个宏:
```C++
#define REGISTER_LAYER_CREATOR(type, creator) \
    static LayerRegisterer<float> g_creator_f_##type(#type, creator<float>); \
    static LayerRegisterer<double> g_creator_d_##type(#type, creator<double>) \
```
```C++
#define REGISTER_LAYER_CLASS(type) \
    template <typename Dtype> \
    shared_ptr<Layer<Dtype> > Creator_##type##Layer(const LayerParameter& param) \
    { \
        return shared_ptr<Layer<Dtype> >(new type##Layer<Dtype>(param)); \
    } \
    REGISTER_LAYER_CREATOR(type, Creator_##type##Layer)
```
而在每个具体类型的layer的.cpp文件中最后都会调用`REGISTER_LAYER_CLASS`这个宏, 此宏中会利用该layer的构造函数生成一个返回该layer的指针的Creator函数.</br>
得到这个函数后, 会把这个函数的指针以及type送给另一个宏`REGISTER_LAYER_CREATOR`, 在该宏中会调用`LayerRegisterer`类把这种layer也添加到上述的`static map`中, 从而实现了框架对于每个Layer的支持.</br>
该过程也发生于main()函数运行之前.
