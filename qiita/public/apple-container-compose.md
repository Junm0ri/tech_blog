---
title: Apple Containerでcomposeを使いたい
private: false
tags:
  - apple
  - container
  - docker
  - compose
updated_at: '2025-08-03T02:42:13.185Z'
id: null
organization_url_name: null
slide: false
---

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
Appleは6月に開催されたWWDC2025にて、[Apple Containerization Frameworkを発表](https://developer.apple.com/videos/play/wwdc2025/346/)しました。
これにより、MacOS上でのLinuxコンテナ作成と実行がネイティブにサポートされるようになりました。

コンテナ技術のおさらいとして、Linuxコンテナを作成するためには入れ物としてのLinuxカーネル (≒Linux VM)が必要です。
そのため、MacOS上でLinuxコンテナを実行するためにはLinux VMを用意してくれるようなサードパーティ製ツール（例：Docker Desktop）を使用する必要がありました。
Apple Containerization Frameworkは、MacOS上でLinuxコンテナをネイティブに実行できるようにするフレームワークです。
Containerization Frameworkにより、MacOS上でLinuxコンテナを実行するためにDocker Desktopのようなサードパーティ製ツールに依存する必要がなくなります。

#### 補足：ContainerとContainerization
今回、Appleはこの技術を以下2つのOSSリポジトリとして公開しています。
1. [Apple/Containerization](https://github.com/apple/containerization)
   - Linux VMの作成やOCIイメージの管理などを行う基盤フレームワーク
2. [Apple/Container](https://github.com/apple/container)
   - コンテナの実行や管理を行うCLIツール
   - 上記 Containerizationを利用

基本的に`docker`コマンドに相当するのは後者の`container`となります。
そのため、以下では`container`に対するComposeサポートについて解説します。

:::note
完全に余談ですが、Appleは [Virtualization Framework](https://developer.apple.com/documentation/virtualization)という名前のVMフレームワークも提供しています。（Containerization Frameworkもこの技術を利用しています）
Virtualization Frameworkといい、Containerization Frameworkといい、やけに一般的な名前を付けるのがApple Styleなのでしょうか。
:::

### Apple Containerのメリット
Apple Containerは、Appleが公式に提供するコンテナツールであり、以下のような利点が期待されます。

1. MacOS上でLinuxコンテナをネイティブに実行できるため、Docker Desktopのようなサードパーティ製ツールに依存しない
   - 特に、Docker Desktopは企業の規模・収益によっては[有料プラン](https://www.docker.com/pricing/)が必要なため、コスト面でのメリットが大きい
2. Appleのハードウェアに最適化されているため、パフォーマンスが向上する
3. Appleのエコシステムに統合されているため、開発者にとって使いやすい

### 制約：Composeサポートの欠如
しかしながら、Apple Containerは現状、`compose.yml`を使用したコンテナオーケストレーションをサポートしていません。
近年の開発では、`compose.yml`を使用して複数のコンテナを一括で管理することが一般的であり、これがサポートされていないことは大きな制約となります。
本記事では、Apple Containerでの`compose.yml`のサポート状況と、代替手段について解説します。

## 本題：Composeサポートに関する現状とこれから
### 進行中：公式リポジトリによるサポート

Composeのサポートは利用者からも要望が多く、Apple Containerリポジトリにも以下のディスカッションがあります。
https://github.com/apple/container/discussions/194

このディスカッションから、[Mcrich23](https://github.com/Mcrich23)という開発者によって以下のPRが作成されています。
https://github.com/apple/container/pull/239

PRの作成が6月19日となっており、一か月以上経過していますが、現在も議論が進んでいます。
ただ、[メンテナの一人であるjgloganのコメント](https://github.com/apple/container/pull/239#issuecomment-3120201022)では、保守性を考慮してもっと小さい単位のPRに分割することが提案されています。
このPRがそのままマージされるにせよ、小さい単位のPRに分割されるにせよ、そう遠くないうちにComposeのサポートが実現することが期待されます。



### 代替手段：コミュニティによる自作OSS
前述のディスカッションの中で、何人かの個人開発者は自作OSSとしてComposeをサポートするためのCLIツールを実装・公開しています。
Apple Containerの公式サポートが実現されるまでの代替手段として利用することが出来るかもしれません。
（筆者はまだ未検証です。時間があれば試して記事にしようと思います）

https://github.com/noghartt/container-compose
https://github.com/youroldmangaming/Apple_Container_Compose
https://github.com/container-compose/cli
https://github.com/Mcrich23/Container-Compose


## 結論
Apple Containerは現状、`compose.yml`を使用したコンテナオーケストレーションをサポートしていません。
公式のComposeサポートは進行中であり、近い将来に実現されることが期待されます。
それまでの間、コミュニティによる自作ツールを利用することも可能ですが、公式サポートではないため注意が必要です。