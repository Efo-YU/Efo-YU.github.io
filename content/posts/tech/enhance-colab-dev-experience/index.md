---
title: "Colabでの開発体験をいい感じにする小ネタ集"
date: 2025-12-15T00:20:25+09:00
# tags: [""]
---

# VSCode 上のコードを Colab で実行する

VSCode には Google 公式 Colab 拡張機能がある：

- [拡張機能](https://marketplace.visualstudio.com/items?itemName=Google.colab)
- [公式ブログの記事](https://developers.googleblog.com/en/google-colab-is-coming-to-vs-code/)

この拡張機能を使うことで VSCode 上から Colab のランタイムに直接接続し，コードを実行できる．

これにより，VSCode でコーディングし，Colab の計算リソースを使って実行する，というワークフローをとれる．

なお，Google Drive のデスクトップアプリをインストールしておけば，Drive 上のファイルを VSCode で直接開ける．

この状態で Colab のランタイムを接続すれば，Drive 上のファイルをそのまま編集し，Colab 環境で実行できることになる．

# GitHub 上のコードを Colab で開く

Colab はもともと，GitHub 上の public repo から直接ノートブックを開く機能を備えている．

- [公式解説](https://github.com/googlecolab/colabtools/blob/master/notebooks/colab-github-demo.ipynb)

GitHub public repo への URL を`github.com`→`colab.research.google.com/github`と置換するだけでよい．
