---
title: Apple Containerでcomposeを使いたい
private: false
tags:
  - apple
  - container
  - docker
  - compose
updated_at: '2025-08-03T11:42:21+09:00'
id: 0782e39b248e5f640d6f
organization_url_name: null
slide: false
---

:::note
**追記（2026/11/1）**
Apple Container では、compose 機能はまだ直接的にはサポートされていません。
なお、本文で紹介している [Compose 導入の PR](https://github.com/apple/container/pull/239) は close されました。
ただし、`container machine` で立てた Linux VM の中で Docker Engine を動かせば、Docker Desktop なしで `docker compose` を使えます。
手順とスクリプトは、以下の記事にまとめました。

[さよなら Docker Desktop : Apple container machine を「Mac 版 WSL」にする](https://zenn.dev/junm0ri/articles/goodbye-docker-desktop-container-machine)
:::

## TL;DR
- [Apple Container](https://github.com/apple/container)では現状、`compose.yml`を使用したコンテナオーケストレーションはサポートされていない
- [Compose導入のPR](https://github.com/apple/container/pull/239)は現在進行中
- コミュニティによる自作ツールはいくつか存在する
  - 公式サポートではないため注意は必要

:::note
関連情報を不定期にウォッチ中です。
状況が変わり次第、この記事も更新していきます。
:::

## 背景

### Apple Containerization Frameworkとは？
Apple は 6 月に開催された WWDC2025 にて、[Apple Containerization Frameworkを発表](https://developer.apple.com/videos/play/wwdc2025/346/)しました。
これで、MacOS 上での Linux コンテナの作成と実行がネイティブでサポートされました。

コンテナ技術のおさらいとして、Linux コンテナを作成するためには入れ物としての Linux カーネル (≒Linux VM)が必要です。
そのため、MacOS 上で Linux コンテナを実行するためには Linux VM を用意してくれるようなサードパーティ製ツール（例：Docker Desktop）を使用する必要がありました。
Apple Containerization Framework は、MacOS 上で Linux コンテナをネイティブに実行できるようにするフレームワークです。
Containerization Framework により、MacOS 上で Linux コンテナを実行するために Docker Desktop のようなサードパーティ製ツールに依存する必要がなくなります。

#### 補足：ContainerとContainerization
今回、Apple はこの技術を以下 2 つの OSS リポジトリとして公開しています。
1. [Apple/Containerization](https://github.com/apple/containerization)
   - Linux VM の作成や OCI イメージの管理などを行う基盤フレームワーク
2. [Apple/Container](https://github.com/apple/container)
   - コンテナの実行や管理を行う CLI ツール
   - 上記 Containerization を利用

基本的に`docker`コマンドに相当するのは後者の`container`となります。
そのため、以下では`container`に対する Compose サポートについて解説します。

:::note
完全に余談ですが、Apple は VM フレームワークも提供しています。その名も [Virtualization Framework](https://developer.apple.com/documentation/virtualization) です。Containerization Framework も、この技術を利用しています。
Virtualization Framework といい、Containerization Framework といい、やけに一般的な名前を付けるのが Apple Style なのでしょうか。
:::

### Apple Containerのメリット
Apple Container は、Apple が公式に提供するコンテナツールであり、以下のような利点が期待されます。

1. MacOS 上で Linux コンテナをネイティブに実行できるため、Docker Desktop のようなサードパーティ製ツールに依存しない
   - 特に、Docker Desktop は企業の規模・収益によっては[有料プラン](https://www.docker.com/pricing/)が必要なため、コスト面でのメリットが大きい
2. Apple のハードウェアに最適化されているため、パフォーマンスが向上する
3. Apple のエコシステムに統合されているため、開発者にとって使いやすい

### 制約：Composeサポートの欠如
しかしながら、Apple Container は現状、`compose.yml`を使用したコンテナオーケストレーションをサポートしていません。
近年の開発では、`compose.yml`を使用して複数のコンテナを一括で管理することが一般的であり、これがサポートされていないことは大きな制約となります。
本記事では、Apple Container での`compose.yml`のサポート状況と、代替手段について解説します。

## 本題：Composeサポートに関する現状とこれから
### 進行中：公式リポジトリによるサポート

Compose のサポートは利用者からも要望が多く、Apple Container リポジトリにも以下のディスカッションがあります。
https://github.com/apple/container/discussions/194

このディスカッションから、[Mcrich23](https://github.com/Mcrich23)という開発者によって以下の PR が作成されています。
https://github.com/apple/container/pull/239

PR の作成が 6 月 19 日となっており、一か月以上経過していますが、現在も議論が進んでいます。
ただ、[メンテナの一人であるjgloganのコメント](https://github.com/apple/container/pull/239#issuecomment-3120201022)では、保守性を考慮してもっと小さい単位の PR に分割することが提案されています。
この PR がそのままマージされるか、小さい単位の PR へ分割されるかはさておき、そう遠くないうちに Compose のサポートは実現すると期待されます。



### 代替手段：コミュニティによる自作OSS
前述のディスカッションの中で、何人かの個人開発者は自作 OSS として Compose をサポートするための CLI ツールを実装・公開しています。
Apple Container の公式サポートが実現されるまでの代替手段として利用することが出来るかもしれません。
（筆者はまだ未検証です。時間があれば試して記事にしようと思います）

https://github.com/noghartt/container-compose
https://github.com/youroldmangaming/Apple_Container_Compose
https://github.com/container-compose/cli
https://github.com/Mcrich23/Container-Compose


## 結論
Apple Container は現状、`compose.yml`を使用したコンテナオーケストレーションをサポートしていません。
公式の Compose サポートは進行中であり、近い将来に実現されることが期待されます。
それまでの間、コミュニティによる自作ツールを利用することも可能ですが、公式サポートではないため注意が必要です。