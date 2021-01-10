
+++
title= "Python勉強"
date= 2020-12-26T19:24:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["ML"]
tags = ["python", "機械学習"]
keywords = ["python", "機械学習"]
+++

こちらをやるにあたってメモしていこうと思う。
https://qiita.com/yoshizaki_kkgk/items/79f4056901dd9c059afb


### 仮想通貨のデータを取得する

Poloniexを使ってBitcoinのデータを取得することが出来る。

requirements.txtに以下のように追加した。
```
# 仮想通貨データを取得するPoloniexのwrapper
# https://github.com/s4w3d0ff/python-poloniex
poloniexapi==0.5.7
```

使い方の例として、

```python
from poloniex import Poloniex
polo = Poloniex()
json = polo.returnChartData(currencyPair='USDT_BTC', period=polo.DAY,
                                    start=time.time() - polo.DAY * 500, end=time.time())
```
このように書き、これで
- 1日間隔で、現在から500日前までのUSDT_BTCのJSON形式のデータを取得することができる。

### JSONをPandasに変換する

Pandasとば、データ解析を支援する機能を提供するライブラリとのこと(https://qiita.com/ysdyt/items/9ccca82fc5b504e7913a)
データを扱うにはPandasを使うのが扱いやすいとのことで、JSONからPandasのDataFrameという型に変換するには、

```python
import pandas as pd
df = pd.DataFrame(json)
```
とするだけで良い。

ちなみに、下記のように書くとdate,low,high,open,closeのデータなどが取れる。

```python
print(df.loc[0:9,['date', 'low', 'high',  'open', 'close']])
```
```
         date             low            high            open           close
0  1565758004  10052.17796145  10052.17796145  10052.17796145  10052.17796145
1  1565827200    9523.4400982  10445.99999999  10054.59980013  10311.39375692
2  1565913600            9750           10520  10305.65100999  10349.64220512
3  1566000000  10001.00000001  10459.70000002  10346.04220416  10214.13066121
4  1566086400  10082.94992633  10457.67618276  10214.02298649  10314.99999992
5  1566172800  10260.95659381         10929.8  10302.23008702  10915.76304202
6  1566259200   10519.8244101        10942.17  10914.69999999   10747.1349111
7  1566345600   9886.05320926  10799.53171858  10763.86594711  10134.36772855
8  1566432000            9775  10229.23754879        10134.52  10103.61402433
9  1566518400  10029.59444478  10441.70679653  10101.00000001  10383.75091678
```

### DataFrameをグラフに描画するには

グラフを描くにはMatplotlibというライブラリを使うのが一般的とのこと。

```python
import matplotlib.pyplot as plt
plt.plot(df['close'])
plt.show()
```




Seaborn
https://myenigma.hatenablog.com/entry/2015/10/09/223629


### グラフに複数の線をプロットするには

追加する線のデータとして、移動平均を計算してみます。
いま`df`は1日ごとのclose時のデータなので、5日の移動平均を計算するには、次のようにするだけでよさそうです。

```python
data_short = df.rolling(5).mean()
```

この5日の移動平均データをプロットするには、`show`の前に`plt.plot` を書くだけです。

```python
import matplotlib.pyplot as plt
plt.plot(df['close'])
plt.plot(data_short, color='#f1c40f') # 追加
plt.show()
```


### Pythonの基本

#### classのコンストラクタ

このように書いたら
```
class Ml:
    def __init__(self, df):
        self.closeDataFrame = df
```
このように出来る。

```
Ml(df)
```

### PyTorch

PyTorchをインストール

```requirements.txt
# PyTorch
# https://pytorch.org/get-started/locally/#mac-anaconda
torch==1.7.1
torchvision==0.8.2
```

PyTorchインストールの確認

```
import torch


def pytorch():
    x = torch.rand(5, 3)
    print(x)
```

実行すると、次のようになればインストールOK

```
tensor([[0.5370, 0.7126, 0.1732],
        [0.5158, 0.6087, 0.3694],
        [0.5605, 0.2149, 0.0579],
        [0.3865, 0.1120, 0.1044],
        [0.5769, 0.7161, 0.7334]])
```

#### PyTorch チュートリアル
テンソルは、配列や行列に非常に似た特殊なデータ構造です。PyTorchでは、テンソルを使ってモデルの入力と出力、モデルのパラメータをエンコードします。

##### Tensorの初期化
テンソルの初期化にはいくつか方法があります。

- データから直接
```
data = [[1, 2],[3, 4]]
x_data = torch.tensor(data)
```

- Numpy arrayから

```
np_array = np.array(data)
x_np = torch.from_numpy(np_array)
```

上記の出力はそれぞれ同じで

```
tensor([[1, 2],
        [3, 4]])
```
となります。


- 他のTensorから

```
x_ones = torch.ones_like(x_data)
print(f"Ones Tensor:\n{x_ones}\n")
x_rand = torch.rand_like(x_data, dtype=torch.float)
print(f"Random Tensor:\n{x_rand}\n")
```

```
Ones Tensor:
tensor([[1, 1],
        [1, 1]])

Random Tensor:
tensor([[0.0630, 0.8669],
        [0.8863, 0.7345]])
```

見て分かるように、引数のTensorのプロパティ（形状とdata型）を保持し、値は保持しません。


- ランダム、Constant Values
```
shape = (2, 3,)
rand_tensor = torch.rand(shape)
print(f"Random Tensor:\n{rand_tensor}")
ones_tensor = torch.ones(shape)
print(f"Ones Tensor:\n{ones_tensor}")
zeros_tensor = torch.zeros(shape)
print(f"Zeros Tensor:\n{zeros_tensor}")
```
出力
```
Random Tensor:
tensor([[0.9787, 0.6661, 0.9095],
        [0.3474, 0.8071, 0.5095]])
Ones Tensor:
tensor([[1., 1., 1.],
        [1., 1., 1.]])
Zeros Tensor:
tensor([[0., 0., 0.],
        [0., 0., 0.]])
```
##### Tensor Attributes

```
tensor = torch.rand(3, 4)
print(tensor)
print(f"Shape of tensor: {tensor.shape}")
print(f"DataType of tensor: {tensor.dtype}")
print(f"Device tensor is stored on: {tensor.device}")
```

出力
```
tensor([[0.7128, 0.0874, 0.7596, 0.2232],
        [0.6125, 0.2712, 0.7320, 0.2892],
        [0.7133, 0.3881, 0.0178, 0.2333]])
Shape of tensor: torch.Size([3, 4])
DataType of tensor: torch.float32
Device tensor is stored on: cpu
```

##### Tensor操作
- Numpyのように操作できる
```
tensor = torch.ones(4, 4)
tensor[:, 1] = 0
print(tensor)
```
出力
```
tensor([[1., 0., 1., 1.],
        [1., 0., 1., 1.],
        [1., 0., 1., 1.],
        [1., 0., 1., 1.]])
```

- tensorの結合

```
    t1 = torch.cat([tensor, tensor, tensor], dim=1)
    print(t1)
```

```
tensor([[1., 0., 1., 1., 1., 0., 1., 1., 1., 0., 1., 1.],
        [1., 0., 1., 1., 1., 0., 1., 1., 1., 0., 1., 1.],
        [1., 0., 1., 1., 1., 0., 1., 1., 1., 0., 1., 1.],
        [1., 0., 1., 1., 1., 0., 1., 1., 1., 0., 1., 1.]])
```

- Tensorの掛け算

```
print(f"tensor.mul(tensor)\n{tensor.mul(tensor)}")
print(f"tensor * tensor\n{tensor * tensor}")
```
```
tensor.mul(tensor)
tensor([[1., 0., 1., 1.],
        [1., 0., 1., 1.],
        [1., 0., 1., 1.],
        [1., 0., 1., 1.]])
tensor * tensor
tensor([[1., 0., 1., 1.],
        [1., 0., 1., 1.],
        [1., 0., 1., 1.],
        [1., 0., 1., 1.]])
```

- 行列の積
```
print(f"tensor.matmul(tensor.T) \n {tensor.matmul(tensor.T)} \n")
# Alternative syntax:
print(f"tensor @ tensor.T \n {tensor @ tensor.T}")
```

```
tensor.matmul(tensor.T) 
 tensor([[3., 3., 3., 3.],
        [3., 3., 3., 3.],
        [3., 3., 3., 3.],
        [3., 3., 3., 3.]]) 

tensor @ tensor.T 
 tensor([[3., 3., 3., 3.],
        [3., 3., 3., 3.],
        [3., 3., 3., 3.],
        [3., 3., 3., 3.]])
```

- Tensorの中身を変える

suffix `_` がつく。
```
tensor.add_(5)
print(tensor)
```

```
tensor([[6., 5., 6., 6.],
        [6., 5., 6., 6.],
        [6., 5., 6., 6.],
        [6., 5., 6., 6.]])
```
# 統計

### 四分位範囲(interquartile range, IQR)とは

第3四分位数と第1四分位数の差のこと。

四分位数とは、

q 


### 中央値とは
データを昇順に並べたときの真ん中の値のこと。

たとえば、次のようなデータがあったとき、
```
[ 3  2 10 15  2 16  7  5  9]
```

昇順に並べ替えると、次のようになり。

```
[ 2  2  3  5  7  9 10 15 16]
```

真ん中の値、つまり中央値は`7`となります。


これをnumpyを使って計算して見ると、次のようになります。

```
import numpy as np

data = np.array([3, 2, 10, 15, 2, 16, 7, 5, 9])
print(data)
data = np.sort(data)
print(data)
median = np.median(data)
print(median)
```

```
[ 3  2 10 15  2 16  7  5  9]
[ 2  2  3  5  7  9 10 15 16]
7.0
```

※ここではわかりやすさのために`np.sort`していますが、`np.median`を実行するまえに並べ替える必要はありません。
https://stackoverflow.com/a/16872271


