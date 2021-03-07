# Readme
RuboCop Linter Action の デモ
https://github.com/marketplace/actions/rubocop-linter-action

# Usage
RuboCop Linter Actionを動かすGitHub ActionsとRuboCop Linter Actionの設定をします。

`.github/workflows/rubocop_linter_action.yml`

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

`action_config_path: ".github/config/rubocop_linter_action.yml"` に設定ファイルの場所を記述します。

`.github/config/rubocop_linter_action.yml`

```.github/config/rubocop_linter_action.yml
versions:
  - rubocop
  - rubocop-rails
  - rubocop-minitest
  - rubocop-performance
  - rubocop-rspec
rubocop_config_path: ".rubocop.yml"
```

`rubocop_config_path` にrubocopの設定ファイルへのパスを記述します。


`.rubocop.yml` に設定を書きます。ここでは個人的にオススメの使わない方がよさそうな 条件をfalseにしています。

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
