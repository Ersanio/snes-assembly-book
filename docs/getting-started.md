# 環境構築

## IDE

65C816に特化したIDE（統合開発環境）は存在しないため、メモ帳アプリやVSCodeといったどのようなASCIIエディタを用いても構いません。
しかし、既存のコードエディタには、様々な人びとが開発した様々なプラグインがあり、シンタックスハイライトといった機能を利用することができます。


* Josh Netaの"[65816 Assembly](https://marketplace.visualstudio.com/items?itemName=joshneta.65816-assembly)"（VSCode用プラグイン）
* Viceの"[65816 SNES Assembly Language Server](https://marketplace.visualstudio.com/items?itemName=vicerust.snes-asm)"（VSCode用プラグイン）
* lx5の"[Asar syntax highlight](https://github.com/TheLX5/AsarSyntaxHighlight-VSCode)"（VSCode用プラグイン）
* Telinc1の"[65c818 ASM syntax for highlight.js](https://github.com/telinc1/smwcentral-highlightjs-asar)"（Java Scriptプラグイン）

また、アセンブリファイルは、一般に拡張子".asm"で保存します。

## アセンブラ

本書では、Alcaroが開発し、その後SMW Centralコミュニティの多くのメンバーによってメンテナンスされている、
"Asar"と呼ばれるアセンブラで用いられているシンタックスを使用しています。
このアセンブラはSMW Centralで公開されており、[ここから](https://www.smwcentral.net/?p=section&a=details&id=19043)
ダウンロードできます。
また、AsarのGitHubレポジトリには[ここから](https://github.com/RPGHacker/asar)アクセスできます。
