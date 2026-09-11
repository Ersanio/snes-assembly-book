# はじめに

本書は、[SMW Central](https://www.smwcentral.net/)で公開している65C816の解説書のオンライン版です。
筆者は元々、「スーパーマリオワールドコミュニティに簡素な英語で65C816というアセンブリ言語を教える」という目的のために本書を執筆しました。
しかしながら今では、ROMハックコミュニティの多くの人びとが本書を読んでいます。
こうした経緯から筆者は、本書をGitHub上のオープンソースにすることとし、
これによって人々が本書を改良したり、あるいは翻訳したりすることを期待しています。

筆者はSMW Centralのメンバーである一方、本書はスーパーマリオワールドに特化したものではなく、また、スーパーマリオワールド専用の解説書でもありません。
本書は、あらゆるスーパーファミコンの文脈において使えるものとなっています。

## 65C816という言語

65C816は、スーパーファミコン\(SFC/SNES\)に搭載されているRicoh 5A22プロセッサで使用されているアセンブリ言語です。
「65C816」という名前について詳しく見てみましょう。
「816」は、プロセッサが8-bitモードと16-bitモードを切り替えられることを表しており、
「C」は、CMOSを表しています。
「65」は、このプロセッサが65xxプロセッサのファミリーであることを表しています。
このプロセッサは当時にしては非常に画期的なプロセッサでした。
本書ではニーモニック（命令・オペコードともいいます）を解説し、また、それらをどう使いこなすかについて解説します。
なお、ハードウェアレジスタといった、スーパーファミコンに特有の機能については紹介しません。

65C816を使いこなすことで、スーパーファミコンのゲームのためのコーディングができるようになります（例えば、スーパーマリオワールドに独自の機能を追加するなど）。
ところで、アセンブリ言語とは、いわば第2世代のプログラミング言語です。
すなわち、アセンブリ言語は、例えばC\#といったプログラミング言語と比較して低級な言語である、ということです。
つまり、アセンブリ言語とは人間が読める機械語であり、アセンブリ言語で書かれたコードは16進数で表される機械語に一対一対応します。
65C816においては全てのオペコードはアルファベット3文字で構成されており、それに様々な種類の述語が後置されます。

## 謝辞

下記の方々には、SMW Centralで公開した初版のレビューを行なっていただきました。この場を借りて感謝申し上げます：
**[spigmike](https://www.smwcentral.net/?p=profile&id=132), [Roy](https://www.smwcentral.net/?p=profile&id=845), [smkdan](https://www.smwcentral.net/?p=profile&id=411), [S.N.N](https://www.smwcentral.net/?p=profile&id=23), [andy\_k\_250](https://www.smwcentral.net/?p=profile&id=67), [Domiok](https://www.smwcentral.net/?p=profile&id=7211), [reghrhre](https://www.smwcentral.net/?p=profile&id=4176), [ChaoticFox](https://www.smwcentral.net/?p=profile&id=3462), [Tails\_155](https://www.smwcentral.net/?p=profile&id=6151), [GreenHammerBro](https://www.smwcentral.net/?p=profile&id=18802), [Vitor Vilela](https://www.smwcentral.net/?p=profile&id=8251)**

また、本レポジトリの[コントリビュータ](https://github.com/Ersanio/snes-assembly-book/graphs/contributors)の皆様にも感謝申し上げます。