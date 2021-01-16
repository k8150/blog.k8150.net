---
author: "koji haigo"
title: "VSCodeでPlantUMLのプレビュー環境を構築する"
date: "2021-01-16"
description: ""
tags: ["VSCode", "PlantUML"]
---

VSCodeでPlantUMLのプレビュー環境を構築する手順をまとめる。  
毎回忘れてしまうので。

## 手順

1. `Java`のインストール
1. `Graphviz`のインストール  
    ```sh
    # brew install
    brew install graphviz
    ```
1. VSCodeで`PlantUML`プラグインをインストール
1. `setting.json`に`"plantuml.java": "/usr/bin/java"`を追加

## ショートカット

- プレビュー表示：`alt`+`D`
- 画像のエクスポート：コマンドパレットに`PlantUML: Export Current Diagram`に入力してファイル形式を選択。

## TODO

- HUGOでPlantUML対応したい(パッと調べた感じなさそう)

## 参考

- [https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml](https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml)