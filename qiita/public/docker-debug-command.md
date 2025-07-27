---
title: 'Dockerコンテナに未インストールのコマンドを実行する : docker debug コマンド'
private: false
tags:
  - docker
  - dockerdesktop
  - debug
updated_at: '2025-07-27T03:40:17.197Z'
id: null
organization_url_name: null
slide: false
---

## TL;DR
- `slim`などの軽量イメージは、必要最低限のコマンドしかインストールされていない
- そのため、動作確認やデバッグのためのコマンド（例：`ping`）が使用できない
- そんな時は、`docker debug`コマンドを使うと、コンテナに未インストールのコマンドを実行できる

### 注意事項

`docker debug`コマンドはDocker Desktopの有料プラン(Pro/Team/Business) でしか利用できません。

## 背景

Dockerイメージを作成する際のベストプラクティスとして、「必要最小限の要素のみを含める」というものがあります。
「イメージサイズを小さくすることによるダウンロード時間の短縮」や「脆弱性が入り込むリスクを低減する」といったものが主な理由です。

一方で、動作確認をするときに、普段使用しているコマンドが使用できず、不便さを感じることも多々あります。
複数コンテナを使用したシステムで、「疎通ができているか確認しよう」と気軽に`ping`をしてエラーが発生すると辛いものがありますよね。
そんな時は、`docker debug`コマンドを使うと、コンテナに未インストールのコマンドを実行できます。


## `docker debug` コマンド

### 使用例

まずは `docker debug`がない場合を見てみましょう。
ここでは、軽量イメージとして[`hello-world`](https://hub.docker.com/_/hello-world)を使用します。
`hello-world`コンテナを起動して、シェルに接続してみます。

```bash
❯ docker run -it hello-world sh
docker: Error response from daemon: failed to create task for container: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: error during container init: exec: "sh": executable file not found in $PATH: unknown

Run 'docker run --help' for more information
```

はい、そもそも`sh`がインストールされていないので、エラーになります。

次に、`docker debug`コマンドを使って、`hello-world`コンテナにアクセスしてみます。
やり方は簡単で、 `docker debug {image_name}` というコマンドを実行するだけです。
（起動中のコンテナにアクセスする場合は、`docker debug {container_name}` というコマンドを実行します）

```bash
❯ docker debug hello-world
         ▄
     ▄ ▄ ▄  ▀▄▀
   ▄ ▄ ▄ ▄ ▄▇▀  █▀▄ █▀█ █▀▀ █▄▀ █▀▀ █▀█
  ▀████████▀    █▄▀ █▄█ █▄▄ █ █ ██▄ █▀▄
   ▀█████▀                        DEBUG

Builtin commands:
- install [tool1] [tool2] ...    Add Nix packages from: https://search.nixos.org/packages
- uninstall [tool1] [tool2] ...  Uninstall NixOS package(s).
- entrypoint                     Print/lint/run the entrypoint.
- builtins                       Show builtin commands.

Checks:
✓ distro:            unknown/scratch
✓ entrypoint linter: no errors (run 'entrypoint' for details)

Note: This is a sandbox shell. All changes will not affect the actual image.
                                                                                                                                       Version: 0.0.41
root@ec153840d1e6 / [hello-world:latest]
```

Dockerのロゴが表示され、デバッグ用のサンドボックスシェルが起動しました。
この後は、通常のシェルと同様にコマンドを実行できます。

```bash
docker > pwd
/
root@ec153840d1e6 / [hello-world:latest]

docker > ls
dev  etc  hello  nix  proc  sys
root@ec153840d1e6 / [hello-world:latest]

docker > ping google.com
PING google.com (142.251.42.142) 56(84) bytes of data.
64 bytes from nrt12s45-in-f14.1e100.net (142.251.42.142): icmp_seq=1 ttl=62 time=26.9 ms
64 bytes from nrt12s45-in-f14.1e100.net (142.251.42.142): icmp_seq=2 ttl=62 time=33.5 ms
...
root@ec153840d1e6 / [hello-world:latest]
```


### 追加のコマンドをインストールする

`docker debug`を使うと、コンテナに未インストールのコマンドを実行できます。
しかしながら、すべてのコマンドが事前に準備されている訳ではありません。
例として、`nslookup`はインストールされていないため、エラーになります。

```bash
docker > nslookup google.com
zsh: command not found: nslookup
```

このような場合は、`install`コマンドを使って、デバッグ環境にコマンドをインストールします。
使用したいコマンドが入っているパッケージを https://search.nixos.org/packages で検索し、`install`コマンドでインストールします。
今回使用したい`nslookup`は`bind`パッケージに含まれているようでしたのでこちらを指定します。

```bash
docker > install bind
Tip: You can install any package available at: https://search.nixos.org/packages.
installing 'bind-9.18.28'
building '/nix/store/d1cpn3yjl14aib5p82ig6wkk4plxjb8j-user-environment.drv'...

root@ec153840d1e6 / [hello-world:latest]
docker > nslookup google.com
Server:		192.168.65.7
Address:	192.168.65.7#53

Non-authoritative answer:
Name:	google.com
Address: 172.217.175.14
Name:	google.com
Address: 2404:6800:4004:825::200e
```

無事に`nslookup`コマンドが使えるようになりました。


## Reference
### 公式ドキュメント
https://docs.docker.com/reference/cli/docker/debug/


### 参考書籍
[Docker Deep Dive](https://amzn.asia/d/85evlt7)という書籍が、Dockerの全体像を理解するのに役立ちます。
Zero to Dockerとタイトルにあるように、まだDockerを使ったことがない人にも優しく、なおかつDockerの内部的な仕組みについても詳しく書かれています。
Dockerをより良く使いたい人、またその中身についても興味がある人にはおすすめです。
