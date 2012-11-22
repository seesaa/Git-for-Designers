デザイナのための Git
====================

Git とは
--------

 * バージョン管理システムです (ミスった変更を取り消したり、ブランチを分けて並行して開発ができます)
 * 変更したファイルをサーバに反映するのも Git 経由でおこないます

### 概念図

![
　　　　┌── commit -a / commit *file* ───┐
　　　　│ 　 　 　 　 　　　　　　　　　　　　　　　　　　↓　　　　　　　　　　　　　　origin
　　∧＿∧　　　　　　　　∧＿∧ 　 　　　　　　　 ∧＿∧　　　　　　　　　　　　∧＿∧
　 （　´∀｀）　─add→　（´∀｀　） ─commit→ （　´∀｀）　──push─→　 （　・∀・）
　 （　　つつ　　　　　　 ⊂　　　　つ　　　　　　　 （ HEAD）　　　　　　　　　　　（　　　　）
　　） 　） ）←checkout─（　（　 （&thinsp;←reset --&thinsp;─｜ ｜　|　 ←─fetch──　 ｜ ｜　|
　（＿_）__）　　　　　　　 　（＿（＿） 　 　 　　　　 （_＿）＿） （remote update) （_＿）＿）
作業エリア　　　　　　　 予約エリア　　　　　自分のレポジトリ　　　　　　リモートレポジトリ
&nbsp;work tree　　　　　　　 stage (index)　　　　 local repository　　　　　 　remote repository
　　　↑↑ 　 　 　　　　　　　↑　　　　　　　　　　&emsp;│↑ 　　　　　　　　　　　　　│
　　　│└────&thinsp;　checkout&ensp;HEAD &thinsp;────┘│ 　　　　　　　　　　　　　│
　　　└──────────────────┴&thinsp;pull / pull --rebase&thinsp;─┘
　　　┠───&nbsp;&thinsp;diff&ensp;───╂─&ensp;diff&ensp;--cached&ensp;─┨　　※pull　　　　　　 ≒ fetch + merge
　　　┠───────&ensp;diff HEAD&ensp;──────┨　　※pull --rebase ≒ fetch + rebase
](https://github.com/seesaa/Git-for-Designers/raw/master/images/figure1.png)

最初に！
-------

### Git のバージョンをチェック

	% git --version
	git version 1.8.0

バージョン 1.6.x などの場合は、最寄りのエンジニアを呼んでバージョンを上げてもらって下さい。便利な機能が使えるようになります。

### シェル環境を整える

bash を使っている（標準）場合は `~/.bash_profile` に、
zsh  を使っている場合は `~/.zshenv` などに、
以下を記述（追記）しておきます。

	export PAGER=`which less`
	export LESS=-ReiM

	export EDITOR=`which nano`

ここの `PAGER` は長すぎるコマンド出力をページングして読むためのツールの指定です。
`man ls` としてマニュアルページを読むときなどに使われます。

また、 `EDITOR` は Subversion や Git のコミットログの入力時などに起動されるエディタの指定です。
プログラマー以外は nano が簡単でおすすめです。

### 便利な設定をしておく

まず、 Git に無視してほしいファイルの名前パターンを設定します。エディタを起動し、

	nano ~/.gitignore

以下をコピペしてセーブ（<kbd>Ctrl+O</kbd>, <kbd>Enter</kbd>）し、閉じます（<kbd>Ctrl+X</kbd>）。

	*.o
	*.lo
	*.la
	#*#
	.*.rej
	*.rej
	.*~
	*~
	.#*
	.DS_Store
	*.sw[po]
	Thumbs.db

Git にこのファイルを使うように伝えます。

	git config --global core.excludesfile '~/.gitignore'

（Git 1.6.5.4より古い場合は '' で括らないでください）

以下をコピペしてターミナル画面に流してください。Git 生活を快適にします。
一気に流しても大丈夫です。また、既に設定している人が実行しても問題ありません。

	git config --global color.ui    true

	git config --global push.default tracking

	git config --global core.pager 'less -FRX'

	git config --global alias.st  '-p status'
	git config --global alias.ss  '-p status -s'
	git config --global alias.co   checkout
	git config --global alias.ci  'commit -v'
	git config --global alias.ca  'commit -av'
	git config --global alias.cam 'commit --amend -v'

	git config --global alias.di   diff
	git config --global alias.dc  'diff --cached'

	git config --global alias.br   branch
	git config --global alias.nbr 'checkout -b'
	git config --global alias.sw   checkout

	git config --global alias.mup   'submodule update --init --recursive'
	git config --global alias.madd  'submodule add'
	git config --global alias.mst   '-p submodule status --recursive'
	git config --global alias.msync 'submodule sync'
	git config --global alias.meach 'submodule each'
	git config --global alias.mclean "\!sh -c 'git submodule foreach \"git clean \$@\"' ."
	git config --global alias.mff   "\!sh -c 'for d in \"\${@-.}\"; do (cd \"\$d\" && [ -f .git ] && git fetch && git checkout origin); done' ."

	git config --global alias.um  "\!sh -c 'git pull \"\$@\" && git mup' ."
	git config --global alias.up  '!git um --rebase'

	git config --global diff.wordRegex "$(printf '[@$%%]*[[:alnum:]_]+|[^\n]')"
	git config --global alias.wdi 'diff --word-diff'
	git config --global alias.wdc 'diff --word-diff --cached'

	git config --global alias.au  'add -u'

	git config --global alias.ri  'rebase -i'
	git config --global alias.rio 'rebase -i origin'
	git config --global alias.rim 'rebase -i master'
	git config --global alias.rc  'rebase --continue'
	git config --global alias.rab 'rebase --abort'
	git config --global alias.rs  'rebase --skip'
	git config --global alias.mab 'merge --abort'
	git config --global alias.chp  cherry-pick
	git config --global alias.mt   mergetool

	git config --global alias.unadd    'reset --'
	git config --global alias.rollback 'reset HEAD^ --'
	git config --global alias.unci     'reset --soft HEAD^'
	git config --global alias.hreset   'reset --hard'

	git config --global alias.ap   'add -p'
	git config --global alias.unap 'reset -p'
	git config --global alias.cop  'checkout -p'

	git config --global alias.l   'log --decorate --numstat --summary'
	git config --global alias.ll  'log --decorate --numstat --summary --pretty=fuller -p'
	git config --global alias.wll 'log --decorate --numstat --summary --pretty=fuller -p --word-diff'
	git config --global alias.ol  'log --oneline --decorate'
	git config --global alias.t	  'log --graph --oneline --decorate'

	git config --global alias.g    grep

	git config --global alias.xl  'stash list'
	git config --global alias.xi  'stash save'
	git config --global alias.xo  'stash pop'
	git config --global alias.xd  'stash drop'
	git config --global alias.xa  'stash apply'
	git config --global alias.xs  'stash show'
	git config --global alias.xp  'stash show -p'
	git config --global alias.xup '!git xi && git up && git xo'

エイリアスは多分に好みの問題なのでこの例に囚われず、自分が便利でよく使うものを思い起こしやすい名前で登録してみてください。

### 上記エイリアスの解説

- `st = -p status`

    `-p`はページャが起動する設定だが、`-F`付きのlessなので短い出力はページングなし。

- `ss = -p status -s`

    覚え方: short status

    Subversionのような簡潔なステータス表示。

- `co = checkout`

- `ci = commit -v`

- `ca = commit -av`

    覚え方: commit all

- `cam = commit --amend -v`

    直前のcommitにミスや抜けがあった場合に、addしてからこれを実行する。

- `di = diff`

- `dc = diff --cached`

    覚え方: diff to commit

- `br = branch`

- `nbr = checkout -b`

    覚え方: new branch

    新しいブランチのスタート。

- `sw = checkout`

    ブランチの切り替え。

    coがあるのに敢えてこれを設けるのは、ブランチ切り替えは重要な操作なので自覚的に行うようにするためと、コマンドライン履歴で目立たせるため。

- `mup = submodule update --init --recursive`

    覚え方: module update

- `madd = submodule add`
- `mst = -p submodule status --recursive`
- `msync = submodule sync`
- `meach = submodule each`

- `mff = !sh -c 'for d in "${@-.}"; do (cd "$d" && [ -f .git ] && git fetch && git checkout origin); done' .`

    覚え方: module fast-forward

    指定ディレクトリ（複数可）もしくはカレントディレクトリのsubmoduleをリモート先端に引き上げる。

    `git meach 'git mff'`として全submoduleをリモート先端に更新することも可能。

- `um = !sh -c 'git pull \"$@\" && git mup' .`

    覚え方: upstream merge

- `up = !git um --rebase`

    覚え方: update

- `wdi = diff --word-diff`
- `wdc = diff --word-diff --cached`

    「単語」単位の差分表示。

    標準ではスペース区切りという大雑把さなのでプログラムやHTML向きに`diff.wordRegex`を設定する。

- `au = add -u`

    覚え方: add unstaged

    既存ファイルに加えた変更を一括でコミット対象としたい場合に。

- `ri = rebase -i`

- `rim = rebase -i master`

    masterから切ったブランチで、コミットを整理したいときに。

- `rio = rebase -i origin`

    originにpushする前にコミットを整理したいときに。

- `rc = rebase --continue`
- `rab = rebase --abort`
- `rs = rebase --skip`

    自分のコミットを整理するときはもちろん、複数人で作業していると他人の変更を引っ張ったときにしばしばマージが必要になる。

- `chp = cherry-pick`

    別ブランチに行ったコミットをひとつだけ持ってきたいときに。

- `unadd = reset --`

    addを取り消したいときに。`git reset`は`git reset HEAD`と同じです。
    `--`の後にはファイル名やディレクトリ名を指定できます。

- `rollback = reset HEAD^ --`

    rebaseのeditでコミットを分割したいときに有用。

- `unci = reset --soft HEAD^`

    うっかりコミットしちゃったけどちょっと待った、というときに。

    ただし、pushまでやってしまった場合は手遅れです。

        % git revert HEAD

    として直前のコミットを取り消すようなコミットを作り、pushします。

- `hreset = reset --hard`

    あまり使わなくて済むことを祈る！

    これを使うと、コミットしていない変更は完全に失われてしまうので注意しましょう。

    逆に言うと、作業ディレクトリがよく分からない状態になり、リセットしたいので捨てて構わないというときに使います。

    また、`rebase`などで失敗してしまったときにも使います。
    「やってしまった！」というときは、まずは落ち着いて自分が何をしたかを冷静に振り返り、分かる人に助けを求めましょう。
    Gitでは一回コミットしたものは消えないので、戻せない操作はほとんどありません。たいがい大丈夫です。

- `ap = add -p`

    覚え方: add patch

    差分の対話的な追加。複数の変更を行ってしまったときに、部分的にコミットするために使う。

- `unap = reset -p`

    apでやりすぎてしまった部分を対話的に取り消すのに使う。

- `cop = checkout -p`

    デバッグコードの除去など、コミットするつもりのない変更を対話的に捨てるのに使う。

- `l = log --decorate --numstat --summary`

    コミットメッセージと変更ファイルリストを含むログ表示。いちばんよく使う。

- `ll = log --decorate --numstat --summary --pretty=fuller -p`

    パッチ付きのログ。差分も見たいときに。

- `wll = log --decorate --numstat --summary --pretty=fuller -p --word-diff`

    差分を単語単位で。

- `ol = log --oneline --decorate`

    一コミット一行で履歴を眺めたいときに。

- `t = log --graph --oneline --decorate`

    覚え方: tree

    コミットのブランチ間マージの様子が見られる。

- `g = grep`

    検索はみんな大好き。

- `xl = stash list`
- `xi = stash save`
- `xo = stash pop`
- `xd = stash drop`
- `xa = stash apply`
- `xs = stash show`
- `xp = stash show -p`

    stashを使いこなすための簡易なエイリアスたち。

    popという用語はpushとの連関で混乱を招きやすいが、こうしてin/outに言い換えれば間違えない。

最低限のワークフロー
--------------------

 1. リポジトリをクローン: `git clone`
 2. 他人の変更を取得: `git up` (=`git pull --rebase`)
 3. ファイルを変更/追加: `git add file1`
 4. 変更をコミット: `git ci` (=`git commit -v`)
 5. コミットを送信: `git push`
 6. 2 へ

以下は適宜

 * 作業の合間合間に: `git st` (=`git -p status`)
 * ブランチを切り替える: `git sw branch1` (=`git checkout branch1`)
 * ブランチを戻る: `git sw -` (=`git checkout -`)

### git clone (最初の 1 回だけ)

(必要なら、いつもの作業場所のひとつ上に移動する)

	% cd ..

中央のリポジトリをクローンしてきます

	% git clone --recursive git://src1.dev/proj1

リポジトリとサブモジュールをコピーするため、多少時間がかかります。

### git pull (最新の変更を取得する)

他の人が行った変更を手元にも反映します。

キーワードの最初の設定をしていれば、以下の 1 コマンドで OK です。

	% git up

	remote: Counting objects: 6453, done.
	remote: Compressing objects: 100% (6053/6053), done.
	remote: Total 6269 (delta 3881), reused 0 (delta 0)
	Receiving objects: 100% (6269/6269), 744.80 KiB | 137 KiB/s, done.
	Resolving deltas: 100% (3881/3881), completed with 101 local objects.
	From git://src1.dev/proj1
	   68f83fa..41414d4  master     -> origin/master
	 * [new branch]      split      -> origin/split
	   9772205..3dfd8f3  testable   -> origin/testable
	First, rewinding head to replay your work on top of it...
	Fast-forwarded master to 41414d46425b62b1d2922ee57482af08c63a3dba.

設定をしていない場合、以下を実行してください。

	% git pull --rebase
	% git submodule update --init

手元のファイルがブランチの最新の状態になります。

なお、`git pull`と`git pull --rebase`の違いはこうです。

- `git pull`: `git fetch` + `git merge` = リモートから取ってきた新しいコミットをローカルに当てる

  どちらかというと、あまりフィードバック（push）する気がない変更を抱えたままリモートのコミットを取り込んで追いかけていくというときに使います。

- `git pull --rebase`: `git fetch` + `git rebase` = ローカルの独自コミットを、リモートの最新の内容に当て直す

  どちらかというと、手元の変更をいつでもフィードバック（push）できるように更新しつつリモートのコミットを取り込んで追いかけていくときに使います。
  理由は省略しますが、マージの手間はこちらの方が掛かります。ただ、自分が開発者の一員の場合はこちらを使い、いいタイミングでpushしましょう。

#### pull 時のトラブル

	error: Your local changes to 'templates/index.html' would be overwritten by merge.  Aborting.
	Please, commit your changes or stash them before you can merge.

上のようなメッセージが出た場合は、pull で更新されるファイルが手元で修正されていて、まだコミットされていません。変更をコミットしてからもう一度 `git pull` しなおして下さい。

またはコミットしていない変更を「しまって（`git stash`）」おき、pullしてから「取り出す（`git stash pop`）」方法もあります。上の方でエイリアスを設定した場合は`git xi`/`git xo`で行けます。

	% git xi
    Saved working directory and index state WIP on master: 6e5c56e Adjust spaces.
    HEAD is now at 6e5c56e Adjust spaces.
    % git up
	...
    % git xo
	...
	Dropped refs/stash@{0} (66d765acabf5b12e65fe6553e0b2847619ff679e)

pull（umやup）でリモートからコミットを持ってくると、自分の手元の変更とコンフリクト（衝突）することがあります。

	Auto-merging templates/index.html
	CONFLICT (content): Merge conflict in templates/index.html
	Automatic merge failed; fix conflicts and then commit the result.

上のようなメッセージが出た場合は、他の人が行った変更と手元でコミットした内容がコンフリクトしています。量が多すぎる、訳がわからない、修正できるか自信がない場合は、以下のコマンドを実行して中止し、エンジニアに尋ねてください。

	% git hreset

### git checkout (ブランチを切り替える)

同じリポジトリで複数のブランチを並行して開発できるのが git の便利な点です。常に master ブランチが本番に反映されます。開発は master 以外のブランチで行い、適宜 master にマージして反映、というのが開発の流れです。開発中は複数のブランチを渡り歩くことになるかもしれません。

作業ブランチを切り替えたい場合はブランチ名を指定して `git sw` (=`git checkout`) コマンドを呼びます。

	% git sw branch-name

checkout 直後は submodule の更新が必要な場合があります。

	M       modules/Hatena
	M       modules/WorkerManager

のように表示されたら、以下のコマンドで submodule を更新してください。

	% git mup

### git add (コミットするファイルを追加する)

ファイルの変更をコミットするには、一度 `git add` してコミット可能な状態にする必要があります。

	% git add templates/index.html

ディレクトリ名を指定することで、ディレクトリ以下の全ての変更を Git に通知します。

	% git add static/images

`git add` は通常何の出力も返さないので、次の項でふれる `git status` で状態を確認してください。

なお、非常に便利な機能として `git ap` (=`git add -p`) があります。これを使うと、編集箇所ごとに違いを目で確認しながら部分ごとにaddしていくことができます。
使い方についてはエンジニアから講習を受け、ぜひ使えるようになってください。

### git status/git diff (作業状況を確認する)

現在のファイル単位の作業状況を確認するには、`git status` というコマンドを使います。頻繁に使う割に長いので、先ほど`st`というエイリアスを作りました。

出力は英語のメッセージですが、読んで下さい。一行一行に意味があります。

	% git st

まっさらな状態では、以下のような出力になります。

	# On branch master
	nothing to commit (working directory clean)

1 行目はいま自分が "master" ブランチにいることを、2 行目はファイルやディレクトリに何の変更も加わっていないことを表しています。

master ブランチで作業中に新しいファイル static/images/hoge.gif をつくったときは、以下のような出力になります。

	# On branch master
	# Untracked files:
	#   (use "git add <file>..." to include in what will be committed)
	#
	#       static/images/hoge.gif

リポジトリに入っておらずバージョン管理されないファイルとして、static/images/hoge.gif がリストされています。3 行目の指示を見れば、これをコミットに含めるには `git add` すればよいということが分かるようになっています。

すでにリポジトリに含まれているファイルを変更した場合は、以下のような出力になります。

	# On branch master
	# Changed but not updated:
	#   (use "git add <file>..." to update what will be committed)
	#   (use "git checkout -- <file>..." to discard changes in working directory)
	#
	#       modified:   static/css/common.css
	#       modified:   static/images/spacer.gif
	#
	no changes added to commit (use "git add" and/or "git commit -a")

編集したけれどその変更が Git に伝わっていないファイルとして、`static/css/common.css` と `static/images/spacer.gif` がリストされています。先ほどと同じ 3 行目の指示に加え 4 行目にも指示がありますがまあ試してみてください (変更が元に戻ります)。最後の行は、今のままではコミットするものがないので `git add` してくださいといっています。

ファイルが具体的にどう編集されているのかを確認するには、 `git diff` を使います。先ほど短いエイリアス `git di` を付けたので、こまめに実行して確認してみましょう。
残念ながら画像はターミナル上で違いを見ることはできませんが、テキストファイルについては行単位でどう変わったかが表示されるので、目を凝らしてよくチェックしてみてください。

なお、ファイルの種類によっては、行単位では違いがわかりにくいことがあります。
たとえばHTMLファイルは、行というよりはタグが集まって構成されており、またそのタグも属性が同じ行に並んでいます。
こういうファイルの差分を読むときは、単語単位で差分を表示する`git wdi`を使ってみてください。タグの属性値を変えただけなどの場合は違いが一目瞭然です。

さて、`git add static/images/spacer.gif` などで変更を Git に通知してやると、`git st` の結果は以下のようになります。

	% git st
	# On branch master
	# Changes to be committed:
	#   (use "git reset HEAD <file>..." to unstage)
	#
	#       modified:   static/images/spacer.gif
	#

コミットされる予定のファイルがリストされています。

これらの出力は組み合わさって表示されることもあります。

	% git st
	# On branch master
	# Changes to be committed:
	#   (use "git reset HEAD <file>..." to unstage)
	#
	#       modified:   static/images/spacer.gif
	#
	# Changed but not updated:
	#   (use "git add <file>..." to update what will be committed)
	#   (use "git checkout -- <file>..." to discard changes in working directory)
	#
	#       modified:   static/images/logo_header.gif
	#
	# Untracked files:
	#   (use "git add <file>..." to include in what will be committed)
	#
	#       i18n/a

### git commit (コミットする)

`git add` された変更をまとめてコミットします。`git add` されていないファイルはコミットに含まれないので、新しい画像を追加したときなどは注意してください。

	% git ci

エディタが立ち上がります。下の方には差分があるのでよく確認してください。

コミットメッセージを書いて保存し、エディタを終了させればコミットされます。

	[master 7196d3f] 文言修正
	 1 files changed, 1 insertions(+), 0 deletions(-)

こんなメッセージが出たらコミット完了。

エディタが起動してしまったがやっぱりコミットはやめておこう、というときは何も編集しないか、全消去して保存終了すれば中止されます。

### git push (変更をサーバに送る)

変更を中央のリポジトリに送信します。
push したつもりがエラーが出て push できていないこともあるので、コマンドの結果に注意してください。

	% git push

	Counting objects: 24, done.
	Compressing objects: 100% (13/13), done.
	Writing objects: 100% (13/13), 1.58 KiB, done.
	Total 13 (delta 10), reused 0 (delta 0)
	To git://src1.dev/proj1
	   1ede18e..c6fa4c4  HEAD -> master

以下のように出たときは push に失敗しています！他の人が同じブランチに先に push していた場合、この表示になります。一度 pull (up) してから push しなおしてみて下さい。

	% git push
	To git://src1.dev/proj1
	 ! [rejected]        HEAD -> master (non-fast-forward)
	error: failed to push some refs to 'git://src1.dev/proj1'
	To prevent you from losing history, non-fast-forward updates were rejected
	Merge the remote changes before pushing again.  See the 'Note about
	fast-forwards' section of 'git push --help' for details.

よくある開発のワークフロー
--------------------------

まず、自分の作業環境（MacBook, シェルサーバなど）にリポジトリをクローンします。

    sh% git clone git://src1.dev/proj1

    sh% cd proj1

ここでは、メインブランチを`master`とします。（違う場合は適宜読み替えてください）

そこから開発する機能（あるいはバグ修正）用のブランチを作成して切り替えます。

    sh% git nbr taro-feature1 master

※自分のアカウント名を入れると分かりやすく、ぶつかる危険もないのでおすすめです。

リモートにブランチをプッシュ:

    sh% git push -u origin taro-feature1

開発用の実行環境にログインし、作ったブランチを取得して切り替えます。

    dev% cd /home/seesaa/proj1
    dev% git nbr taro-feature1 origin/taro-feature1

これで、リモート（`origin`）上の同じブランチを介して作業環境と実行環境で変更をやりとりできるようになりました。

ここまでが最初にやることで、あとは以下を繰り返します。

作業環境でファイルを編集してcommitし、確認したいところでpushします。

    sh% edit ...
    sh% git add ...
    sh% git ci
    ...

    sh% git push

※さっき `git push -u ...` としておいたので、もう指定しなくても`origin`の同名ブランチにpushされます。

実行環境でpullして動かします。

    dev% git up

    （必要に応じてサービス再起動など）

見た目の微調整など、簡単な変更は実行環境で加えてもOKです。

	dev% edit ...
	dev% edit ...
	dev% edit ...

    （必要に応じてサービス再起動など）

    dev% git add ...
    dev% git ci
    dev% git push

その場合は作業環境にコミットを取り込んでから作業を続けます。

    sh% git up

以上を繰り返し、機能を完成させます。

メインブランチにマージする前に、コミットを整理しましょう。

    sh% git ri master

これでエディタが開き、`master`から分岐して以降のコミット一覧が出てきます。

    pick 79e6485 Change window.open(image) to lightbox.
	pick 2734870 Add jQuery lightbox plugin.
    pick f5a6f2d Add onmouse effects to thumbnail.
	pick 7e6a083 Adjust lightbox style.
	pick df12c4b Adjust again.
	pick 0590926 Yet again!
    pick f5a6f2d Update ChangeLog.

    # Rebase 129de80..07a3f47 onto 129de80
    #
    # Commands:
    #  p, pick = use commit
    #  r, reword = use commit, but edit the commit message
    #  e, edit = use commit, but stop for amending
    #  s, squash = use commit, but meld into previous commit
    #  f, fixup = like "squash", but discard this commit's log message
    #  x, exec = run command (the rest of the line) using shell
    #
    # If you remove a line here THAT COMMIT WILL BE LOST.
    # However, if you remove everything, the rebase will be aborted.
    #

古い順でコミットが並んでいます（いちばん下が最新）。これを編集することで、コミットの順序を変えたり、くっつけたりすることができます。（下部の操作説明部分はコメントです）

たとえばこの例だと、lightboxを導入した（`79e6485`）けどjQueryプラグインを入れ忘れていた（`2734870`）ということですから、後者が先に来るべきです。また、スタイルが一発でうまく行かず、何度も調整（`7e6a083`, `df12c4b`, `0590926`）していますが、それは特段残すべき足跡とは言えないのでまとめてしまいたいところです。

そこで、コミットの列をこう書き換えます。

	pick 2734870 Add jQuery lightbox plugin.
    pick 79e6485 Change window.open(image) to lightbox.
	f 7e6a083 Adjust lightbox style.
	f df12c4b Adjust again.
	f 0590926 Yet again!
    pick f5a6f2d Add onmouse effects to thumbnail.
    pick f5a6f2d Update ChangeLog.

これで保存終了すると、履歴が指示通り改変されます。（`git ri`(`git rebase -i`)は繰り返し行えます）

イメージ的には、こういう履歴になります。

         2734870 Add jQuery lightbox plugin.
         xxxxxxx Change window.open(image) to lightbox.
         xxxxxxx Add onmouse effects to thumbnail.
         xxxxxxx Update ChangeLog.

順序の変更等でコンフリクトが発生したときはがんばって解決できればいいですが、よくわからなくなってしまったら`git rab`(`git rebase --abort`)で元の状態に戻るので大丈夫です。

なお、コミットの分割は少し難易度が高いのでここでは割愛します。始めから細かい単位でコミットし、わかりやすいメッセージを一行目に書いておくことをおすすめします。（でないと、"update" "update" "update"の羅列を前に往生することになります）

さて、`git ll`(`git log -p`)でパッチ付きの履歴を読み返し、自分はもちろん他人が読んで分かるという意味で納得が行くものになったら、メインブランチにマージします。まずメインブランチに移り:

    sh% git sw master

そこで`git merge`を実行すると、指定したブランチにだけあるコミットが接ぎ木されます。

    sh% git merge taro-feature1

    あるいは確実にマージしたという記録（マージコミット）を残したければ

    sh% git merge --no-ff taro-feature1

それでは、マージされた結果を実行環境で取得し、テストしてみましょう。

    dev% git sw master
    dev% git up

    （テストする）

問題なければ、もうブランチは消しても構いません。作業環境でこうします。

    sh% git br -d taro-feature1
    sh% git push origin :taro-feature1

実行環境では、作業環境でrebaseされたあげくに`master`にマージされたということがわからないので、単に`git br -d`だとマージしていないよと言われてしまいます。でもあなたはマージが済んでいることを知っているので、強制削除して構いません。

    dev% git br -D taro-feature1

以上が、ブランチを活用した開発フローの一例です。プロジェクトによっては、本番用ブランチと開発ブランチなどが分かれていたりするので、プロジェクトメンバーと相談しながら運用を決めましょう。

["A successful Git branching model"](https://www.google.co.jp/search?ie=UTF-8&q=%22A+successful+Git+branching+model%22)のようなフローも参考になります。ブランチの理解が深まってきたらプロジェクトで導入してみるのもいいでしょう。

こんな時は/プラスアルファ
-------------------------

以下はそれほど覚える必要がないものです。

### 変更履歴がみたい

#### `git log`

作業を始める前や、pullした直後、pushする前など、適宜`git log`で履歴を確認します。

先のエイリアスを活用し、`git l`と`git ll`で大概の用は済みます。

#### tig を使う

tig という便利ツールが手元にインストールされているかもしれません (入ってなかった場合は最寄りのエンジニアに頼んでみてください)。これを使って

	% tig

とすると、リポジトリの変更履歴が (コミットした人の名前、コミットメッセージとともに) 表示されます。この画面では矢印キーの上下で履歴を辿り、<kbd>Enter</kbd> でひとつひとつのコミットにおける変更内容などを確認することができます。終了したくなったら <kbd>q</kbd> キーを押してください。

#### magit を使う

Emacsを使っている人は[magit](https://github.com/magit/magit)がおすすめです。tigと同様のUIで履歴のブラウズができるほか、秀逸なUIでレポジトリに対するあらゆる操作ができます。

### コンフリクトの解消

`git pull` や `git merge` や  `git rebase` でコンフリクトが発生し、自分の手で解決したい場合。
`git status` すると、コンフリクトしたファイルが赤く表示されます。

	% git status
	# On branch master
	# Your branch and 'origin/master' have diverged,
	# and have 1 and 3 different commit(s) each, respectively.
	#
	# Changes to be committed:
	#
	#       modified:   templates/guide.html
	#
	# Unmerged paths:
	#   (use "git add/rm <file>..." as appropriate to mark resolution)
	#
	#       both modified:      templates/index.html
	#

この場合 templates/index.html がコンフリクトしているようです。

コンフリクトしたとされるファイルをエディタで開いてください。"<<<<<<<" と ">>>>>>>" に囲まれた部分がコンフリクトしています。

	<<<<<<< HEAD
	    <div class="container">
	=======
	    <div>[% IF foobar %]
	>>>>>>> 3100b66ffb26bb8b876cb47d68d2b91b4ec17f7f

"=======" を挟んで "HEAD" とある側が自分の変更、反対側が相手の変更です。意図を読むか相談するかして、二つの変更を手動でマージします。分からなくなった場合は、いつでも `git hreset` してエンジニアを呼んでください。

Emacsでは、コンフリクトしたファイルを開いた状態で `M-x vc-resolve-conflicts` とするか、 `magit-status` の画面でコンフリクトしたファイルにカーソルを合わせて `M-x magit-interactive-resolve-item` とすると、対話的な操作でファイルの衝突を解決することができます。

上の例だと、こうするのが正しそうです。

	    <div class="container">[% IF foobar %]

このようにしてコンフリクトマーカーをすべて消し去ったら、そのファイルを `git add` し (こうすることでコンフリクトを解消したことをシステムに伝えます)、いつものようにコミット&プッシュします。コンフリクトしているファイルが複数ある場合は、全部に対して手動マージしてからコミットしてください。

	% git add templates/index.html
	% git commit
	% git push

### ブランチを間違えてコミットしてしまった

push している場合やよくわからない場合は、エンジニアに頼んでください。
まだ push されておらず自分の手で解決したい場合は、以下のようにしてください。

theme ブランチに行うべき変更を master ブランチにコミットしてしまった場合。その直後なら

(theme ブランチに戻る)

	% git sw theme

(master の最新のコミット=誤った変更を theme ブランチに適用)

	% git chp master

(master の最新のコミットを巻き戻す)

	% git revert master

これで完了です。コンフリクトが発生するなどして分からなくなったら、`git hreset` してエンジニアを呼んでください。

### あの言葉が入ったファイルを探す

`git g 'word'` で、リポジトリ内からその単語を含むファイルと行を表示できます。ふつうに検索すると、アプリのコードも検索されてしまうので最後にディレクトリ名を加えて以下のようにするのがいいです。

	% git g 'TODO' templates

	templates/ch.html:[% # TODO : 外部化したい %]
	templates/campaign.html:            [% #TODO このテーマを使うボタンが動かない %]

### あのときのファイルの内容に戻したい

ファイルを編集開始前の状態に戻したいときは、

    % git co HEAD -- file

とすればOKです。（`--`は必須ではないですが、入れておくとzshの補完がうまく働いてくれます）

ディレクトリを指定することもできます。ただ、編集中だった内容は消えて戻せなくなるので注意してください。

    % git cop HEAD -- file

として確認しながらインタラクティブに戻すのがおすすめです。

また、修正方法が気にくわない場合など、一つ前のバージョンに戻したいときは

    % git co HEAD^ -- file

あるいは

    % git co HEAD~1 -- file

とします。`HEAD~2`なら二つ前の意味になります。また、ログからコミットID（`commit abcd123..`の`abcd123...`部分）をコピーしてそれを指定することもできます。

注意点として、`checkout`した内容は自動的に`git add`された状態になります（つまりインデックスに入る）。

こまめに`git st`や`git di`/`git dc`して状態を確認しながら作業を進めましょう。
