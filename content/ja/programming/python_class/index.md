---
title: 'Pythonのクラス'
date: '2024-04-04'
private: true
image:
  caption: 
  focal_point: ''
  placement: 1
  preview_only: true
---


### データや関数をひとまとめにした袋を作る（クラス）

以上までを手で体験した人なら感じていると思いますが、結晶構造の情報は「atn, spc, elname, eln, lv, e, x, y, z」のようにたくさんの変数とリストが必要で、煩雑で面倒臭いです。
一つだけの構造を読み込むならいいんですが、２つの構造が必要な場合、atn1, atn2のようにいちいちそれだけの変数を用意しなければならないです。
いくら関数を用意したところで、100個構造を読み込もうとすればatn1からatn100まで用意しないといけないです。
「POSCARごとにデータをひとまとめに出来れば分かりやすいのに...」と思いませんか？

そういう時に役立つのがクラスです。
誤解が生じることを承知の上で簡潔に言うと、クラスと言うのは変数や関数をひとまとめにしたデータの束（設計図）です。
以下のようにクラスを定義すれば、結晶構造の情報をひとまとめにできます。

```python
#クラスの定義
class Atom:
    def __init__(self):
        self.atn = 0
        self.spc = 0
        self.eln = [] 
        self.elname = []
        self.lv = [ [0.0, 0.0, 0.0] for i in range(3) ]
        self.e, self.x, self.y, self.z = [], [], [], []
 
#関数の定義
def input_poscar(ifn, at): # ifn = input filename
    f = open(ifn, "r")
    line = f.readline()
    line = f.readline()
    scale = float(line)
    
    for i in range(3):
        line = f.readline()
        vals = line.split()
        for j in range(3):
            at.lv[i][j] = float(vals[j]) * scale
     
    at.elname = f.readline().split()
    at.spc = len(at.elname)
     
    at.eln = [] # number of elementsのリスト
    vals = f.readline().split()
    at.eln = list(map(int, vals))
    at.atn = sum(at.eln) # number of atoms
     
    line = f.readline()
    for i in range(at.spc):
        for j in range(at.eln[i]):
            vals = f.readline().split()
            at.e.append(at.elname[i])
            at.x.append(float(vals[0]))
            at.y.append(float(vals[1]))
            at.z.append(float(vals[2]))
     
    f.close() 
 
# 以下がメインのコード
at1 = Atom()
at2 = Atom()
input_poscar("POSCAR1.vasp", at1) 
input_poscar("POSCAR2.vasp", at2)
print(at1.x)
print(at2.x)
```

クラスの定義は「class」という文言から始まります。
用語が増えてきてややこしいですが、、、クラスの中のdef initというのは、クラスが呼び出された時に最初に必ず実行される特殊な関数です。
この中に、ひとまとめにしたい変数を定義すれば良いです。
ただしルールがあり、self.というおまじないを変数の前に付けておいてください。
おまじないについては後で書きます。

続けて、メインのコードの中で、「at1 = Atom()」のように、定義した関数を呼び出しています。
このように書くと、結晶構造の設計図であるAtomクラスが、at1の中に呼び出されます。at1のように、設計図（クラス）に従って実体化されたもののことを、「インスタンス」と呼びます（より広義にはオブジェクト。聞いたことがあるかもしれません）。
at1 = Atom()を実行した瞬間に、at1というインスタンスが生成され、class中のinit関数も呼び出されて、さまざまな変数が自動的に定義されています。

