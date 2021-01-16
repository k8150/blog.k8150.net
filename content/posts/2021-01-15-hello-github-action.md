---
author: "koji haigo"
title: "GitHub Actionsのチュートリアルをやってみた"
date: "2021-01-15"
description: ""
tags: ["GitHub", "GitHubActions"]
---

GitHubActionsのチュートリアルをやってみた。  
[GitHub Actions: Hello World](https://lab.github.com/githubtraining/github-actions:-hello-world)の`Start free course`ボタンを押下すると自分のリポジトリに`hello-github-actions`が作成される。  
PullRequestをするとbotが次の操作を指示してくれるので簡単に試せた。  
ソースコードは下記の通り。  

`action-a/Dockerfile`
```Dockerfile
FROM debian:9.5-slim

ADD entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
```

`action-a/action.yml`
```yml
name: "Hello Actions"
description: "Greet someone"
author: "octocat@github.com"

inputs:
  MY_NAME:
    description: "Who to greet"
    required: true
    default: "World"

runs:
  using: "docker"
  image: "Dockerfile"

branding:
  icon: "mic"
  color: "purple"
```

`action-a/entrypoint.sh`
```sh
#!/bin/sh -l

sh -c "echo Hello world my name is $INPUT_MY_NAME"
```

`.github/workflows/main.yml`
```yml
name: A workflow for my Hello World file
on: push # pushした際にActionを実行する

# 実行するjobを定義する
jobs:
  build:
    name: Hello world action
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - uses: ./action-a
        with:
          MY_NAME: "koji haigo" # 環境変数の設定
```

pushした際にActionが起動する。  
Actionコンソールに「Hello world my name is <MY_NAME>」が出力される。  

## 参考

- [GitHub Actions: Hello World](https://lab.github.com/githubtraining/github-actions:-hello-world)
- [docs.github.com](https://docs.github.com/ja/actions)
- [https://github.com/k8150/hello-github-actions](https://github.com/k8150/hello-github-actions)
