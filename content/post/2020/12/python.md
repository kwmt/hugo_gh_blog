
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