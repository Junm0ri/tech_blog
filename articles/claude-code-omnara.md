---
title: "Omnara: セットアップ（ほぼ）0で外出先からClaude Code"
emoji: "📱"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [ClaudeCode, OSS, VibeCoding]
published: true
---

## TL;DR
- omnaraというGitHubリポジトリの紹介
- 自宅PCで動作しているClaude Codeを外出先からスマホで操作できるようにする
- マネージドサービスを利用することが出来るため、セットアップが手軽に完了するのが魅力


## 概要
### 外出先からClaude Codeを利用したい
Claude Codeのヘビーユーザーの中には、「外出先からもClaude Codeを監視/利用したい」と思う方が少なくないようです。
そのようなニーズの実現方法として、下記の記事などがあります。

https://zenn.dev/fuku_tech/articles/bba079706955fd

上記の手法では Tailscale (VPN) と Termius (SSHクライアント)を利用して、自宅PCにリモートアクセスを行っています。
シンプルな構成ではありますが、VPNのセットアップやSSHクライアントの設定が必要で、ものぐさな身としては少し面倒に感じていました。

### もっと手軽な方法はないのか
今回は、より手軽に外出先からClaude Codeを利用する方法として、omnaraというGitHubリポジトリを紹介します。

https://github.com/omnara-ai/omnara

[omnara.com](https://omnara.com) でホストされているサーバーを経由することで、スマホなどからClaude Codeを利用できるようにするツールです。
外部サーバーを経由するためセットアップが非常に簡単で、すぐに利用を開始できるのが特徴です。^[ただし同時にセキュリティリスクは一定あり、個人開発向けという印象はあります]^[READMEを確認するとおそらくセルフホストも出来そう。その場合は結局VPNセットアップが必要ですが...]

基本的には以下の操作だけで利用を開始できます。

1. 自宅サーバーにomnara をインストール (`uv pip install omnara`)
2. omnara / Claude Code を起動 (`uv run omnara`)
3. Webダッシュボードから会員登録
   - Googleアカウントで登録可能
4. 操作端末からwebダッシュボードへログイン

以下で動作イメージについて簡単に紹介します。

## 動作イメージ

### 動作環境
- 自宅PC：
  - Windows 11 (WSL) ^[READMEではWindowsのセットアップ方法が記載されていますが、[依存ライブラリの問題でWindowsでは動きません](https://github.com/omnara-ai/omnara/issues/72)。WSLを使いましょう。]
  - uv / npm / node インストール済み

### 起動～操作

#### omnaraの起動（自宅サーバー）
まず最初に、自宅サーバーからomnaraおよびClaude Codeを起動します。
私はuvを利用してインストールしたので `uv run omnara`で起動します。
omnara起動後、ターミナルではいつもと同じClaude Codeのプロンプトが表示されます。
![](/images/omnara/boot-omnara.png)
*起動時の画面*

#### Webダッシュボードにアクセス（操作端末）
[omnara.com](https://omnara.com)にアクセスし、 `GetStarted for Free`ボタンをクリックすると、ログインポップアップが表示されます。
![](/images/omnara/login.png)
*Webログイン画面*

ログイン完了後、ダッシュボードが表示されます。
![](/images/omnara/dashboard.png)
*ログイン後に表示されるダッシュボード*
画面左側のサイドバーに、既存のセッションが表示されます。
セッション画面に移動すると、チャットベースのUIが表示されます。
この画面から、自宅サーバーで動作しているClaude Codeに対して操作/監視を行うことができます。

以下では検証のため、デスクトップ左にターミナル、右にブラウザを表示させて動作確認を行います。
今回は、適当なプロンプトとしてブラウザから「Web上で動作する簡単な電卓アプリを作成してください。」と入力してみました。
ブラウザからプロンプト送信後、ターミナルにもその内容が反映されます。
（逆に、ターミナルからプロンプト送信した場合もブラウザに反映されます）

![](/images/omnara/working.png)
*ウェブUIからプロンプトを送信。ターミナルにも反映される様子*

Claude Codeを利用している方にはおなじみの `Yes/ No`の入力選択もブラウザ上から行うことができます。

![](/images/omnara/question.png)
*ユーザー入力を待機している時は黄色枠で表示される*

スマートフォンから操作する場合も同様に、[omnara.com](https://omnara.com)にアクセスしてログインするだけで、Claude Codeを操作できます。
ChatGPTなどのサービスと同様のUI/UXとなっており、かなり使いやすいように感じます
![](/images/omnara/SP2.jpg =550x)
*スマホからアクセスした時の画面（切り抜き）*


## まとめと感想
omnaraを使うことで、外出先からでも手軽にClaude Codeを利用できるようになります。
VPNとSSHクライアントを組み合わせた方法と比べて、セットアップが非常に簡単で、すぐに利用を開始できるのが大きなメリットです。
一方、外部サーバーを経由するため、セキュリティリスクは一定あり、個人開発向けという印象はあります。
