

# 概要
Rubyのコードフォーマットを通して、GitHub Actionsを体感しましょう。
リポジトリを新規作成し、削除します。コードの事前の準備は不用です。

# 手順
* 新規レポジトリ作成
* GitHub Actionsの導入
* GitHub Pull Request設定の変更
* GitHub Actionsの動作確認
* 作成したレポジトリを削除する

# 新規レポジトリ作成

今回試すためのレポジトリを追加します。 重複する可能性が低そうな下記の名前のレポジトリを作成します。

`junara_ruby_rubocop_github_actions_example`

（ご自身が作られているレポジトリ名と重複しなければなんでもかまいません。ただし、以下の工程では `junara_ruby_rubocop_github_actions_example` の名前でリポジトリを作成している前提で説明します。もしこの段階で違うリポジトリ名を使用している場合は、適宜読み替えてください。）

* 作成する時の `Description` は `Public` と `Private` どちらでもかまいません。
* main ブランチが先に存在した方が以降の作業がやりやすいので、 `README.md` ファイルを追加しておきます。
* 最後に `create repository` ボタンを押してリポジトリを作成します。

![image](https://user-images.githubusercontent.com/17272426/110229806-719a1780-7f4f-11eb-8473-2e5aa644739f.png)

以下の画面が表示されれば無事リポジトリが新規作成されました。


![image](https://user-images.githubusercontent.com/17272426/110229819-85de1480-7f4f-11eb-9212-66b3ba76a069.png)


# GitHub Actionsの導入
[GitHub Actions](https://github.co.jp/features/actions)を試しましょう。 2021/03/07現在では、 2000分/月 までは無料です。今回のデモは無料の範囲で試すことができます。

GitHub Actionsについては公式のページからの説明を参照ください。

GitHub Actionsの利用例として導入するのは[RuboCop Linter Action](https://github.com/marketplace/actions/rubocop-linter-action)です。
これのActionは、GitHubへのPush等と連動して、RubyをRubocopで解析し解析した結果を注釈として追加してくれるものです。
とりあえずこれを入れておけば、開発者はコードの作成に集中することができ、Pull Request時のレビュワーの負荷低減が期待できます。

[RuboCop Linter Action](https://github.com/marketplace/actions/rubocop-linter-action)を導入するために追加するファイルは下記の3つです。
* RuboCop Linter Action の GitHub Actionsのworkflowファイル `.github/workflows/rubocop_linter_action.yml`
* RuboCop Linter Action の設定ファイル `.github/config/rubocop_linter_action.yml`
* Rubocopの設定ファイル `.rubocop.yml`

上記の内、 Rubocopの設定ファイル は必須ではないのですが、Rubocopの設定をデフォルトで運用するのは現実的ではないので、必須ファイルとして扱いました。


ファイルの追加方法は、GitHub上から全て直接追加します。直接追加する時は、ブランチ直接と別ブランチ2つの方法があるのですが、今回はmainブランチに直接mergeします。

なお、ご自身のリポジトリに追加する時は、別ブランチを作成し、PushしPull Requestの工程により、既存のRubyのコードが全てRubocopのチェックをクリアできる状態であることを検証して、Mergeすることを強くオススメします。これは、main（master）ブランチのコードがrubocopでエラーが起きる状態のままですと、pushしたコードにかかわらずrubocopによるエラーが常に出続けてしまうため、開発がすまなくなるためです。

## RuboCop Linter Action の GitHub Actionsのworkflowファイル

`.github/workflows/rubocop_linter_action.yml` に下記のコードを記述し mainブランチに直接commitします。

```.github/workflows/rubocop_linter_action.yml
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: RuboCop Linter Action
      uses: andrewmcodes/rubocop-linter-action@v3.3.0
      with:
        action_config_path: ".github/config/rubocop_linter_action.yml" # Note: this is the default location
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```


* `junara_ruby_rubocop_github_actions_example` の大元に移動して `Create new file` を押します。
  ![image](https://user-images.githubusercontent.com/17272426/110230414-1dddfd00-7f54-11eb-9385-19bee1339b7c.png)
* Name your fileの箇所に `.github/workflows/rubocop_linter_action.yml` と入力します。
  ![image](https://user-images.githubusercontent.com/17272426/110230458-701f1e00-7f54-11eb-9751-15692236b6ee.png)
* 以下のようになります
  ![image](https://user-images.githubusercontent.com/17272426/110230468-8a58fc00-7f54-11eb-8448-010f1753a5b2.png)
* 前述のコードを入力します
  ![image](https://user-images.githubusercontent.com/17272426/110230475-96dd5480-7f54-11eb-9c2e-7dab51e93275.png)
* ページ下部に移動し、下図のように入力し `Commit new file` を押して main ブランチに直接commitします。
![image](https://user-images.githubusercontent.com/17272426/110230579-53371a80-7f55-11eb-9e91-96fff645ce0b.png)
* 下図のようになっていれば成功です。
  ![image](https://user-images.githubusercontent.com/17272426/110230590-6c3fcb80-7f55-11eb-85e6-fc154fba2d13.png)

## RuboCop Linter Action の設定ファイル
`.github/config/rubocop_linter_action.yml` に下記のコードを記述し mainブランチに直接commitします。

```.github/config/rubocop_linter_action.yml
versions:
  - rubocop
  - rubocop-rails
  - rubocop-minitest
  - rubocop-performance
  - rubocop-rspec
rubocop_config_path: ".rubocop.yml"
```

手順は `.github/workflows/rubocop_linter_action.yml` とほぼ同様ですので、細かな説明は割愛します。

* Create new fileを押す。
  ![image](https://user-images.githubusercontent.com/17272426/110230613-87124000-7f55-11eb-891c-76068b65b020.png)
* 入力する
  ![image](https://user-images.githubusercontent.com/17272426/110230641-bc1e9280-7f55-11eb-8786-745984da9f47.png)
* 下図のように入力して `Commit new file` を行う。
![image](https://user-images.githubusercontent.com/17272426/110230652-d193bc80-7f55-11eb-888d-c669d17433e4.png)
* 下図のようになる
  ![image](https://user-images.githubusercontent.com/17272426/110230658-d9ebf780-7f55-11eb-8c70-14fae66d5712.png)



## Rubocopの設定ファイル
`.rubocop.yml` に下記のコードを記述し mainブランチに直接commitします。

```.rubocop.yml
Style/AsciiComments:
  Enabled: false

Style/Documentation:
  Enabled: false

Style/ClassAndModuleChildren:
  Enabled: false

Style/FrozenStringLiteralComment:
  Enabled: false

Metrics/ClassLength:
  Enabled: false

Metrics/ModuleLength:
  Enabled: false

AllCops:
  NewCops: enable # rubocopの新機能をactiveにする
  Exclude:
    - 'Gemfile'
    - 'log/**/*'
    - 'spec/**/*'
    - 'vendor/**/*'
    - 'tmp/**/*'
    - 'db/**/*'
    - 'bin/**/*'
    - 'assets/**/*'
    - 'node_modules/**/*'
```

手順は `.github/workflows/rubocop_linter_action.yml` とほぼ同様ですので、細かな説明は割愛します。

* Create new fileを押します。
  ![image](https://user-images.githubusercontent.com/17272426/110230681-f38d3f00-7f55-11eb-9d31-da30367cf947.png)

* コードを入力します
  ![image](https://user-images.githubusercontent.com/17272426/110230730-38b17100-7f56-11eb-9863-a459aeaa365c.png)
* 下図のように入力して `Commit new file` を押す
  ![image](https://user-images.githubusercontent.com/17272426/110230702-161f5800-7f56-11eb-8fc5-944054a10efe.png)
* 下図のようになる。
  ![image](https://user-images.githubusercontent.com/17272426/110230746-4ebf3180-7f56-11eb-9ff6-9ce41f4235c4.png)


# GitHub Pull Request設定の変更
GitHub ActionsとPull Requestを連動させ、GitHub Actionsが全て成功しない場合はPull Requestをmergeできないようにします。

これにより、誤って品質の低いコードがmergeされることを防ぐことができます。

設定は `Settings > Branches > Add rule` より行います。

* Settingsを押します
  ![image](https://user-images.githubusercontent.com/17272426/110230381-db1c2500-7f53-11eb-97f6-fd29d989f116.png)
* Branchesを押します
  ![image](https://user-images.githubusercontent.com/17272426/110230387-e0796f80-7f53-11eb-9fd2-4b8120e623ef.png)
* Add rule を押します。
  ![image](https://user-images.githubusercontent.com/17272426/110230392-e53e2380-7f53-11eb-9a96-9368354dadf7.png)
* `Require status cehcks to pass before merging` と `RuboCop Action` にチェックをいれます。その後, `Create` ボタンを押します。これによりmainブランチへのmergeの時はRuboCopのチェックが必須となります。
  ![image](https://user-images.githubusercontent.com/17272426/110230775-a067bc00-7f56-11eb-9f4d-9649a933ef6a.png)
* 下図のような表示になれば成功です。
  ![image](https://user-images.githubusercontent.com/17272426/110230782-be352100-7f56-11eb-9bfc-0a8fd80cfb23.png)


# GitHub Actionsの動作確認
テスト用のRubyコード（正しくないRubyのコード）をPull Requestし、今回導入したGitHub Actionsの効果を体感しましょう。

## テスト用Rubyコードをcommitする
テスト用のRubyコード `kadai/test_1.rb` を、 `rubocop_test_code` というブランチ名でcommitします。

このコードは、RuboCopのテストが通らない二箇所あります。

`kadai/test_1.rb`

```kadai/test_1.rb
class Meat
  def initialize(nome)
    @name = name
  end

  def bite(num)
    "#{@name}kamu"*num
  end
end

Meat.new('butaniku').bite(2)
#=> "butanikukamubutanikukamu"
```

手順は `.github/workflows/rubocop_linter_action.yml` とほぼ同様ですが、コードを追加後に 新たなブランチを作りますので、その点だけ注意して下さい。

* Create new fileを押します
  ![image](https://user-images.githubusercontent.com/17272426/110230813-f6d4fa80-7f56-11eb-8b78-db1965b4a917.png)
* コードを追加し、 `ruboco_test_code` というブランチを追加します。
  ![image](https://user-images.githubusercontent.com/17272426/110230855-3996d280-7f57-11eb-9c18-0c01fecc0160.png)

* ひきつづきPull Requestを作成しますので以下にすすんでください。

## Pull Requestする
Pull Requestを作成します。
* `rubocop_test_code` を `main` ブランチに対してPull Requestを作成します。
  ![image](https://user-images.githubusercontent.com/17272426/110230883-63e89000-7f57-11eb-948a-e12bc231ead0.png)

* GitHub Actionsによって、Pull Requestのmergeがブロックされていることを確認して下さい。
  ![image](https://user-images.githubusercontent.com/17272426/110230968-0d2f8600-7f58-11eb-81ab-58d7a111c6c6.png)

Pull Requestを見ると赤文字で `All checks have failed` と表示されます。

これは、 RuboCop Linter Action](https://github.com/marketplace/actions/rubocop-linter-action) 

GitHub Actionsが実行され、Rubocopのチェックが行われた結果、コードの不備が見つかったということです。

また、上述の Settingsでの設定により黄色で `Required statuses must pass before merging` とあり、このままだとmergeができない事がわかります。

このままだとコードをmergeできませんので、以下の項でコードを修正しましょう。

## 修正箇所の確認
RuboCop Linter Action](https://github.com/marketplace/actions/rubocop-linter-action) はコード修正箇所commentで追加してくれますので確認します。

* Files changedを押します。
  ![image](https://user-images.githubusercontent.com/17272426/110230980-2fc19f00-7f58-11eb-8f66-e7fad81b8d9d.png)
* 2箇所RuboCopによる指摘が追加されている事を確認してください。
  ![image](https://user-images.githubusercontent.com/17272426/110230992-3e0fbb00-7f58-11eb-9833-d33115b7da32.png)
## 修正する
この2箇所の指摘を修正しましょう。
今回はGitHub上から直接修正します。


* 右上の所から `Edit file` を選択します。
  ![image](https://user-images.githubusercontent.com/17272426/110231011-5f70a700-7f58-11eb-91b0-fcf636e1029c.png)
* 指摘された箇所を修正します。 `rubocop_test_code` ブランチに commitします。

修正コードは以下の通りです。

```kadai/test_1.rb
class Meat
  def initialize(name)
    @name = name
  end

  def bite(num)
    "#{@name}kamu" * num
  end
end

Meat.new('butaniku').bite(2)
#=> "butanikukamubutanikukamu"
```

  ![image](https://user-images.githubusercontent.com/17272426/110231044-b1193180-7f58-11eb-9680-4f05891c0878.png)

* `Conversation` タブにもどります。2分ぐらいたつと下図の通り、全て緑色になり、mergeが可能となっていることがわかります。
  ![image](https://user-images.githubusercontent.com/17272426/110231086-f50c3680-7f58-11eb-8ad3-292f51dd4e3d.png)



ここまでの工程でいかのことができることが確認できました。

* GitHub Actionsの導入
* GitHub Pull Request設定の変更
* GitHub Actionsの動作確認

あとは、今回デモ用に作成したレポジトリーを削除するだけです。

# 作成したレポジトリを削除する
今回作成したレポジトリを削除します。
もし、今回作成したレポジトリを削除したくない場合はこの工程は不要です。

* `Settings > Options > (ページ最下部へ移動)`
  ![image](https://user-images.githubusercontent.com/17272426/110231167-4288a380-7f59-11eb-9b73-19fbacd6ca56.png)
* ページ下部の `Delete this repository` を押します。
![image](https://user-images.githubusercontent.com/17272426/110231187-677d1680-7f59-11eb-95bd-00d64c0050d0.png)
* 画面にこのような表示が現れます
![image](https://user-images.githubusercontent.com/17272426/110231195-6ea42480-7f59-11eb-879d-43e31311a649.png)

* `あなたのアカウント名/junara_ruby_rubocop_github_actions_example` を入力して、 `I understand the consequences, delete this repository` を押します。これによりリポジトリが削除されます。

![image](https://user-images.githubusercontent.com/17272426/110231235-e1150480-7f59-11eb-82ac-3ad9f380ce46.png)
* 下記のような表示になれば成功です。
  ![image](https://user-images.githubusercontent.com/17272426/110231280-0e61b280-7f5a-11eb-8629-fe244c137e03.png)

# 興味を持ったら
GitHub Actionの導入と、その効果を体感していただきました。
GitHub Actionsは他にもたくさんありますし、独自に書く事もできますので、そちらの理解を進めるのもオススメです。

* 他のGitHub Actions https://github.com/marketplace?type=actions
* GitHub Actionsのドキュメント https://docs.github.com/ja/actions

