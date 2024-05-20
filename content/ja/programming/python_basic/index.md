---
title: 'Pythonの基本の基本'
date: '2024-04-02'
private: true
image:
  caption: 
  focal_point: ''
  placement: 1
  preview_only: true
---

### 基本の基本

Anacondaの何かしらのソフトを起動するか、python (or python3) と計算機クラスターのターミナルで打って、以下を入力してみてください。
```python
a = 1         # pythonは整数と認識します。
a = 1.0       # pythonは実数と認識します。（もしくはa = 1.）
a = "1"       # pythonは文字列と認識します。（もしくはa = '1'）
a = 1 + 2     # pythonは整数の1と2を足します。
a += 2        # pythonはaに2を足します。
a = [1, 2, 3] # aの中に整数が３つ入ります（リストと言います）。
              # a[0]とすると、リストの中の要素が取り出せます（この場合１が出てきます）。
a = [ [1, 2, 3], [4, 5, 6] ] # このように、リストを入れ子に出来ます。
                             # a[1][0]とすると、要素が取り出せます（この場合４が出てきます）。
a = {"H":1, "He":2, "Li":3}  # 辞書も作れます。
                             # a["H"]とすると、辞書の要素が取り出せます。

#forなどのあとは、スペースやタブでインデントをつけないといけません。
a = [0, 0, 0]
for i in range(3):
    a[i] = i  #（forループの中で実行）
    a[i] *= 2 #（forループの中で実行）
a[0] *= 2     #（インデントが付いてないので、forループから抜け出している）
print(a)
```
<br>

### ファイル読み込み
基本的にプログラミングの初心者がやりたいことは、平均をとるとか、標準偏差をとるとか、pythonでも一瞬で終わる作業が多いです。
それ自体は割と簡単ですが、その前にファイルに書いてある数値を読み取らねばなりません。
ここでは例をとって、以下のようなVASPの[POSCAR](POSCAR)を読み込んでみましょう。

