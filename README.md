# Android OSSプロジェクトのベストプラクティス

これは[OSS開発のリテラシー / Android編](https://paper.dropbox.com/doc/OSS-Android--ASA0GNW7rKNcc2UFqytnBbE3AQ-3Zp3zkvV2mJFF8gcpNyO5) として
[potatotips #56, 2018/11/15 in SmartNews](https://potatotips.connpass.com/event/104242/) で発表したものを改編したものです。

## Table of Contents

<!-- TOC depthFrom:2 anchorMode:github.com -->

- [Table of Contents](#table-of-contents)
- [この文書について](#この文書について)
  - [リポジトリのホスティング](#リポジトリのホスティング)
  - [ベストプラクティス実践事例](#ベストプラクティス実践事例)
- [[MUST] OSSライセンスを設定する](#must-ossライセンスを設定する)
  - [補足: OSSライブラリを使うときにはまずライセンスを確認すべき](#補足-ossライブラリを使うときにはまずライセンスを確認すべき)
- [[MUST] READMEを置く](#must-readmeを置く)
  - [READMEについての規格 "Standard Readme"](#readmeについての規格-standard-readme)
- [[MUST] リリースしたら git tag を打ってpushする](#must-リリースしたら-git-tag-を打ってpushする)
- [[MUST] CI を設定してバッヂをREADMEに置く](#must-ci-を設定してバッヂをreadmeに置く)
- [[MUST] アーティファクトのgroupIdはライブラリ固有のもににする](#must-アーティファクトのgroupidはライブラリ固有のもににする)
- [[MUST] bintrayに成果物を公開するときはライブラリごとにorgをつくる](#must-bintrayに成果物を公開するときはライブラリごとにorgをつくる)
- [[SHOULD] Android Studioでプロジェクトをつくる](#should-android-studioでプロジェクトをつくる)
- [[SHOULD] テストを書く](#should-テストを書く)
- [[SHOULD] ChangeLogファイルを置く](#should-changelogファイルを置く)
  - [Changelogの書き方についての緒論](#changelogの書き方についての緒論)
- [[SHOULD] リリースエンジニアリングをコード化する](#should-リリースエンジニアリングをコード化する)
- [License](#license)

<!-- /TOC -->

## この文書について

- AndroidプロジェクトをOSSとして開発するにあたってベストプラクティスをまとめた
- あくまでも「プロジェクトの構成」についてまとめたもの
  - gfxの個人的な経験に基づく
  - コードについては触れていない
- Androidに依存することも、OSSに一般的なことも混在しているが、いずれも利用者としてあると嬉しいという基準で選定した
- 紹介するベストプラクティスは MUST / SHOULD で分類した
  - **MUST** は「これが満たされてないとプロダクションで使用するのは難しい」というレベル
  - **SHOULD** は「できれば満たしてほしい」というレベル

### リポジトリのホスティング

* この文書は何らかのリポジトリホスティングサービスを利用することを想定している
  * GitHub, GItLab, BitBucketなど
* 特定のホスティングサービスに依存することはこの文書では触れない

### ベストプラクティス実践事例

https://github.com/maskarade/Android-Orma

- 2015年から [gfx](https://github.com/gfx/) がプロダクトオーナーとしてメンテナンスしているAndroidのORM
- Ormaの開発で得たベストプラクティスが多いので、実践事例として紹介する

## [MUST] OSSライセンスを設定する
- OSSライセンス（OSIが「OSSライセンスである」と規定したライセンス）の設定されていないソフトウェアはOSSとはいえないので、これはベストプラクティスというよりはOSSの定義そのものではある
- ライセンスは README にセクションを設けて一言触れ、さらにLICENSE ファイルを用意すべき
  - 各々のソースコードの冒頭でライセンスを書くのは、できればやったほうがよい
    - これは「やったほうがよい」程度という認識
- Android界隈だと使われるライセンスはApache 2.0 License, MIT あたりがメジャーか


### 補足: OSSライブラリを使うときにはまずライセンスを確認すべき

- ライセンスのない野良コードを製品に組み込むのはNG（訴訟リスクあり）
- GPL系も使うのは難しい
  - 条件次第では可能だったりはするが…（アプリ自体をOSSにするなど）


## [MUST] READMEを置く

- 書くべきもの:
  - [MUST] ソフトウェアの名前
  - [SHOULD] ToC (エディタのpluginで自動生成すると便利)
  - [MUST] シンプルな使い方（ “synopsis” )
  - [MUST] インストール方法
  - [SHOULD] APIリファレンス（synopsisだけで済む場合は不要）
  - [SHOULD] 開発のしかた（コントリビューションガイド）
  - [SHOULD] リリースエンジニアリング（releng）
  - [MUST] 作者の情報（連絡方法も含めて）
  - [MUST] ライセンス
- Q「コントリビューションガイドは何を書けばいいか」A「PRを送るのに困らないようにするための開発のための解説でよいかと。開発環境とか」

例: https://github.com/maskarade/Android-Orma

### READMEについての規格 "Standard Readme"

https://github.com/RichardLitt/standard-readme

* 必ずしも広まっているとはいえないものの、こういう規格もある
* > Standard Readme is designed for open source libraries. Although it’s historically made for Node and npm projects, it also applies to libraries in other languages and package managers.
  * "npm用に開発された規格だが、他のOSSライブラリにも適用できる" とのこと
* 個人的には重厚すぎて完全に従うのは面倒くさい
  * とはいえ見るべきものはあるので一読をおすすめしたい

## [MUST] リリースしたら git tag を打ってpushする

- 手でやってると忘れるのでコード化するとよい
- 言語によっては自動的にやってくれたりする (e.g. `rake release` for ruby gems)

## [MUST] CI を設定してバッヂをREADMEに置く

- テストがなくてもCIの設定はすべき
- CIの設定こそ開発環境やビルドの方法をコード化したものだからである
- テストがある場合にはもちろんテストを実行する
- 余裕があればカバレッジもとってバッジ化する（coverallsなどで可能）


## [MUST] アーティファクトのgroupIdはライブラリ固有のもににする

- Ormaのように複数のアーティファクトからなるライブラリでgroupIdをたとえば `com.github.gfx` みたいにしていたら「Ormaライブラリ群」を識別できない
- なおartifactIdにも `orma` や `orma-processor` などライブラリ名を含めるほうがよい
  - artifactIdはしばしばファイル名として使われるので `library` みたいな名前だと他のものとパッと見てわからなくなりがち
- Q「小さなライブラリなら `com.github.gfx` でもよいのでは」⇢ A「将来的にアーティファクトを分割したときに困るし、ルールはシンプルなほうがいいいので常に groupId をライブラリ固有のものにするのをおすすめする」

## [MUST] bintrayに成果物を公開するときはライブラリごとにorgをつくる

- 理由のひとつは、groupId同様に「ライブラリ群」を識別しやすくするため
- 理由のもうひとつは、公開権限を複数人にするにはorgにするしかないため
  - Ormaは最初個人アカウントに公開していたが、最近メンテナが増えたのでorg管理に変えた
    - このorgへの移行のせいでgroupIdを変更しなければならなかった…
- 余談: bintrayにアップロードするpluginは、 `bintray-release` はメンテが滞りがちなので、bintray公式の`gradle-bintray-plugin`を使うほうがよい

## [SHOULD] Android Studioでプロジェクトをつくる

- Android Studioで作ったプロジェクト構成やbuild.gradleが “Android Standard” といえるので、Android Standardに沿ってないプロダクトをたまにみると面食らう
- `gradlew` とその関連ファイルもrepoにいれるべき
  - gradleのバージョンとandroid gradle pluginのバージョンはわりとちゃんと合ってないと動かないので、gradlewがrepoにないとビルドすらできないということになりがち


## [SHOULD] テストを書く

- 可能であればテストを書き、カバレッジもとってバッヂにするとよい
- Tips: Robolectric 4.0 は、Android Instrumentation Test用に書いたテストをJVMで実行するためのテスティングフレームワーク
  - OrmaはRobolectricを使って「開発時はJVM test、リリース前にInstrumentation test」というスタイルでやっている
  - Robolectric固有の制限は（大量に）あるので、ライブラリによってはRobolectricだとまともに動かせないと思われるが…
    - それでもUIの絡まないビジネスロジック部分だけでもJVMでテストできると捗ると思われる

## [SHOULD] ChangeLogファイルを置く

- できればほしい
- merge commitから自動生成できるとよい（が、Ormaではそこまではやってない）
- 例: https://github.com/maskarade/Android-Orma/blob/master/CHANGELOG.md
  - 「セマンティックバージョニングに従ってます」という宣言
  - バージョン
  - リリース日
  - 差分へのリンク（例: https://github.com/maskarade/Android-Orma/compare/v6.0.1...v6.0.2 )
  - 修正内容
    - バグ修正 or 新機能などでセクションを分けることもあり

### Changelogの書き方についての緒論

https://keepachangelog.com/

* Changelogの書き方についての文書

## [SHOULD] リリースエンジニアリングをコード化する
- Ormaは gradle + make
  - `bumpMajor` などのversion変更用taskがあるのは、README内の依存関係の記述を最新のバージョンに保つため
  - ツールはなんでもいいが、とにかくコード化してREADMEに書いておくべき
    - 主に自分のために
    - たくさんOSSをメンテしてるとrelengの方法を忘れがち
    - かといって言語ごとにベストプラクティスが違うので統一も難しい
- OrmaのREADMEより:

```shell-session
./gradlew bumpMajor # or bumpMinor / bumpPatch
code CHANGES.md # edit change logs
git add -va
make publish # run tests, build artifacts, publish to jcenter, and make a git tag to HEAD
```

## License

MIT License

Copyright (c) 2018 FUJI Goro

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

