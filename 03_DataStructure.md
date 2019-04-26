blob数据成员:</br>
    data_           : SyncedMemory类型的指针, 存储blob前向中的数据</br>
    diff_           : SyncedMemory类型的指针, 存储blob的梯度</br>
    shape_          : vector<int>刻画blob每个维度的大小</br>
    count_          : blob中所有元素的数量</br>
    capacity_       : 类似于vector中的capacity，避免反复分配内存</br>
    connectivity_   : SyncedMemory类型的指针, 用于描述稀疏性的mask</br>

blob函数成员:</br>
    Reshape()       : 如名, set shape_并分配内存</br>
    cpu_data()      : 获取cpu_data, gpu/diff也是类似的</br>
    set_cpu_data()  : 设置cpu_data, gpu/diff也是类似的</br>
    ShareData()     : 将当前blob的data_指向某个其他blob_的data, ShareDiff也是类似</br>
    Update()        : 用diff_来更新data_</br>
    asum_data()     : 累加blob的data_中所有元素, diff_也是类似的</br>
    sumsq_data()    : 返回blob的data_中的所有元素的平方和, diff_也是类似的</br>
    scale_data()    : data_中元素乘以scale, diff_也是类似的</br>
    ShapeEquals()   : 检查两个blob的shape是否一致</br>
    CopyFrom()      : 从source blob中copy data_/diff_(由参数决定)</br>
    FromProto()     : 从Proto中读取数据到blob中, 一般用于restore</br>
    ToProto()       : 写数据到Proto中, 一般用于save Model</br>

layer数据成员</br>
    param_propagate_down_   : 对应的参数是否进行反向传播</br>

layer函数成员</br>
    LayerSetUp()</br>
    ReShape()               : 调整top blob以适应bottom blob</br>
    Forward()               : 根据bottom计算top/loss</br>
    Backward()              : 根据top/loss的梯度计算bottom梯度</br>
    set_param_propagate_down: 设置某个参数是否进行反向传播</br>
    SetLossWeights(top)     : 给具有非零loss权重的top设置loss权重乘子</br>