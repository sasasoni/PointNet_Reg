# PointNet_Reg


# Note

## transpose
https://pytorch.org/docs/stable/generated/torch.transpose.html#torch.transpose


torch.transpose(input, dim0, dim1) → Tensor

- input（Tensor）–入力テンソル。

- dim0（int）–転置される最初の次元

- dim1（int）–転置される2番目の次元

2* 3行4列 の 1次元目と2次元目を入れ替えて 2* 4行3列に転置している

```python
>>> x = torch.randn(2, 3, 4)
>>> x
tensor([[[ 0.9807,  0.0329, -0.3041, -1.0789],
         [-0.1989,  0.5325,  0.9603,  1.6502],
         [-1.4203,  0.3572, -0.0059, -1.6910]],

        [[-0.5305,  0.3733,  0.4158,  0.0045],
         [-0.2104,  0.1885,  0.0040,  0.1256],
         [-0.4915,  0.2189, -1.1648,  0.0640]]])
>>> torch.transpose(x, 1, 2)
tensor([[[ 0.9807, -0.1989, -1.4203],
         [ 0.0329,  0.5325,  0.3572],
         [-0.3041,  0.9603, -0.0059],
         [-1.0789,  1.6502, -1.6910]],

        [[-0.5305, -0.2104, -0.4915],
         [ 0.3733,  0.1885,  0.2189],
         [ 0.4158,  0.0040, -1.1648],
         [ 0.0045,  0.1256,  0.0640]]])
```

## view

一つ目の引数に`-1`を入れることで、2つ目の引数で指定した値にサイズ数を自動的に調整してくれる
```python
# ex) 5つのpointを持つ点群データ 2つ
>>> x = torch.randn(2, 5, 3)
>>> x
tensor([[[ 0.3104, -1.5533, -0.4214],
         [ 0.1752, -0.1765, -0.0746],
         [-0.6158,  0.4281, -0.1702],
         [ 2.0274,  2.3959,  0.5081],
         [ 0.1707, -0.5628, -2.4552]],

        [[ 0.8340, -0.4241, -0.4802],
         [-1.6821, -0.5301,  1.2178],
         [ 2.2584, -0.0460,  1.7543],
         [-0.9705, -0.4540,  0.6339],
         [ 0.1246, -0.0607,  0.9205]]])
>>> x.view(-1, 3, 5)
tensor([[[ 0.3104, -1.5533, -0.4214,  0.1752, -0.1765],
         [-0.0746, -0.6158,  0.4281, -0.1702,  2.0274],
         [ 2.3959,  0.5081,  0.1707, -0.5628, -2.4552]],

        [[ 0.8340, -0.4241, -0.4802, -1.6821, -0.5301],
         [ 1.2178,  2.2584, -0.0460,  1.7543, -0.9705],
         [-0.4540,  0.6339,  0.1246, -0.0607,  0.9205]]])
```

### viewをtransposeの違い

viewはテンソルの形を変えます．要素の並び順は変わりません．
transposeはテンソルのランクを入れ替えます．要素の並び順が変わります．

```python
>>> x
tensor([[[ 0.3104, -1.5533, -0.4214],
         [ 0.1752, -0.1765, -0.0746],
         [-0.6158,  0.4281, -0.1702],
         [ 2.0274,  2.3959,  0.5081],
         [ 0.1707, -0.5628, -2.4552]],

        [[ 0.8340, -0.4241, -0.4802],
         [-1.6821, -0.5301,  1.2178],
         [ 2.2584, -0.0460,  1.7543],
         [-0.9705, -0.4540,  0.6339],
         [ 0.1246, -0.0607,  0.9205]]])
>>> x.view(-1, 3, 5)
tensor([[[ 0.3104, -1.5533, -0.4214,  0.1752, -0.1765],
         [-0.0746, -0.6158,  0.4281, -0.1702,  2.0274],
         [ 2.3959,  0.5081,  0.1707, -0.5628, -2.4552]],

        [[ 0.8340, -0.4241, -0.4802, -1.6821, -0.5301],
         [ 1.2178,  2.2584, -0.0460,  1.7543, -0.9705],
         [-0.4540,  0.6339,  0.1246, -0.0607,  0.9205]]])
>>> torch.transpose(x, 1, 2)
tensor([[[ 0.3104,  0.1752, -0.6158,  2.0274,  0.1707],
         [-1.5533, -0.1765,  0.4281,  2.3959, -0.5628],
         [-0.4214, -0.0746, -0.1702,  0.5081, -2.4552]],

        [[ 0.8340, -1.6821,  2.2584, -0.9705,  0.1246],
         [-0.4241, -0.5301, -0.0460, -0.4540, -0.0607],
         [-0.4802,  1.2178,  1.7543,  0.6339,  0.9205]]])
```

## MaxPool1d

torch.nn.MaxPool1d(kernel_size)

```python
>>> x
tensor([[[ 0.9807,  0.0329, -0.3041, -1.0789],
         [-0.1989,  0.5325,  0.9603,  1.6502],
         [-1.4203,  0.3572, -0.0059, -1.6910]],

        [[-0.5305,  0.3733,  0.4158,  0.0045],
         [-0.2104,  0.1885,  0.0040,  0.1256],
         [-0.4915,  0.2189, -1.1648,  0.0640]]])
>>> x.size(0)
2
>>> x.size(1)
3
>>> x.size(2)
4
>>> x.size(-1) # つまり2次元目を指している(列数)
4
>>> torch.nn.MaxPool1d(x.size(-1))(x)
tensor([[[0.9807],
         [1.6502],
         [0.3572]],

        [[0.4158],
         [0.1885],
         [0.2189]]]) # 4列の中で一番大きい数字を取り出している
```

