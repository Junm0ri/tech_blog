---
title: 電子書籍をオーディオブックに変換する：abogenを試してみた
private: false
tags:
  - oss
  - ebook
  - audiobook
  - epub
updated_at: '2025-08-11T05:20:35.694Z'
id: null
organization_url_name: null
slide: false
---

## TL;DR
- [abogen](https://github.com/denizsafak/abogen)というOSSを試してみた
  - EPUB, PDFを音声化して、オーディオブックを作成するツール
- Linux/Windows/MacOS、 GPU/CPUの全環境で動作する
  - CPUでも50ページのデータを約5分で音声化できた
- 音声ファイルのままだと扱いにくいので、スマホからストリーミング再生できるともっと便利になりそう

## 概要

https://github.com/denizsafak/abogen

ふと、abogenというOSSがあることを知りました。
このツールを使うことで、EPUBやPDF形式の電子書籍を音声化し、オーディオブックを作成することができるとのことです。
[HumbleBundle](https://www.humblebundle.com)で購入した技術書が山のようにあったので、このツールを使うことで消化できるかもしれないと思い、試してみることにしました。

## 検証環境
Windows ミニPC([MINISFORUM UM790 Pro](https://amzn.asia/d/004252A))で検証しました。
詳細なスペックは以下。
- OS: Windows 11
- CPU: AMD Ryzen 9 7940HS (4.00GHz)
- メモリ: 64GB

## セットアップ
レポジトリのREADMEに従って、セットアップを行います。
いくつかの方法があるようですが、自分は `bat`ファイルを使用したインストールを行いました。

1. `git clone https://github.com/denizsafak/abogen.git`
2. クローンしたディレクトリに移動
3. `WINDOWS_INSTALL.bat`を実行

これだけで、必要な依存関係がインストールされ、`abogen`が使用可能になります。
（GitHubのOSSにあるまじきユーザーフレンドリーさ...ありがたい）

セットアップが完了すると、レポジトリ直下に`abogen`というショートカットが生成されます。
このショートカットをダブルクリックすることで、GUIアプリを起動できます。

![](https://raw.githubusercontent.com/Junm0ri/tech_blog/main/images/make-audiobook-from-ebook/abogen_shortcut.png)
*ショートカットが生成される*

![](https://raw.githubusercontent.com/Junm0ri/tech_blog/main/images/make-audiobook-from-ebook/abogen_startup.png)
*起動時の画面*

## 音声変換の実行
今回はOreillyの[AI Engineering](https://www.oreilly.com/library/view/ai-engineering/9781098166298/)のEPUBファイルを音声化してみることにしました。
ウィンドウ上部の領域に、音声化したいファイルをドラッグ＆ドロップすると、以下のような画面になります。

![](https://raw.githubusercontent.com/Junm0ri/tech_blog/main/images/make-audiobook-from-ebook/abogen_upload_epub.png)
*`aiengineering.epub`をドラッグ＆ドロップ後*

書籍の章立てが自動的に認識され、左側のリストに表示されます。
ここから、音声化したい章を選択した後、`OK`ボタンをクリックします。
今回はお試しということで `Chapter 1. Introduction to Building AI Applications with Foundation Models` (約50ページ)を選択しました。

`OK`ボタンをクリックすると、音声化の設定画面が表示されます。
今回は特に設定を変更せず、そのまま`Start`を押して音声化を始めました。

![](https://raw.githubusercontent.com/Junm0ri/tech_blog/main/images/make-audiobook-from-ebook/abogen_chapter_selected.png)
*章を選択後、音声化の設定画面に遷移*

音声化処理が始まると、進捗バーが表示され、処理の進行状況がわかります。
![](https://raw.githubusercontent.com/Junm0ri/tech_blog/main/images/make-audiobook-from-ebook/abogen_processing.png)
*音声化処理中の画面*

待っていると、そのうち処理が完了し、`.wav`ファイルと`.ass`ファイルが生成されます。
`.wav`ファイルは音声データ、`.ass`ファイルは字幕データです。
![](https://raw.githubusercontent.com/Junm0ri/tech_blog/main/images/make-audiobook-from-ebook/abogen_process_completed.png)
*音声化処理完了の画面*

今回は50ページのデータを音声化したところ、約5分で21分の音声データが生成されました。
AI Engineeringの書籍全体では535ページあるので、すべてを音声化しても1時間以内に収まりそうです。
音質や発音についても全く問題なく、聞き取りやすい音声が生成されました。
（音質について気になる方は[READMEのdemo](https://github.com/denizsafak/abogen?tab=readme-ov-file#demo)を確認してみてください）

## まとめと感想

[abogen](https://github.com/denizsafak/abogen?tab=readme-ov-file#demo)を使って、積読となっていた電子書籍を音声化することができました。
インストールや操作が非常に簡単で、エンジニアでなくても手軽に使えるツールだと思います。
また、音声化の速度も非常に速く、CPUのみでも十分なパフォーマンスを発揮しました。

ただ、音声ファイルのままだと普段の生活の中で自然に聞くのは難しいように思われます（Podcastとかの体験と比較してしまうと...）。
この課題を解決するための方法についても模索していきたいと思います。

### 余談
依存ソフトウェアをまるっとインストールする関係上、セットアップ後は2GB程度のディスク容量を消費します。
ストレージがカツカツな環境では注意が必要かもしれません。
