---
title: 'Python: Function'
date: '2024-04-03'
private: true
summary: 'Pythonの関数。'
image:
  caption: 
  focal_point: ''
  placement: 1
  preview_only: true
---

### よく使う機能の関数化

前項に書いたPOSCARのファイル読み込みを、同じプログラム中で２回行う必要が出たらどうしますか？
２回くらいだったら、コピペで２回書けばいいやと思うかもしれません。
では、もしも１００回やる必要があったらどうでしょう。
普通に考えて、コピペですら面倒くさいし、絶対一回はミスするし非現実的ですよね。
こういった場合、「よく使う機能をひとまとめにする」、つまり関数化することで、何度も似たようなコードを書くことを回避できます。

```python
#関数の定義
def input_poscar(ifn): # ifn = input filename
    f = open(ifn, "r")
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

    return atn, spc, elname, eln, lv, e, x, y, z

# 以下がメインのコード
atn1, spc1, elname1, eln1, lv1, e1, x1, y1, z1 = input_poscar("POSCAR1.vasp")
atn2, spc2, elname2, eln2, lv2, e2, x2, y2, z2 = input_poscar("POSCAR2.vasp")
```

このように、defを使うことで関数を定義できます。
そして、関数を定義した後に、メインのコードの中で関数を呼び出すと、それがいつでも使えます。
上記のコードでは、２回関数を使っています。

重要なのは、関数を呼び出すときに何かしらの変数を渡して、別の処理をさせることができることです。
上記の例で言うと、input_pocsar("POSCAR1.vasp"), input_poscar("POSCAR2.vasp")と２回関数を呼び出していますが、この括弧の中の文字列が、def...で書いてあるifnという変数の中に突っ込まれます（引数といいます）。
このifnをf = open(ifn, "r")と読み込んでいるので、同じ関数なのに別のファイルの中身を読み込んでくれるという訳です。

そして、関数の最後のreturnというところで、関数の中で作り出した変数やリスト、「atn, spc, elname, eln, lv, e, x, y, z」をメインのコードに返しています。
その結果が、「atn1, spc1, elname1, eln1, lv1, e1, x1, y1, z1」の中に突っ込まれることになります。

一つ重要な注意点を言うと、関数の中で新たに定義した変数は、メインコードの中ではアクセスできません。
その結果が欲しいなら、returnで渡す必要があります。
また、メインのコードの中で定義した変数と同じ名前の変数を関数の中で作った場合、メインコードの変数とは「別のもの」として扱われます。
なので、メインのコードの変数を関数の中で使ったりいじったりしたいなら、引数として渡さないといけません（用語としてはグローバル変数とかローカル変数とか言いますが、言語によって扱い方も変わるので細かいことはググってください）。


{{% callout note %}}
変数の名前は後で見ても何を意味しているか理解できるように、長くなってもぱっと分かるようにする、あるいはコメントで何を意味しているか書いておきましょう。
{{% /callout %}}
