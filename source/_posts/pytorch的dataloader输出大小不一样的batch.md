---
title: pytorch的dataloader输出大小不一样的batch
date: 2020/10/14 21:31
tags: 
---



# pytorch的dataloader输出大小不一样的batch



##  问题出现

```python
class MYDATASET(Dataset):
    def __init__(self, soft_data_dir, transforms_=None):
				***************************

    def __getitem__(self, index):
				**********************
        return {'A_256': item_A, 'B_256': item_B, 'A_512': item_A_512, 'B_512': item_B_512}

    def __len__(self):
        return len(self.files_A)
```

期望返回的dict中, 256代表256*256大小的图片, dict中含有两种大小的输出, 在使用时

```python
for i, batch in enumerate(dataloader):
  # Set model input
  real_A_256 = Variable(input_source_256.copy_(batch['A_256']))
  real_B_512 = Variable(input_target_512.copy_(batch['B_512']))
```

若batchsize=1或者num_workers>1的情况下会报错`RuntimeError: invalid argument 0: Sizes of tensors must match except in dimension 0.`

这是因为dataloader在把dataset的`__getitem__`输出拼合成batch的时候无法处理dict这种数据



## 解决方法 ->  collate_fn

DataLoader可以配置collate_fn参数, 用于拼合各个样本. 报错也正是默认的`default_collate`函数中`torch.stack(batch, 0, out=out)`部分出错



```python
from torch.utils.data import DataLoader
import torch

transforms_ = [transforms.ToTensor(),
               transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5))]  # (0,1) -> (-1,1)

class TmpSET(Dataset):
  def __init__(self, transforms_=None):
    self.img = Image.open('./dlrb.jpeg')
    self.transform = transforms.Compose(transforms_)

    def __getitem__(self, index):
      img_A = self.img.resize((512, 512), Image.BILINEAR)
      img_B = self.img.resize((256, 256), Image.BILINEAR)
      item_A = self.transform(img_A)
      item_B = self.transform(img_B)
      return [item_A, item_A, item_B, item_B]
    # return {'A_256': item_A, 'B_256': item_B, 'A_512': item_A, 'B_512': item_B}

    def __len__(self):
      return 100
    
    def my_collate_fn(batch):
      A_256, B_256, A_512, B_512 = zip(*batch)
      return [torch.stack(A_256, 0), torch.stack(B_256, 0), torch.stack(A_512, 0), torch.stack(B_512, 0)]


    dataloader = DataLoader(TmpSET(transforms_=transforms_),
                            batch_size=10, shuffle=True, drop_last=True, num_workers=4, collate_fn=my_collate_fn)


    for i, batch in enumerate(dataloader):
      print(batch[0].shape)
      # print(batch[1].shape)
      # print(batch[2].shape)
      # print(batch[3].shape)
      break

########################################################################
torch.Size([10, 3, 512, 512])
torch.Size([10, 3, 512, 512])
torch.Size([10, 3, 256, 256])
torch.Size([10, 3, 256, 256])
########################################################################
```

这样, 简单的在my_collate_fn中把list拆开手动使用torch.stack合并各项, 就可以正确得到各个nchw的batch数据了


