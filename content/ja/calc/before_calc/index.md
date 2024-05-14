---
title: '計算を始める前に'
date: '2024-04-23'
private: true
---

### 計算機にログインしよう
研究室で保有している[計算機クラスター](/important/computers)がありますので、こちらを使って第一原理計算などの計算科学研究を行います。

計算機を操作する時には、ターミナルを使います。
ハッカーみたいな、黒い画面に文字だけ映っているやつです。
（文字だけでコンピュータとやりとりするインターフェースをCharacter User Interface; CUIとか、Command Line Interface; CLIとかいいます。普段皆さんが使っているのはGraphical User Interface; GUIです。）
最初はとっつきにくいんですが、慣れたらこっちの方が圧倒的に早いです。

ターミナルは、Macの場合、OS標準で入ってます。
これでもいいですが、個人的には[iTerm2](https://iterm2.com)というのが使いやすいので、インストールして使ってます。
Windowsの場合、Microsoft Storeからイントールできる[Windows Terminal](https://apps.microsoft.com/detail/9n0dx20hk701)が良さそうです。

ターミナルが使えるようになったら、
```bash
ssh USERNAME@IPADDRESS
```
と打ち込んでください。
（USERNAMEとIPADDRESSは一応隠しているので直接聞いてください。）
こうすると、「USERNAME」というユーザー名で、「IPADDRESS」というIPアドレスを持つマシンにログインできます。
ログインの時にパスワードを要求されますので、これも直接聞いてください。
これで計算機クラスターに入れたはずです。

{{< spoiler text="ちょっとマニアックな話">}}
計算機にいちいちWindowsやMacを入れていたらお金がかかってしょうがないので、普通はフリーのOSを使います。
Linuxというのがそれで、Linuxの中にも色んな種類のOSがあります（UbuntuとかDebianとかCent OSとか）。
martletにはCent OSがインストールされています。

MacとLinuxはUNIXという同じOSが元になっているので、親和性が高いです。
WindowsはMS-DOSなので作りが大分違います。
初心者だと使い心地はMacでもWindowsでもそんなに変わらないので、気にしなくていいです。
{{< /spoiler >}}
<br>
<br>

### bashに慣れよう
計算機クラスターに入ったら、bashという言語を通じて操作を行うことになります。
基本的なコマンドは以下の通りです。
順番に実行してみてください。
ちょくちょくlsというコマンドを実行すると何が起きているのか分かりやすいです。

```bash
cp -r basic your_name # your_nameは自分の名字に置き換えてください
cd your_name          # 
ls                    # 今いるディレクトリ（=フォルダ）の中身を表示する
mkdir AAA             # AAAというディレクトリを作成する
cd AAA                # change directory; AAAというディレクトリに移動する
cd ..                 # 一つ前のディレクトリに移動する
echo "test"           # testと出力する
echo "test" > BBB     # testをBBBというファイルに出力する
echo "test" >> BBB    # testをBBBというファイルに追記する
cat BBB               # BBBというファイルの中身を画面に出力する
less BBB              # BBBというファイルの中身を見る（qを押すと抜けられます）
man cat               # catコマンドのマニュアルを見る（qを押すと抜けられます）
cp BBB CCC            # BBBをCCCというファイル名でコピーする
cp -r AAA DDD         # AAAというディレクトリをDDDというディレクトリとしてコピーする
mv BBB AAA            # BBBをAAAというディレクトリの中に移動する
mv DDD EEE            # DDDをEEEという名前に変更する（BBBというディレクトリが存在しない場合、mvは名前変更のコマンドになる）
rm CCC                # CCCというファイルを消す
rm -r EEE             # EEEというディレクトリを消す
vim AAA/BBB           # AAAの中のBBBというファイルの中身を編集する（Esc, :, wq, Enterで抜けられます）
rm -r AAA             #
```

「bash　基本」とか「vim　使い方」とかgoogleで調べて遊んでみるのがいいと思います。
vimは使い方がややこしいんですが、慣れると相当早いので、頑張って慣れてください。
ちなみにコマンドやファイル名を入力する途中でタブキーを押すと、補完してくれたり、候補を画面に示したりしてくれます。

{{% callout warning %}}
ちなみにコマンドラインにゴミ箱は無く、rmで消したファイルやディレクトリは蘇りませんので注意してください。
バックアップ大事。
管理者権限で
```bash
rm -rF * # F=強制的に; *=そのディレクトリの中身全部
```
とかすると、本当に全てのファイルが消えます:loudly_crying_face:
{{% /callout %}}
