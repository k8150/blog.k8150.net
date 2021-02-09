---
author: "koji haigo"
title: "git submoduleの削除について"
date: "2021-02-09"
description: ""
tags: ["git"]
---

## 経緯

Netlifyでホスティングしていたwebサイト(HUGO)がfailedしていた。  
エラーログを見たところ、原因は`git submodule`で追加していたリポジトリの対象コミットが削除されていたためだった。  
過去に`git submodule`で追加したリポジトリを削除して、再度リポジトリを追加する必要があったのでメモとして残す。  

## 手順

```sh
# 削除
$ git submodule deinit -f <対象のsubmodule>
$ rm -rf .git/modules/<対象のsubmodule>
$ git rm -f <対象のsubmodule>
# 追加(今までと変わらず)
git submodule add <対象のリポジトリ> <パス>
```

- (`git submodule`あまり使う機会がないのでコマンドすぐ忘れるなぁ)

## 参照

- [7.11 Git Tools - Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
