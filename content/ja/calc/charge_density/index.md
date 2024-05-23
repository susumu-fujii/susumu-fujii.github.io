---
title: '電子の分布を見てみよう'
date: '2024-04-28'
private: true
---

### BaZrO3を例に
まず、energy/01_optimizationの中にあるCHGCARというファイルをVESTAで開いてみてください。
こうすると、どのように電子がユニットセルの中で分布しているかを見ることができます。
VESTAの上のメニューから、

Objects -> Properties -> Isosurfaces

を選択して、No.1 とかテーブルになってるところをクリックした後、Isosurface levelをいじってみてください。
Isosurfaceは等値面という意味です。ここでは電荷密度（どのくらい電子がいるか）の値を表しています。
色々値を変えながら眺めてみてください。

指示しといて何ですが、多分よくわかんないと思います。
なぜよく分かんないかというと、僕らの興味があるところは電荷密度自身がどうなっているかではなく、原子同士がどのように結合しているか、つまり「結合による電荷密度の変化」だからです。
例えば、孤立したOは元々電子を8個持っています。O<sup>2-</sup>になると10個持っています。
この時僕らが知りたいのは、Oが10個電子を持っているということではなくて、「Oが元の状態から余分に2個電子を持っている」ということです。

「何言ってんねんこいつ」と思ってると思いますが、とりあえず進めて下さい。
第一原理計算を使うと、孤立した原子の電子分布を出力することもできます。
これを出力して、結合による電荷密度の変化を見てみましょう。
コピーしたbasicの中に、electronic_structureというディレクトリがあります。
この中にある、01_charge_density/BaZrO3/normalに移動してください。
ここに、bazro3の計算結果を以下の通りコピーして、もう一度計算して下さい。

```bash
cp -r ../../../../energy/01_optimization/{go.sh,INCAR,POTCAR,CONTCAR,KPOINTS} .
mv CONTCAR POSCAR
qsub go.sh
```

終わったら、計算ファイルをicharg12というディレクトリにコピーして下さい。

```bash
cp go.sh INCAR POTCAR CONTCAR KPOINTS ../icharg12/
cd ../icharg12
mv CONTCAR POSCAR
```

その後、INCARを少しだけ編集します。

```bash
vim INCAR
```

以下の行を編集します。

```
NSW        =  300 ->
NSW        =  0

#ICHARG    =  2 ->
ICHARG    =  12    # コメントアウト（#）を消すのを忘れないように！そうしないとその行は無視されます！
```

修正し終わったら、qsub go.shで計算を回して下さい。
この設定にすることで、孤立した原子の電荷密度をただ重ね合わせただけの計算結果が出てきます。

終わったら、一つ前のディレクトリに戻って、以下のコマンドを実行して下さい。

```bash
perl ~/SCRIPT/vtstscripts-964/chgdiff.pl icharg12/CHGCAR normal/CHGCAR
```

こうすると、CHGCAR_diffというファイルが出力されるはずです。
これをVESTAで開いてみて下さい。

さっきと違って、黄色い部分と青い部分があると思います。
（原子を消したり、Isosurface levelを変えながら見てみて下さい。）
黄色い部分は電子が増えたところ、青い部分は電子が減ったところです。
金属原子の周りでは電子が減って、酸素の周りでは増えているはずです。
電子の分布が変化して、ちゃんとカチオンとアニオンになっていることが分かります。

Isosurface levelを低めにすると、球状に電荷密度が分布しているのが分かります。
原子同士の間で電子が増えていないので、概ねBaZrO3はイオン結合的です。
Isosurface levelを高めにすると、電荷密度に方向性があるのが分かると思います。
なので少しだけ共有結合的な要素もありそうです。
こういう原子・電子の挙動が微視的に観察できるのが第一原理計算の面白いところです。


### H2O分子と金属Alの場合

同じことをH2O分子とAlでもやってみましょう。
H2O/normalというディレクトリで、普通にH2Oの構造最適化が出来ます（POTCARは自分で作って下さい）。
終わったら、BaZrO3の時と同じ要領で入力ファイルをicharg12にコピーし、CONTCARをPOSCARにして、INCARを書き換えて計算を回して下さい。
その後、CHGCAR_diffを作ってVESTAで見てみて下さい。
BaZrO3よりは共有結合っぽい（電子が原子の間に存在している）のが分かると思います。
Alでも同じやり方で出来るようにしてます。
金属結合っぽい（電子が原子の間に広く分布して非局在化している）のが分かるはずです。
<br>

{{% callout note %}}
{{< spoiler text="ちなみに">}}
この記事の中で使ったchgdiff.plは、[the Hafner Research Group at the University of Vienna](https://theory.cm.utexas.edu/vtsttools/index.html)が作成の上、無償で公開しているperl言語スクリプトの一つです。他にもいろんなことが出来ます。
{{< /spoiler >}}
{{% /callout %}}