このインスタンスの中の変数は、at1.xのように、インスタンス名の後にドットをつけて変数名を書くことによって、アクセスできます。
例えば、print(at1.x）とすると、a原子のx座標が出力されます。
このインスタンスを利用することによって、例えば一つ目のPOSCARの情報をat1, 二つ目のPOSCARの情報をat2に入れ、それぞれの情報にはat1.x, at2.xなどとしてアクセスすれば良くなります。
いちいち「atn1, spc1, elname1, eln1, lv1, e1, x1, y1, z1」, 「atn2, spc2, elname2, eln2, lv2, e2, x2, y2, z2」などと定義するよりは相当楽ですし、間違いが減ります。

そして、上記のコードでは、先ほど作ったPOSCARを読み込むinput_poscar関数に、at1, at2などのインスタンスを引数として渡すように変更をしています。
そして渡したインスタンスの中身を「at.lv\[i\]\[j\] = float(vals[j]) * scale」のように関数の中で直接書き換えるようにしています。
この書き方だと、関数の実行結果をいちいちreturnで返す必要もないです。
クラスを使ってデータをひとまとめにすること、そしてインスタンスごとデータを関数と受け渡しすることで、コードのかなり煩雑さが減るのが分かるでしょうか。

ちなみに、クラスを使いさえすれば、100個のPOSCARだろうが現実的に読み込めます。
POSCAR-001.vasp, ..., POSCAR-100.vaspというファイルがある場合、メインのコード部分を下記のように書き換えれば良いです。

```python
# 以下がメインのコード
at_list = [ Atom() for i in range(100) ]
for i in range(100):
    ifn = "POSCAR-%3d.vasp" % (i+1)
    input_poscar(ifn, at_list[i])
 
print(at_list[0].x)
```

Atomクラスのリストを作ってforループを組んだら終わりです。at_list[0].xで、1番目のPOSCARのx座標が見れます。
プログラミングを覚えると仕事が早くなる、というのが段々わかってきたでしょうか。
<br>
<br>

### 応用編
少し応用です。クラスの中には関数も定義することが出来ます。
input_poscarをクラスの中に組み込んだコードは以下の通りです。

```python
#クラスの定義
class Atom:
    def __init__(self):
        self.atn = 0
        self.spc = 0
        self.eln = []
        self.elname = []
        self.lv = [ [0.0, 0.0, 0.0] for i in range(3) ]
        self.e, self.x, self.y, self.z = [], [], [], []
     
    def input_poscar(self, ifn): # ifn = input filename
        f = open(ifn, "r")
        line = f.readline()
        line = f.readline()
        scale = float(line)
         
        for i in range(3):
            line = f.readline()
            vals = line.split()
            for j in range(3):
                self.lv[i][j] = float(vals[j]) * scale
        
        self.elname = f.readline().split()
        self.spc = len(self.elname)
         
        self.eln = [] # number of elementsのリスト
        vals = f.readline().split()
        self.eln = list(map(int, vals))
        self.atn = sum(self.eln) # number of atoms
         
        line = f.readline()
        for i in range(self.spc):
            for j in range(self.eln[i]):
                vals = f.readline().split()
                self.e.append(self.elname[i])
                self.x.append(float(vals[0]))
                self.y.append(float(vals[1]))
                self.z.append(float(vals[2]))
         
        f.close() 
 
# 以下がメインのコード
at1 = Atom()
at2 = Atom()
at1.input_poscar("POSCAR1.vasp") 
at2.input_poscar("POSCAR2.vasp")
print(at1.x)
print(at2.x)
```

違いがわかりますか？
ポイントは、クラスの中に出てくるself.というおまじないです。
上記の例の場合、クラス内の関数input_poscarの引数に(self, ifn)と書いてあります。
このselfというのは、インスタンス自身という意味です。
つまり、関数に自分自身を渡しています。
自分自身を渡しているので、self.とおまじないのついた変数には、全てアクセスできます。
クラス内で関数を定義すると、いちいちどの引数を渡すか考えなくても、インスタンスの中でおまじないのついた変数は全て共有しているので、複雑な変数のやりとりが必要な操作を簡略化できるというメリットがあります。

関数を含んだクラスは、使い方によってはとても便利です。
例えば「Car」というクラスを書いて、その中に「直進する」とか、「右に曲がる」とか、全ての車に必要な汎用的な関数（機能）を用意しておきます。
そして、self.maker, self.color, self.priceなど、製造メーカーや車の色、値段などの変数を用意しておきます。
Car(maker="A", color="blue", price="low")とすれば、A社製の青くて安い車が、
Car(maker="B", color="black", price="high")とすれば、B社製の黒くて高い車が出来ます（怒られますかね）。
クラスというのは汎用的な変数や機能を持つ設計図なわけです。
（もっと応用：実際には「右に曲がる」とかの基本的機能を会社ごとにチューニングしたり、車ごとに特別な機能を付加したりするわけですが、そういう操作もclassでやろうと思えば出来ます。「クラス　継承」とかでググってください。）

