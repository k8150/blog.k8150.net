---
author: "koji haigo"
title: "swiftumlでSwiftコードからUMLを生成する"
date: "2021-01-17"
description: ""
tags: ["Swift", "PlantUML", "swiftuml"]
---

Swiftコードからクラス図を自動作成したい。  
[swiftuml](https://github.com/palaniraja/swiftuml)というツールを見つけたので試してみた。

## 手順

```sh
# 実行準備
brew install sourcekitten
brew install jq
# swiftuml
git clone https://github.com/palaniraja/swiftuml.git
cd ./swiftuml
# 実行
find <任意Dir> | grep .swift | xargs -L 1 sh plantuml.sh | grep -v @enduml | grep -v @startuml >> output_`date '+%Y%m%d%H%M%S'`.puml
```

## 参考

- [https://github.com/palaniraja/swiftuml](https://github.com/palaniraja/swiftuml)
