# Ckylark の文法モデル

## 概要

Ckylark は UTF-8 エンコードされた単語列であれば言語を問わず解析可能です。

ただし、ある言語を解析する場合は、適切な文法モデルを読み込む必要があります。

ここでは Ckylark 用の学習済みモデルファイルの配布と、ユーザ独自のモデルの作成方法を解説しています。

## モデルの使用方法

下記の学習済みモデルをダウンロード後、解凍して生成されるファイル群を適切なディレクトリ (`/path/to/Ckylark/model` など) に配置して下さい。

モデルの読み込みは `--model` オプションにモデルファイル群の接頭辞を指定します。

(WSJ モデルの場合の例)

```sh
$ tar xf wsj.tar.gz
$ mv wsj/* /path/to/Ckylark/model
$ /path/to/Ckylark/src/bin/ckylark \
    --model /path/to/Ckylark/model/wsj
```

## 学習済みモデル一覧

[Google Drive](https://drive.google.com/drive/folders/1GjUwp_xmoJuTnvh24w7FOYQs9nGjeNlU?usp=sharing) からダウンロードして下さい。

| モデル | 言語 | ドメイン | 学習データ | 備考 |
| :----- | :--- | :------- | :--------- | :--- |
| WSJ | 英語 | 新聞記事 | Penn Treebank Section 2-22 | デフォルトで付属。 |
| JDC | 日本語 | 新聞記事<br>書籍<br>Web<br>辞書例文<br>特許 | Japanese Word Dependency Corpus | デフォルトで付属。<br>単語分割には KyTea を使用する必要があります。 |
| CTB | 中国語 | | Penn Chinese Treebank | 単語分割には KyTea を使用する必要があります。<br>(KyTea 用の中国語モデルが公開されています) |

## ユーザ独自モデルの作成方法

もし Ckylark の利用者が独自のツリーバンクを持っている場合、Berkeley Parser の学習器で作成したモデルのテキスト出力を使用することができます。

```sh
$ java -cp /path/to/BerkeleyParser-*.jar \
    edu.berkeley.nlp.PCFGLA.GrammarTrainer \
        -path /path/to/my-treebank.mrg \
        -out /path/to/my-grammar.gr \
        -treebank SINGLEFILE
$ java -cp /path/to/BerkeleyParser-*.jar \
    edu.berkeley.nlp.PCFGLA.WriteGrammarToTextFile \
        /path/to/my-grammar.gr \
        /path/to/Ckylark/model/my-grammar
$ /path/to/Ckylark/src/bin/ckylark \
    --model /path/to/Ckylark/model/my-grammar
```
