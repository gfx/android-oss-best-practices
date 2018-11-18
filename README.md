# Android OSSプロジェクトのベストプラクティス

これは[OSS開発のリテラシー / Android編](https://paper.dropbox.com/doc/OSS-Android--ASA0GNW7rKNcc2UFqytnBbE3AQ-3Zp3zkvV2mJFF8gcpNyO5) として
[potatotips #56, 2018/11/15 in SmartNews](https://potatotips.connpass.com/event/104242/) で発表したものを改編したものです。


## この文書について

- AndroidプロジェクトをOSSとして開発するにあたってのベストプラクティスをまとめた
- あくまでも「プロジェクトの構成」について述べたもので、コードについては触れていない
- Androidに依存することも、OSSに一般的なことも混在しているが、いずれも利用者としてあると嬉しいという基準で選定した
- 紹介するベストプラクティスは MUST / SHOULD で分類した
  - **MUST** は「これが満たされてないとプロダクションで使用するのは難しい」というレベル
  - **SHOULD** は「できれば満たしてほしい」というレベル


## ベストプラクティス実践事例

https://github.com/maskarade/Android-Orma

- 2015年から [gfx](https://github.com/gfx) がプロダクトオーナーとしてメンテナンスしているAndroidのORM
- Ormaの開発で得たベストプラクティスが多いので、実践事例として紹介する

## [MUST] OSSライセンスを設定する
- OSSライセンス（OSIが「OSSライセンスである」と規定したライセンス）の設定されていないソフトウェアはOSSとはいえないので、これはベストプラクティスというよりはOSSの定義そのものではある
- ライセンスは README にセクションを設けて一言触れ、さらにLICENSE ファイルを用意すべき
  - 各々のソースコードの冒頭でライセンスを書くのは、できればやったほうがよい
    - これは「やったほうがよい」程度という認識
- Android界隈だと使われるライセンスはApache 2.0 License, MIT あたりがメジャーか


## 補足: OSSライブラリを使うときにはまずライセンスを確認すべき

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

----------

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

