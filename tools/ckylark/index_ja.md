# Ckylark - An Latent Annotated PCFG Parser

## 概要

Ckylark (すかいらーく) は自然言語のための構文解析器です。

Latent Annotated PCFG (LAPCFG) というモデルを使用して高精度に文の構造を推定します。

## インストール方法

### 必要なツール・ライブラリ

Ckylark のインストールには以下のツール・ライブラリが必要です。

- GCC-C++ 4.7 かそれ以降
- Boost C++ Libraries 1.49 かそれ以降
- autotools (configure の生成に使用)
- Python 3.2 かそれ以降

### 手順

Github 上の最新版 を入手します。
以下のコマンドを実行します。

```sh
$ cd /path/to/Ckylark
$ autoreconf -i
$ ./configure
$ make
$ (sudo) make install
```

## 完全な文の構文解析

Ckylark が解析できるのは 句構造 と呼ばれる構文規則です。

入力テキストを 1 行ずつ解析し、Penn Treebank 準拠の S 式 の形式で構文木を出力します。

英語の文を解析する場合、次のように使用します。

```sh
$ src/bin/ckylark --model wsj < (corpus) > (output)
```

(簡単な解析例)

```sh
$ echo "This is a pen ." | src/bin/ckylark --model wsj
( (S (NP (DT This)) (VP (VBZ is) (NP (DT a) (NN pen))) (. .)) )
```

コーパスは予め半角スペース文字で単語分割されている必要があります。

また、コーパスの内容は UTF-8 でエンコーディングされている必要があります (他エンコーディングへの対応は今のところ未定)

解析結果を視覚的に分かりやすく表示するには、 script/show_parse.py を使用します。

```sh
$ echo "This is a pen ." | src/bin/ckylark --model wsj | script/show_parse.py
(
    (S
        (NP
            (DT This))
        (VP
            (VBZ is)
            (NP
                (DT a)
                (NN pen)))
        (. .)))
```

## 不完全な文の構文解析 (`--partial`)

文中の一部の単語が分からない場合、例えば「ここに名詞句が入るが、それが何か分からない・限定したくない」ような場合でも、文法タグを指定して解析することができます。

これによって、途中で分断されてしまったような不完全な文でも、分断箇所の文法タグさえ分かっていれば正しい解析を行うことができます。

文法タグは `[FOO]` のように記述します。

```sh
$ echo "[NP] is a pen ." | src/bin/ckylark --model wsj --partial
( (S (NP [NP]) (VP (VBZ is) (NP (DT a) (NN pen))) (. .)) )
$ echo "[NP] [VBZ] [IN] [NP]" | src/bin/ckylark --model wsj --partial
( (S (NP [NP]) (VP (VBZ [VBZ]) (PP (IN [IN]) (NP [NP])))) )
```

## 品詞推定 (`--output-format postag`)

単語分割済みのコーパスに対する品詞推定だけ行うこともできます。

```sh
$ echo "This is a pen ." | src/bin/ckylark --model wsj --output-format postag
This/DT is/VBZ a/DT pen/NN ./.
```

## 文法モデル・単語分割について

構文解析には言語・ドメインごとに適切な文法モデルを使用する必要があります。

デフォルトでは英語と日本語の文法モデル (WSJ 及び JDC) が付属しています。
その他の言語・ドメインについては下記リンク先で公開しているものを使用可能です。
また、ユーザが独自に文法モデルを作成することも可能です。

- [Ckylark の文法モデル](/tools.ckylark/model_ja)

英語の単語分割には Stanford Tokenizer などを使用することができます。
日本語の単語分割には KyTea を使用して下さい (JDC モデルが KyTea 準拠になっています)。

## 開発者

- 小田 悠介 (@odashi_t) - だいたい全部
- 赤部 晃一 (@vbkaisetsu) - Linux 向けインターフェイスなど
- Graham Neubig (@neubig) - 付属モデル
- 奈良先端科学技術大学院大学 知能コミュニケーション研究室

## 関連論文

- Yusuke Oda, Graham Neubig, Sakriani Sakti, Tomoki Toda, and Satoshi Nakamura. Ckylark: A More Robust PCFG-LA Parser. In Proceedings of the 2015 Conference of the North American Chapter of the Association for Computational Linguistics - Human Language Technologies (NAACL-HLT) (Software Demonstrations), Denver, USA, 2015.
- 小田 悠介, Graham Neubig, 波多腰 優斗, Sakriani Sakti, 戸田 智基, and 中村 哲. 解析失敗の発生しにくいPCFG-LA句構造構文解析. In 言語処理学会 第21回年次大会 講演論文集, 京都, 3月, 2015.

## 更新履歴
- 2020/08/11 - GitHub Pages に移管
- 2015/03/22 - (rev.135) 使い方の修正、参考文献の追加
- 2015/02/20 - (rev.132) いくつかのバグ修正、参考文献の追加
- 2014/11/11 - (rev.109) 品詞推定機能の追加
- 2014/10/31 - (rev.103) show_parse.pyの追加
- 2014/10/29 - (rev.100) ページ公開