## Flatten

torch.nn.Flatten(start_dim: int = 1, end_dim: int = -1)

```python
>>> x
tensor([[[ 0.9807,  0.0329, -0.3041, -1.0789],
         [-0.1989,  0.5325,  0.9603,  1.6502],
         [-1.4203,  0.3572, -0.0059, -1.6910]],

        [[-0.5305,  0.3733,  0.4158,  0.0045],
         [-0.2104,  0.1885,  0.0040,  0.1256],
         [-0.4915,  0.2189, -1.1648,  0.0640]]])
>>> torch.nn.Flatten(1)(x) # 今回では1次元目と2次元目を一つにまとめてる
tensor([[ 0.9807,  0.0329, -0.3041, -1.0789, -0.1989,  0.5325,  0.9603,  1.6502,
         -1.4203,  0.3572, -0.0059, -1.6910],
        [-0.5305,  0.3733,  0.4158,  0.0045, -0.2104,  0.1885,  0.0040,  0.1256,
         -0.4915,  0.2189, -1.1648,  0.0640]])
```

## L0?L1?L2?ノルム???

あるベクトルの大きさのこと

`np.linalg.norm(input, ord:ノルム=2, axis:対象軸)`

### L0ノルム

0以外の値を持つ次元数(ざっくり)

### L1ノルム

各次元の絶対値の和(マンハッタン距離)

### L2ノルム

各次元の２乗した和の平方根(ユークリッド距離)


> 例えば、ベクトルノルムの場合はノルムの計算方法は
> 各要素の２乗和の平方根
> で算出しますが、ord=3 を指定すると
> 各要素の３乗和の３乗根 で算出されます。
> https://teratail.com/questions/73822


## Axis

https://deepage.net/features/numpy-axis.html

## nn.BatchNorm1d

torch.nn.BatchNorm1d(num_features, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)

```python
>>> input = torch.randn(10, 3)
>>> m = torch.nn.BatchNorm1d(3)
>>> output = m(input)
>>> output
tensor([[ 0.6273, -0.5331, -1.5502],
        [ 0.9353, -0.7351,  0.7647],
        [ 1.5150, -0.9468,  0.2170],
        [-0.5732, -1.5238, -0.3610],
        [-2.0045,  0.5869,  1.6986],
        [ 0.5672, -0.3396,  1.5211],
        [-0.2734,  0.6944, -0.2322],
        [ 0.1759,  1.6959, -0.3830],
        [ 0.2872, -0.3248, -0.9642],
        [-1.2569,  1.4260, -0.7109]], grad_fn=<NativeBatchNormBackward>)
```

## torch.matmul()

行列積を計算
matmul()とmm()/bmm()は同じ機能で、`@`演算子も使えるらしい

いろいろな行列積の計算方法
https://qiita.com/tand826/items/9e1b6a4de785097fe6a5

```python
>>> matrix = torch.randn(1, 3, 3)
>>> matrix
tensor([[[ 0.0466, -0.9606, -0.0769],
         [ 1.3093, -0.5168, -0.3116],
         [-1.0825,  0.4925, -0.0080]]])
>>> input = torch.randn(1, 10, 3)
>>> output = torch.matmul(input, matrix)
>>> output
tensor([[[ 0.1437,  1.2994, -0.1585],
         [ 2.5082, -0.0109, -0.5832],
         [-0.3461, -0.4641,  0.0111],
         [ 0.1358, -0.6681,  0.1967],
         [ 0.1357,  0.3903, -0.1306],
         [-2.7507,  1.8638,  0.1512],
         [ 2.5679, -0.2351, -0.1907],
         [ 0.2100,  0.0833,  0.2342],
         [-0.8640,  1.3497,  0.1551],
         [ 3.4258, -3.5129, -0.7188]]])
>>> output.size()
torch.Size([1, 10, 3])
>>> out = input@matrix
>>> out
tensor([[[ 0.1437,  1.2994, -0.1585],
         [ 2.5082, -0.0109, -0.5832],
         [-0.3461, -0.4641,  0.0111],
         [ 0.1358, -0.6681,  0.1967],
         [ 0.1357,  0.3903, -0.1306],
         [-2.7507,  1.8638,  0.1512],
         [ 2.5679, -0.2351, -0.1907],
         [ 0.2100,  0.0833,  0.2342],
         [-0.8640,  1.3497,  0.1551],
         [ 3.4258, -3.5129, -0.7188]]])
>>> input.bmm(matrix)
tensor([[[ 0.1437,  1.2994, -0.1585],
         [ 2.5082, -0.0109, -0.5832],
         [-0.3461, -0.4641,  0.0111],
         [ 0.1358, -0.6681,  0.1967],
         [ 0.1357,  0.3903, -0.1306],
         [-2.7507,  1.8638,  0.1512],
         [ 2.5679, -0.2351, -0.1907],
         [ 0.2100,  0.0833,  0.2342],
         [-0.8640,  1.3497,  0.1551],
         [ 3.4258, -3.5129, -0.7188]]])
```

## torch.cat()

テンソルの結合を行う

https://qiita.com/Haaamaaaaa/items/709d774698082e9d342d

```python
>>> input1 = torch.randn(2, 3, 4)
>>> input2 = torch.randn(3, 3, 4)
>>> input3 = torch.randn(5, 3, 4)
>>> inputlist = [input1, input2, input3]
>>> output = torch.cat(inputlist)
>>> output.size()
torch.Size([10, 3, 4])
```