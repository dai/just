<div align=right>目次↗️</div>

<h1 align=center><code>just</code></h1>

<div align=center>
  <a href=https://crates.io/crates/just>
    <img src=https://img.shields.io/crates/v/just.svg alt="crates.io version">
  </a>
  <a href=https://github.com/casey/just/actions/workflows/ci.yaml>
    <img src=https://github.com/casey/just/actions/workflows/ci.yaml/badge.svg alt="build status">
  </a>
  <a href=https://github.com/casey/just/releases>
    <img src=https://img.shields.io/github/downloads/casey/just/total.svg alt=downloads>
  </a>
  <a href=https://discord.gg/ezYScXR>
    <img src=https://img.shields.io/discord/695580069837406228?logo=discord alt="chat on discord">
  </a>
  <a href=mailto:casey@rodarmor.com?subject=Thanks%20for%20Just!>
    <img src=https://img.shields.io/badge/Say%20Thanks-!-1EAEDB.svg alt="say thanks">
  </a>
</div>
<br>

`just`は、プロジェクト固有のコマンドを保存して実行するための便利なツールです。

このREADMEは[書籍](https://just.systems/man/en/)としても利用できます。書籍は最新のリリースを反映しており、[GitHubのREADME](https://github.com/casey/just/blob/master/README.md)は最新のmasterブランチを反映しています。

(中文文档在 [这里](https://github.com/casey/just/blob/master/README.中文.md),
快看过来!)

レシピと呼ばれるコマンドは、`make`に触発された構文を持つ`justfile`というファイルに保存されます：

![screenshot](https://raw.githubusercontent.com/casey/just/master/screenshot.png)

その後、`just RECIPE`で実行できます：

```console
$ just test-all
cc *.c -o main
./test --all
Yay, all your tests passed!
```

`just`には多くの便利な機能があり、`make`に対する多くの改善点があります：

- `just`はビルドシステムではなくコマンドランナーなので、[`make`の複雑さと特異性](#makeの特異性)の多くを回避します。`.PHONY`レシピは不要です！

- Linux、MacOS、Windows、その他の合理的なUnixシステムがサポートされており、追加の依存関係はありません。（ただし、システムに`sh`がない場合は、[別のシェルを選択](#shell)する必要があります。）

- エラーは具体的で有益であり、構文エラーはそのソースコンテキストとともに報告されます。

- レシピは[コマンドライン引数](#recipe-parameters)を受け入れることができます。

- 可能な限り、エラーは静的に解決されます。不明なレシピと循環依存関係は、何も実行される前に報告されます。

- `just`は[`.env`ファイルをロード](#dotenv-settings)し、環境変数を簡単に設定できます。

- レシピは[コマンドラインから一覧表示](#listing-available-recipes)できます。

- コマンドライン補完スクリプトは、[最も一般的なシェル](#shell-completion-scripts)で利用できます。

- レシピは、PythonやNodeJSなどの[任意の言語](#shebang-recipes)で記述できます。

- `just`は、`justfile`を含むディレクトリだけでなく、任意のサブディレクトリから呼び出すことができます。

- その他[多数](https://just.systems/man/en/)の機能！

`just`についてサポートが必要な場合は、お気軽にissueを開くか、[Discord](https://discord.gg/ezYScXR)でお知らせください。機能リクエストとバグレポートは常に歓迎します！

インストール
------------

### 前提条件

`just`は、Linux、MacOS、BSDを含む、合理的な`sh`を持つ任意のシステムで実行されます。

#### Windows

Windowsでは、`just`は[Git for Windows](https://git-scm.com)、[GitHub Desktop](https://desktop.github.com)、または[Cygwin](http://www.cygwin.com)によって提供される`sh`で動作します。インストール後、`just`を呼び出すシェルの`PATH`で`sh`が利用可能である必要があります。

`sh`をインストールしたくない場合は、`shell`設定を使用して、選択したシェルを使用できます。

PowerShellの場合：

```just
# sh の代わりに PowerShell を使用:
set shell := ["powershell.exe", "-c"]

hello:
  Write-Host "Hello, world!"
```

または`cmd.exe`の場合：

```just
# sh の代わりに cmd.exe を使用:
set shell := ["cmd.exe", "/c"]

list:
  dir
```

コマンドライン引数を使用してシェルを設定することもできます。たとえば、PowerShellを使用するには、`--shell powershell.exe --shell-arg -c`で`just`を起動します。

（PowerShellはWindows 7 SP1およびWindows Server 2008 R2 S1以降にデフォルトでインストールされており、`cmd.exe`は非常に扱いにくいため、ほとんどのWindowsユーザーにはPowerShellが推奨されます。）

### パッケージ

#### クロスプラットフォーム

<table>
  <thead>
    <tr>
      <th>パッケージマネージャー</th>
      <th>パッケージ</th>
      <th>コマンド</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href=https://github.com/alexellis/arkade>arkade</a></td>
      <td>just</td>
      <td><code>arkade get just</code></td>
    </tr>
    <tr>
      <td><a href=https://asdf-vm.com>asdf</a></td>
      <td><a href=https://github.com/olofvndrhr/asdf-just>just</a></td>
      <td>
        <code>asdf plugin add just</code><br>
        <code>asdf install just &lt;version&gt;</code>
      </td>
    </tr>
    <tr>
      <td><a href=https://www.rust-lang.org>Cargo</a></td>
      <td><a href=https://crates.io/crates/just>just</a></td>
      <td><code>cargo install just</code></td>
    </tr>
    <tr>
      <td><a href=https://docs.conda.io/projects/conda/en/latest/index.html>Conda</a></td>
      <td><a href=https://anaconda.org/conda-forge/just>just</a></td>
      <td><code>conda install -c conda-forge just</code></td>
    </tr>
    <tr>
      <td><a href=https://brew.sh>Homebrew</a></td>
      <td><a href=https://formulae.brew.sh/formula/just>just</a></td>
      <td><code>brew install just</code></td>
    </tr>
    <tr>
      <td><a href=https://nixos.org/nix/>Nix</a></td>
      <td><a href=https://github.com/NixOS/nixpkgs/blob/master/pkgs/by-name/ju/just/package.nix>just</a></td>
      <td><code>nix-env -iA nixpkgs.just</code></td>
    </tr>
    <tr>
      <td><a href=https://www.npmjs.com/>npm</a></td>
      <td><a href=https://www.npmjs.com/package/rust-just>rust-just</a></td>
      <td><code>npm install -g rust-just</code></td>
    </tr>
    <tr>
      <td><a href=https://pipx.pypa.io/stable/>pipx</a></td>
      <td><a href=https://pypi.org/project/rust-just/>rust-just</a></td>
      <td><code>pipx install rust-just</code></td>
    </tr>
    <tr>
      <td><a href=https://snapcraft.io>Snap</a></td>
      <td><a href=https://snapcraft.io/just>just</a></td>
      <td><code>snap install --edge --classic just</code></td>
    </tr>
  </tbody>
</table>

#### BSD

<table>
  <thead>
    <tr>
      <th>オペレーティングシステム</th>
      <th>パッケージマネージャー</th>
      <th>パッケージ</th>
      <th>コマンド</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href=https://www.freebsd.org>FreeBSD</a></td>
      <td><a href=https://www.freebsd.org/doc/handbook/pkgng-intro.html>pkg</a></td>
      <td><a href=https://www.freshports.org/deskutils/just/>just</a></td>
      <td><code>pkg install just</code></td>
    </tr>
  </tbody>
</table>

#### Linux

<table>
  <thead>
    <tr>
      <th>オペレーティングシステム</th>
      <th>パッケージマネージャー</th>
      <th>パッケージ</th>
      <th>コマンド</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href=https://alpinelinux.org>Alpine</a></td>
      <td><a href=https://wiki.alpinelinux.org/wiki/Alpine_Linux_package_management>apk-tools</a></td>
      <td><a href=https://pkgs.alpinelinux.org/package/edge/community/x86_64/just>just</a></td>
      <td><code>apk add just</code></td>
    </tr>
    <tr>
      <td><a href=https://www.archlinux.org>Arch</a></td>
      <td><a href=https://wiki.archlinux.org/title/Pacman>pacman</a></td>
      <td><a href=https://archlinux.org/packages/extra/x86_64/just/>just</a></td>
      <td><code>pacman -S just</code></td>
    </tr>
    <tr>
      <td>
        <a href=https://debian.org>Debian 13</a> および
        <a href=https://ubuntu.com>Ubuntu 24.04</a> 派生</td>
      <td><a href=https://en.wikipedia.org/wiki/APT_(software)>apt</a></td>
      <td><a href=https://packages.debian.org/trixie/just>just</a></td>
      <td><code>apt install just</code></td>
    </tr>
    <tr>
      <td><a href=https://getfedora.org>Fedora</a></td>
      <td><a href=https://dnf.readthedocs.io/en/latest/>DNF</a></td>
      <td><a href=https://src.fedoraproject.org/rpms/rust-just>just</a></td>
      <td><code>dnf install just</code></td>
    </tr>
    <tr>
      <td><a href=https://nixos.org/nixos/>NixOS</a></td>
      <td><a href=https://nixos.org/nix/>Nix</a></td>
      <td><a href=https://github.com/NixOS/nixpkgs/blob/master/pkgs/by-name/ju/just/package.nix>just</a></td>
      <td><code>nix-env -iA nixos.just</code></td>
    </tr>
  </tbody>
</table>

#### Windows

<table>
  <thead>
    <tr>
      <th>パッケージマネージャー</th>
      <th>パッケージ</th>
      <th>コマンド</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href=https://chocolatey.org>Chocolatey</a></td>
      <td><a href=https://github.com/michidk/just-choco>just</a></td>
      <td><code>choco install just</code></td>
    </tr>
    <tr>
      <td><a href=https://scoop.sh>Scoop</a></td>
      <td><a href=https://github.com/ScoopInstaller/Main/blob/master/bucket/just.json>just</a></td>
      <td><code>scoop install just</code></td>
    </tr>
    <tr>
      <td><a href=https://learn.microsoft.com/en-us/windows/package-manager/>Windows Package Manager</a></td>
      <td><a href=https://github.com/microsoft/winget-pkgs/tree/master/manifests/c/Casey/Just>Casey/Just</a></td>
      <td><code>winget install --id Casey.Just --exact</code></td>
    </tr>
  </tbody>
</table>

#### macOS

<table>
  <thead>
    <tr>
      <th>パッケージマネージャー</th>
      <th>パッケージ</th>
      <th>コマンド</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href=https://www.macports.org>MacPorts</a></td>
      <td><a href=https://ports.macports.org/port/just/summary>just</a></td>
      <td><code>port install just</code></td>
    </tr>
  </tbody>
</table>

![just package version table](https://repology.org/badge/vertical-allrepos/just.svg)

### ビルド済みバイナリ

Linux、MacOS、Windows用のビルド済みバイナリは、[リリースページ](https://github.com/casey/just/releases)で見つけることができます。

次のコマンドを使用して、Linux、MacOS、またはWindows上で最新リリースをダウンロードできます。`DEST`を`just`を配置するディレクトリに置き換えてください：

```console
curl --proto '=https' --tlsv1.2 -sSf https://just.systems/install.sh | bash -s -- --to DEST
```

たとえば、`just`を`~/bin`にインストールするには：

```console
# ~/bin を作成
mkdir -p ~/bin

# just を ~/bin/just にダウンロードして展開
curl --proto '=https' --tlsv1.2 -sSf https://just.systems/install.sh | bash -s -- --to ~/bin

# シェルが実行ファイルを検索するパスに `~/bin` を追加
# この行は、シェルの初期化ファイルに追加する必要があります。
# 例: `~/.bashrc` または `~/.zshrc`
export PATH="$PATH:$HOME/bin"

# just が実行可能になりました
just --help
```

### GitHub Actions

`just`はGitHub Actionsにいくつかの方法でインストールできます。

MacOSでは`brew install just`、Windowsでは`choco install just`を使用して、GitHub Actionsランナーにプリインストールされているパッケージマネージャーを使用します。

[extractions/setup-just](https://github.com/extractions/setup-just)を使用：

```yaml
- uses: extractions/setup-just@v3
  with:
    just-version: 1.5.0  # オプションのsemver指定、それ以外は最新
```

または[taiki-e/install-action](https://github.com/taiki-e/install-action)を使用：

```yaml
- uses: taiki-e/install-action@just
```

### リリースRSSフィード

`just`リリースの[RSSフィード](https://en.wikipedia.org/wiki/RSS)は[こちら](https://github.com/casey/just/releases.atom)で利用できます。

後方互換性
-----------------------

バージョン1.0のリリースにより、`just`は後方互換性と安定性に対する強力なコミットメントを提供します。

将来のリリースでは、既存の`justfile`が動作しなくなったり、コマンドラインインターフェースの動作中の呼び出しを壊したりする後方互換性のない変更は導入されません。

ただし、これは明らかなバグの修正を妨げるものではありません。たとえその動作に依存する`justfile`を壊す可能性があってもです。

`just` 2.0は決してありません。望ましい後方互換性のない変更は、`justfile`ごとにオプトインになるため、ユーザーは自分の都合の良い時に移行できます。

安定化の準備ができていない機能は不安定としてマークされ、いつでも変更または削除される可能性があります。不安定な機能を使用すると、デフォルトでエラーが発生しますが、`--unstable`フラグを渡すか、`set unstable`を設定するか、`JUST_UNSTABLE`環境変数を`false`、`0`、または空文字列以外の任意の値に設定することで抑制できます。

エディタサポート
--------------

`justfile`構文は`make`に十分近いため、エディタに`just`の`make`構文ハイライトを使用するように指示することをお勧めします。

### Vim と Neovim

Vimバージョン9.1.1042以降およびNeovimバージョン0.11以降は、[pbnj](https://github.com/pbnj)のおかげで、Justfile構文ハイライトをすぐにサポートしています。

#### `vim-just`

[vim-just](https://github.com/NoahTheDuke/vim-just)プラグインは、`justfile`の構文ハイライトを提供します。

[Plug](https://github.com/junegunn/vim-plug)などのお気に入りのパッケージマネージャーでインストールします：

```vim
call plug#begin()

Plug 'NoahTheDuke/vim-just'

call plug#end()
```

またはVimの組み込みパッケージサポートを使用：

```console
mkdir -p ~/.vim/pack/vendor/start
cd ~/.vim/pack/vendor/start
git clone https://github.com/NoahTheDuke/vim-just.git
```

### Visual Studio Code

VS Code用の拡張機能は[こちら](https://github.com/nefrob/vscode-just)で利用できます。

### JetBrains IDE

[linux_china](https://github.com/linux-china)によるJetBrains IDE用のプラグインは[こちら](https://plugins.jetbrains.com/plugin/18658-just)で利用できます。

クイックスタート
-----------

インストール方法については、インストールセクションを参照してください。`just --version`を実行して、正しくインストールされていることを確認してください。

構文の概要については、[このチートシート](https://cheatography.com/linux-china/cheat-sheets/justfile/)を確認してください。

`just`がインストールされて動作したら、プロジェクトのルートに次の内容の`justfile`という名前のファイルを作成します：

```just
recipe-name:
  echo 'This is a recipe!'

# これはコメントです
another-recipe:
  @echo 'This is another recipe.'
```

`just`を引数なしで実行すると、`justfile`の最初のレシピが実行されます：

```console
$ just
echo 'This is a recipe!'
This is a recipe!
```

1つ以上の引数を指定すると、実行するレシピを指定できます：

```console
$ just another-recipe
This is another recipe.
```

`just`は、各コマンドを実行する前に標準エラーに出力します。そのため、`echo 'This is a recipe!'`が出力されました。これは`@`で始まる行では抑制されます。そのため、`echo 'This is another recipe.'`は出力されませんでした。

レシピは、コマンドが失敗すると実行を停止します。ここで、`cargo publish`は`cargo test`が成功した場合にのみ実行されます：

```just
publish:
  cargo test
  # テストが合格しました、公開の時間です！
  cargo publish
```

レシピは他のレシピに依存できます。ここで、`test`レシピは`build`レシピに依存しているため、`test`の前に`build`が実行されます：

```just
build:
  cc main.c foo.c bar.c -o main

test: build
  ./test

sloc:
  @echo "`wc -l *.c` lines of code"
```

```console
$ just test
cc main.c foo.c bar.c -o main
./test
testing… all tests passed!
```

機能
--------

### デフォルトレシピ

レシピなしで`just`が呼び出されると、`[default]`属性を持つレシピ、または`[default]`属性を持つレシピがない場合は`justfile`の最初のレシピが実行されます。

このレシピは、テストの実行など、プロジェクトで最も頻繁に実行されるコマンドかもしれません：

```just
test:
  cargo test
```

依存関係を使用して、デフォルトで複数のレシピを実行することもできます：

```just
default: lint build test

build:
  echo Building…

test:
  echo Testing…

lint:
  echo Linting…
```

デフォルトレシピとして意味のあるレシピがない場合は、利用可能なレシピを一覧表示するレシピを`justfile`の先頭に追加できます：

```just
default:
  just --list
```

### 利用可能なレシピの一覧表示

レシピは`just --list`でアルファベット順に一覧表示できます：

```console
$ just --list
Available recipes:
    build
    test
    deploy
    lint
```

`just --summary`はより簡潔です：

```console
$ just --summary
build test deploy lint
```

### エイリアス

エイリアスを使用すると、レシピを代替名でコマンドラインから呼び出すことができます：

```just
alias b := build

build:
  echo 'Building!'
```

```console
$ just b
echo 'Building!'
Building!
```

### 設定

設定は解釈と実行を制御します。各設定は`justfile`のどこにでも最大1回指定できます。

たとえば：

```just
set shell := ["zsh", "-cu"]

foo:
  # この行は `zsh -cu 'ls **/*.txt'` として実行されます
  ls **/*.txt
```

### ドキュメントコメント

レシピの直前のコメントは`just --list`に表示されます：

```just
# build stuff
build:
  ./bin/build

# test stuff
test:
  ./bin/test
```

```console
$ just --list
Available recipes:
    build # build stuff
    test # test stuff
```

### 変数と置換

割り当て、デフォルトのレシピ引数、およびレシピ本文の`{{…}}`置換内で、さまざまな演算子と関数呼び出しが式でサポートされています。

```just
tmpdir  := `mktemp -d`
version := "0.2.7"
tardir  := tmpdir / "awesomesauce-" + version
tarball := tardir + ".tar.gz"

publish:
  rm -f {{tarball}}
  mkdir {{tardir}}
  cp README.md *.c {{tardir}}
  tar zcvf {{tarball}} {{tardir}}
  scp {{tarball}} me@server.com:release/
  rm -rf {{tarball}} {{tardir}}
```

### レシピパラメータ

レシピはパラメータを受け入れることができます：

```just
build target:
  cargo build --release --target {{target}}
```

パラメータを持つレシピを呼び出すには、引数としてそれらを渡します：

```console
$ just build x86_64-unknown-linux-gnu
cargo build --release --target x86_64-unknown-linux-gnu
```

デフォルト値を設定することもできます：

```just
build target="x86_64-unknown-linux-gnu":
  cargo build --release --target {{target}}
```

```console
$ just build
cargo build --release --target x86_64-unknown-linux-gnu
```

### コマンドライン引数の回避

引数の分割を回避するには、引数を引用符で囲みます：

```just
foo argument:
  touch '{{argument}}'
```

位置引数を使用することもできます：

```just
set positional-arguments

foo argument:
  touch "$1"
```

### 環境変数

環境変数は`$VARIABLE_NAME`を使用してレシピとバッククォートでアクセスできます：

```just
foo:
  echo $HOME
```

### プライベートレシピ

名前が`_`で始まるレシピとエイリアスは`just --list`から省略されます：

```just
test: _test-helper
  ./bin/test

_test-helper:
  ./bin/super-secret-test-helper-stuff
```

```console
$ just --list
Available recipes:
    test
```

### 静かなレシピ

レシピ名の前に`@`を付けると、各行の前の`@`の意味が逆になります：

```just
@quiet:
  echo hello
  echo goodbye
  @# all done!
```

```console
$ just quiet
hello
goodbye
# all done!
```

### Shebangレシピ

レシピの本文が`#!`で始まる場合、それはshebangレシピとして扱われ、スクリプトとして実行されます：

```just
polyglot: python js perl sh ruby

python:
  #!/usr/bin/env python3
  print('Hello from python!')

js:
  #!/usr/bin/env node
  console.log('Greetings from JavaScript!')

perl:
  #!/usr/bin/env perl
  print "Larry Wall says Hi!\n";

sh:
  #!/usr/bin/env sh
  echo 'Hello from sh!'

ruby:
  #!/usr/bin/env ruby
  puts "Hello from ruby!"
```

### 依存関係

レシピは他のレシピに依存できます。依存関係は常に最初に実行されます：

```just
a: b c

b:
  echo B

c:
  echo C
```

```console
$ just a
echo B
B
echo C
C
```

### コマンドラインオプション

`just`は、レシピと変数をリスト、ダンプ、デバッグするための多くの便利なコマンドラインオプションをサポートしています：

```console
$ just --list
Available recipes:
  js
  perl
  polyglot
  python
  ruby
```

よくある質問
--------------------------

### justとmakeの違いは何ですか？

`make`には、コマンドランナーとして使用するのに不適切な動作がいくつかあります。

たとえば、いくつかの状況では、`make`は実際にレシピのコマンドを実行しません。`test`というファイルがある場合：

```just
test:
  ./test
```

`make`はテストの実行を拒否します：

```console
$ make test
make: `test' is up to date.
```

`just`では、すべてのレシピはphonyとして扱われるため、この問題は発生しません。

その他の`make`の特異性の例には、割り当ての`=`と`:=`の違い、めちゃくちゃなエラーメッセージ、レシピで環境変数を使用するために`$$`が必要なこと、異なるフレーバーの`make`間の非互換性などがあります。

### Cargoビルドスクリプトとの関係は？

[`cargo`ビルドスクリプト](http://doc.crates.io/build-script.html)には非常に特定の用途があり、それは`cargo`がRustプロジェクトをビルドする方法を制御することです。

一方、`just`は、開発の一部として実行する可能性のあるその他のさまざまなコマンド用です。さまざまな構成でのテストの実行、コードのリンティング、ビルドアーティファクトのサーバーへのプッシュ、一時ファイルの削除などです。

また、`just`はRustで書かれていますが、プロジェクトが使用する言語やビルドシステムに関係なく使用できます。

さらなる考察
-----------------

私は個人的に、ほぼすべてのプロジェクト（大小を問わず）に`justfile`を書くことが非常に便利だと思っています。

複数の貢献者がいる大規模なプロジェクトでは、プロジェクトの作業に必要なすべてのコマンドを手元に置いておくことが非常に便利です。

テスト、ビルド、リント、デプロイなどのさまざまなコマンドがあり、それらをすべて1か所にまとめることは有用であり、どのコマンドを実行するか、どのように入力するかを人々に伝える時間を短縮します。

また、コマンドを配置するための簡単な場所があれば、プロジェクトの集合知の一部である他の便利なものを思いつく可能性が高くなります。たとえば、リビジョン管理ワークフローの一部に必要な難解なコマンド、プロジェクトのすべての依存関係をインストールするためのコマンド、またはビルドシステムに渡す必要があるすべてのランダムなフラグなどです。

いくつかのレシピのアイデア：

- プロジェクトのデプロイ/公開

- リリースモードとデバッグモードでのビルド

- デバッグモードまたはロギングを有効にした実行

- 複雑なgitワークフロー

- 依存関係の更新

- さまざまなテストセットの実行（高速テストと低速テストなど）、または詳細出力での実行

- 覚えておくために本当にどこかに書き留めるべき複雑なコマンドのセット

小規模な個人プロジェクトでも、^でシェル履歴を逆検索するのではなく、名前でコマンドを覚えることができるのは素晴らしいことです。また、ランダムな言語で書かれた、謎めいたビルドシステムを持つ古いプロジェクトに入って、必要なことをするために必要なすべてのコマンドが`justfile`にあることを知っているのは非常に有益です。`just`と入力すれば、何か便利な（または少なくとも興味深い！）ことが起こる可能性があります。

レシピのアイデアについては、[このプロジェクトの`justfile`](https://github.com/casey/just/blob/master/justfile)、または[野生の](https://github.com/search?q=path%3A**%2Fjustfile&type=code)`justfile`をチェックしてください。

とにかく、この信じられないほど長い README はこれで終わりです。

`just`を使用して、すべての計算の試みで大きな成功と満足を得られることを願っています！

😸

[🔼 トップに戻る！](#just)
