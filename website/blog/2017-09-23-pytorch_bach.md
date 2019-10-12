---
title: pytorch批训练
author: woobamboo
authorURL:
authorFBID:
---



# pytorch 的批训练

+ 要点：Torch 中提供了一种帮你整理你的数据结构的好东西, 叫做 `DataLoader`, 我们能用它来**包装自己的数据, 进行批训练.**

<!--truncate-->

## DataLoader

`DataLoader` 是 torch 给你用来包装你的数据的工具. 所以你要将自己的 (numpy array 或其他) 数据形式装换成 `Tensor`, 然后再放进这个包装器中. 使用 `DataLoader` 有什么好处呢? 就是他们帮你**有效地迭代数据**, 举例:

```python
import torch
import torch.utils.data as Data
torch.manual_seed(1)    # reproducible

BATCH_SIZE = 5      # 批训练的数据个数

x = torch.linspace(1, 10, 10)       # x data (torch tensor) x 从1到10
y = torch.linspace(10, 1, 10)       # y data (torch tensor) y 从10到1

# 先转换成 torch 能识别的 Dataset
torch_dataset = Data.TensorDataset(data_tensor=x, target_tensor=y)

# 把 dataset 放入 DataLoader
loader = Data.DataLoader(
    dataset=torch_dataset,      # torch TensorDataset format
    batch_size=BATCH_SIZE,      # mini batch size
    shuffle=True,               # 要不要打乱数据 (打乱比较好)
    num_workers=2,              # 多线程来读数据
)


for epoch in range(3):   # 训练所有!整套!数据 3 次
    for step, (batch_x, batch_y) in enumerate(loader):  # 每一步 loader 释放一小批数据用来学习
        # 假设这里就是你训练的地方...

        # 打出来一些数据
        print('Epoch: ', epoch, '| Step: ', step, '| batch x: ',
              batch_x.numpy(), '| batch y: ', batch_y.numpy())


"""
Epoch:  0 | Step:  0 | batch x:  [ 6.  7.  2.  3.  1.] | batch y:  [  5.   4.   9.   8.  10.]
Epoch:  0 | Step:  1 | batch x:  [  9.  10.   4.   8.   5.] | batch y:  [ 2.  1.  7.  3.  6.]
Epoch:  1 | Step:  0 | batch x:  [  3.   4.   2.   9.  10.] | batch y:  [ 8.  7.  9.  2.  1.]
Epoch:  1 | Step:  1 | batch x:  [ 1.  7.  8.  5.  6.] | batch y:  [ 10.   4.   3.   6.   5.]
Epoch:  2 | Step:  0 | batch x:  [ 3.  9.  2.  6.  7.] | batch y:  [ 8.  2.  9.  5.  4.]
Epoch:  2 | Step:  1 | batch x:  [ 10.   4.   8.   1.   5.] | batch y:  [  1.   7.   3.  10.   6.]
"""
```

可以看出, 每步都导出了5个数据进行学习. 然后每个 epoch 的导出数据都是先打乱了以后再导出.

真正方便的还不是这点. 如果我们改变一下 `BATCH_SIZE = 8`, 这样我们就知道, `step=0` 会导出8个数据, 但是, `step=1` 时数据库中的数据不够 8个, 这时怎么办呢:

```python
BATCH_SIZE = 8      # 批训练的数据个数

for :
    for :
        print('Epoch: ', epoch, '| Step: ', step, '| batch x: ',
              batch_x.numpy(), '| batch y: ', batch_y.numpy())
"""
Epoch:  0 | Step:  0 | batch x:  [  6.   7.   2.   3.   1.   9.  10.   4.] | batch y:  [  5.   4.   9.   8.  10.   2.   1.   7.]
Epoch:  0 | Step:  1 | batch x:  [ 8.  5.] | batch y:  [ 3.  6.]
Epoch:  1 | Step:  0 | batch x:  [  3.   4.   2.   9.  10.   1.   7.   8.] | batch y:  [  8.   7.   9.   2.   1.  10.   4.   3.]
Epoch:  1 | Step:  1 | batch x:  [ 5.  6.] | batch y:  [ 6.  5.]
Epoch:  2 | Step:  0 | batch x:  [  3.   9.   2.   6.   7.  10.   4.   8.] | batch y:  [ 8.  2.  9.  5.  4.  1.  7.  3.]
Epoch:  2 | Step:  1 | batch x:  [ 1.  5.] | batch y:  [ 10.   6.]
"""
```

这时, 在 `step=1` 就只给你返回这个 epoch 中剩下的数据就好了.



参考[莫烦python](https://morvanzhou.github.io/tutorials/machine-learning/torch/3-05-train-on-batch/)