{{% callout note %}}
**POSCARが分からない人へ**<br>
結晶構造のデータが入ったファイルです。[VESTA](https://jp-minerals.org/vesta/jp/)というソフトで可視化できるので、まだやったことがなければそちらを先にやってみてください。
{{% /callout %}}

```
Ba1 Zr1 O3
1.0
4.193200 0.000000 0.000000
0.000000 4.193200 0.000000
0.000000 0.000000 4.193200
Ba Zr O
1 1 3
direct
0.500000 0.500000 0.500000
0.000000 0.000000 0.000000
0.000000 0.000000 0.500000
0.000000 0.500000 0.000000
0.500000 0.000000 0.000000
```

ファイルの何を読み込めばいいか考えてみてください。
具体的に書くと必要なのは下記の通りです。

```bash
 Ba1 Zr1 O3 #（ファイルのタイトル; いらん）
 1.0        #（格子ベクトルの係数）
 4.193200 0.000000 0.000000 #（a軸）
 0.000000 4.193200 0.000000 #（b軸）
 0.000000 0.000000 4.193200 #（c軸）
 Ba Zr O #（元素名）
 1 1 3   #（各元素の数）
 direct  #（いらん）
 0.500000 0.500000 0.500000 #（１個目の原子の相対座標、以下同）
 0.000000 0.000000 0.000000
 0.000000 0.000000 0.500000
 0.000000 0.500000 0.000000
 0.500000 0.000000 0.000000
```

いろんな読み込み方がありますが、、、とりあえず初心者向けは下記の通りです。

```python
f = open("POSCAR", "r")
line = f.readline()
line = f.readline()
scale = float(line)

lv = [ [0.0, 0.0, 0.0] for i in range(3) ]
for i in range(3):
    line = f.readline()
    vals = line.split()
    for j in range(3):
        lv[i][j] = float(vals[j]) * scale

elname = f.readline().split()
spc = len(elname)

eln = [] # number of elementsのリスト
vals = f.readline().split()
eln = list(map(int, vals))
atn = sum(eln) # number of atoms

e, x, y, z = [], [], [], [] # 元素名, x, y, z座標のリスト
line = f.readline()
for i in range(spc):
    for j in range(eln[i]):
        vals = f.readline().split()
        e.append(elname[i])
        x.append(float(vals[0]))
        y.append(float(vals[1]))
        z.append(float(vals[2]))

f.close()
```

一行ずつ打って、適当にprint(lv)とかして確認しながら進めてみてください。以下に各コマンドの説明をします。

```python
f = open("POSCAR", "r")
```
これでfという変数でPOSCARを開きます。そして、

```python
line = f.readline()
```
とすると、一行目の文字列がlineの中にぶち込まれます。
print(line)を使えば中身が見れます。

```python
print(line)
Ba1 Zr1 O3\n # (\nは改行です）
```

一行目はいらんので無視して、二行目を読みます。

```python
line = f.readline()
print(line)
1.0\n
```

この値は欲しいので、実数に変換して保存しときます。

```
scale = float(line)
```

次に、格子定数を読み込みます。
3, 4, 5行目がそれぞれa, b, c軸のベクトルです。

```python
line = f.readline()
print(line)
4.193200 0.000000 0.000000\n
```

ここで困るのが、lineという変数には、"4.193200 0.000000 0.000000\n"という文字列が入っているということです。
スペース（もしくはタブ）で区切って、それぞれを変数にしないといけません。
それは以下の操作でできます。

```python
vals = line.split()
print(vals)
['4.193200', '0.000000', '0.000000']
```

ただしvalsというリストの中身のそれぞれは、数字ではなく文字列です。
実数に変換して、さっき取得した係数（scale）をかけないといけません。

```python
a = [ 0., 0., 0.] #（初期値を代入）
a[0] = float(vals[0]) * scale
a[1] = float(vals[1]) * scale
a[2] = float(vals[2]) * scale
print(a)
[4.1932, 0.0, 0.0]
```
これでa軸が読み取れました。
勘のいい人は気づいたと思いますが、これはforループでもっと簡潔に書けます。

```python
a = [ 0., 0., 0.]（初期値を代入）
for i in range(3):
    a[i] = float(vals[i]) * scale
```

range(3)というのは、0から始まって1ずつ値が増えるリストを返してくれて、この場合[0, 1, 2]が生成されます。これらがiの中に逐次的に入れられて３回分のループが実行されます。
なのでfor i in range(3)というのはfor i in [0, 1, 2]と一緒です。
もっと勘のいい人は気づいたと思いますが、格子ベクトルを入れ子にしてリストにまとめれば、もっと綺麗に書けます。

```python
lv = [ [0.0, 0.0, 0.0] for i in range(3) ] #lattice vector, [0.0, 0.0, 0.0]というリストをforループで3個作る時の書き方
print(lv)
[[0.0, 0.0, 0.0], [0.0, 0.0, 0.0], [0.0, 0.0, 0.0]] #（a軸、b軸、c軸のリストが入ったリスト）

for i in range(3):
    line = f.readline()
    vals = line.split()
    for j in range(3):
        lv[i][j] = float(vals[j]) * scale

print(lv)
[[4.1932, 0.0, 0.0], [0.0, 4.1932, 0.0], [0.0, 0.0, 4.1932]]
```

これで、格子定数が読み込めました。
次に元素名の情報を手に入れます。

```python
elname = f.readline().split() #（line = f.readline(), vals = line.split()をまとめた書き方）
print(elname)
['Ba', 'Zr', 'O']
spc = len(elname) # 元素種の数 = elnameの要素の数 = len(elname)
```

元素の情報は文字なので、何も加工しなくていいです。
次に元素ごとの数を読み込みます。

```python
eln = [] # number of elementsのリスト
vals = f.readline().split()
eln = list(map(int, vals))
print(eln)
[1, 1, 3]
atn = sum(eln) # number of atoms
```

ちょいちょいテクニックを小出しにしてますが、mapはある関数をリストの各要素に作用させる関数で、valsの中身を全部整数化してから、elnに突っ込んでいます。
何言っとんねんこいつと思ったら、普通にeln[0] = int(vals[0])とかすればいいです。
最後に、各原子の座標を読み込みます。ついでに、元素名のリストも作っておきます。

```python
e, x, y, z = [], [], [], [] #元素名、x,y,z座標のリスト
line = f.readline()
for i in range(spc):        #元素の種類分ループ
    for j in range(eln[i]): #各元素の原子数分ループ
        vals = f.readline().split()
        e.append(elname[i])
        x.append(float(vals[0]))
        y.append(float(vals[1]))
        z.append(float(vals[2]))

print(e)
['Ba', 'Zr', 'O', 'O', 'O']
print(x)
[0.5, 0.0, 0.0, 0.0, 0.5]
print(y)
[0.5, 0.0, 0.0, 0.5, 0.0]
print(z)
[0.5, 0.0, 0.5, 0.0, 0.0]
```

appendはリストの末尾に()内のものを足すコマンドです。
これで、格子定数、元素の種類、各元素の個数、各原子の座標という、欲しい情報は全て得られました。
読み取りが終わったら、開いていたファイルを閉じます。

```python
f.close()
```

ついでなので、各元素が何番目の原子なのか、インデックスでも作ってみましょう。

```python
eli = [ [] for i in range(spc) ] # 元素のインデックスのリスト
for i in range(atn):
    for j in range(spc):
        if e[i] == elname[j]:
            eli[j].append(i)
            break

print(eli)
[[0], [1], [2, 3, 4]]
```

これで、Baが0番目の原子、Zrが1番目の原子、Oが2,3,4番目の原子というインデックスが作れました。
これは例えば、カチオンと酸素との距離を測る時などに役立ちます。

データを読み込めたら、基本的にはネットで便利な関数を調べて、足し算とか合計とか平均とか標準偏差とか適当に計算したらいいです。
例えば数値計算で便利なnumpyというライブラリがあり、

```
import numpy as np # numpyというライブラリをnpという名前で読み込む
np.average(x)
np.std(x)
```

とすれば、x座標の平均や標準偏差が出せます（なんの意味もないけど）。
なければ適当に調べてインストールしてください。
ちなみに平均や標準偏差はまじめにべた書きすると10行くらいはかかります。

```python
import math
sum = 0.0
for i in range(atn):
    sum += x[i]

avg = sum / float(atn)
sum = 0.0
for i in range(atn):
    sum += (x[i] - avg) * (x[i] -avg)

variance = sum / atn;
std = math.sqrt(variance)
```

もっと複雑な機能がpythonのライブラリにはあるので、重宝されています。

現実的に使えそうなことを書くと、例えばx[0] += 0.1とすれば、Baの原子の位置をちょっとずらしたモデルが作れます。
Baを動かしたら周りの原子にどれくらい力がかかるんだろうとか、どれくらいエネルギーが上がるんだろうとか、計算すれば分かります。
実際、こういう微視的な情報から材料の物性が分かります。
