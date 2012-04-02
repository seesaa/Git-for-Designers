デザイナのための Git
====================

Git とは
--------

 * バージョン管理システムです (ミスった変更を取り消したり、ブランチを分けて並行して開発ができます)
 * 変更したファイルをサーバに反映するのも Git 経由でおこないます

概念図

	　　 ∧＿∧     　　　　　　∧＿∧
	　　（　´∀｀） ―push→　（　・∀・）
	　　（　　　　） ←pull―　 .（　　　　）
	　　｜ ｜　| 　　　　　　　.｜ ｜　|
	　　（_＿）＿） 　　　　　　 （_＿）＿）

最初に！
-------

### Git のバージョンをチェック

	% git --version
	git version 1.7.9.5

バージョン 1.6.x などの場合は、最寄りのエンジニアを呼んでバージョンを上げてもらって下さい。便利な機能が使えるようになります。

### シェル環境を整える

bash を使っている（標準）場合は `~/.bash_profile` に、
zsh  を使っている場合は `$ZDOTDIR/.zshrc` などに、
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

	git config --global core.excludesfile ~/.gitignore

以下をコピペしてターミナル画面に流してください。Git 生活を快適にします。
一気に流しても大丈夫です。また、既に設定している人が実行しても問題ありません。

	git config --global color.ui    true

	git config --global push.default tracking

	git config --global core.pager 'less -FSX'

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

	git config --glocal alias.mup 'submodule update --init'
	git config --global alias.um  "\!sh -c 'git pull \"\$@\" && git mup' ."
	git config --global alias.up  !git um --rebase

	git config --global diff.wordRegex "$(printf '[@$%%]*[[:alnum:]_]+|[^\n]')"
	git config --global alias.wdi 'diff --word-diff'
	git config --global alias.wdc 'diff --word-diff --cached'

	git config --global alias.au  'add -u'

	git config --global alias.ri  'rebase -i
	git config --global alias.rio 'rebase -i origin'
	git config --global alias.rim 'rebase -i master'
	git config --global alias.rc  'rebase --continue'
	git config --global alias.rab 'rebase --abort'
	git config --global alias.rs  'rebase --skip'
	git config --global alias.chp  cherry-pick

	git config --global alias.unadd    'reset HEAD --'
	git config --global alias.rollback 'reset HEAD^ --'
	git config --global alias.unci     'reset --soft HEAD^'
	git config --global alias.sreset   'reset --soft'
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

	% git clone --recursive git@src1.dev:/home/git/project.git

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
	From repository01:/var/git/projects/Hatena-Project
	   68f83fa..41414d4  master     -> origin/master
	 * [new branch]      split      -> origin/split
	   9772205..3dfd8f3  testable   -> origin/testable
	First, rewinding head to replay your work on top of it...
	Fast-forwarded master to 41414d46425b62b1d2922ee57482af08c63a3dba.

設定をしていない場合、以下を実行してください。

	% git pull --rebase
	% git submodule update --init

手元のファイルがブランチの最新の状態になります。

#### pull 時のトラブル

	error: Your local changes to 'templates/index.html' would be overwritten by merge.  Aborting.
	Please, commit your changes or stash them before you can merge.

上のようなメッセージが出た場合は、pull で更新されるファイルが手元で修正されていて、まだコミットされていません。変更をコミットしてからもう一度 `git pull` しなおして下さい。

	Auto-merging templates/index.html
	CONFLICT (content): Merge conflict in templates/index.html
	Automatic merge failed; fix conflicts and then commit the result.

上のようなメッセージが出た場合は、他の人が行った変更と手元でコミットした内容がコンフリクトしています。量が多すぎる、訳がわからない、修正できるか自信がない場合は、以下のコマンドを実行して中止し、エンジニアに尋ねてください。

	% git reset --hard

### git checkout (ブランチを切り替える/作成する)

同じリポジトリで複数のブランチを並行して開発できるのが git の便利な点です。常に master ブランチが本番に反映されます。開発は master 以外のブランチで行い、適宜 master にマージして反映、というのが開発の流れです。開発中は複数のブランチを渡り歩くことになるかもしれません。

作業ブランチを切り替えたい場合は

	% git checkout branch-name

ブランチ名を指定して `git checkout` コマンドを呼びます。

新しい機能の開発などで、エンジニアが push したブランチが手元にまだ出来ていない場合は

	% git checkout -t origin/branch-name

とします。

checkout 直後は submodule の更新が必要な場合があります。

	M       modules/Hatena
	M       modules/WorkerManager

のように表示されたら、以下のコマンドで submodule を更新してください。

	git submodule update --init

### git add (コミットするファイルを追加する)

ファイルの変更をコミットするには、一度 `git add` してコミット可能な状態にする必要があります。

	% git add templates/index.html

ディレクトリ名を指定することで、ディレクトリ以下の全ての変更を Git に通知します。

	% git add static/images

`git add` は通常何の出力も返さないので、次の項でふれる `git status` で状態を確認してください。

### git status (作業状況を確認する)

現在の作業状況を確認するには、`git status` というコマンドを使います。英語のメッセージですが、読んで下さい。一行一行に意味があります。

	% git status

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
	#       modified:   static/images/spacer.gif
	#
	no changes added to commit (use "git add" and/or "git commit -a")

編集したけれどその変更が Git に伝わっていないファイルとして、static/images/spacer.gif がリストされています。先ほどと同じ 3 行目の指示に加え 4 行目にも指示がありますがまあ試してみてください (変更が元に戻ります)。最後の行は、今のままではコミットするものがないので `git add` してくださいといっています。(`git commit -a` は、はてな的に非推奨です。見なかったことにしてください)

git add static/images/spacer.gif などで変更を Git に通知してやると、`git status` の結果は以下のようになります。

	% git status
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

(もしくは)

	% git commit

エディタが立ち上がります。適当にコミットメッセージを書いて保存し、エディタを終了させてください。

	[master 7196d3f] 文言修正
	 1 files changed, 1 insertions(+), 0 deletions(-)

こんなメッセージが出たらコミット完了。

### git push (変更をサーバに送る)

変更を中央のリポジトリに送信します。
push したつもりがエラーが出て push できていないこともあるので、コマンドの結果に注意してください。

	% git push

	Counting objects: 24, done.
	Compressing objects: 100% (13/13), done.
	Writing objects: 100% (13/13), 1.58 KiB, done.
	Total 13 (delta 10), reused 0 (delta 0)
	To git@repository01:/var/git/projects/Hatena-Project
	   1ede18e..c6fa4c4  HEAD -> master

以下のように出たときは push に失敗しています！他の人が同じブランチに先に push していた場合、この表示になります。一度 pull してから push しなおしてみて下さい。

	% git push
	To git@repository01:/var/git/projects/Hatena-Project
	 ! [rejected]        HEAD -> master (non-fast-forward)
	error: failed to push some refs to 'git@repository01:/var/git/projects/Hatena-Project'
	To prevent you from losing history, non-fast-forward updates were rejected
	Merge the remote changes before pushing again.  See the 'Note about
	fast-forwards' section of 'git push --help' for details.

こんな時は/プラスアルファ
-------------------------

以下はそれほど覚える必要がないものです。

### 変更履歴がみたい

#### `git log --stat`

TBD

#### tig を使う

tig という便利ツールが手元にインストールされているかもしれません (入ってなかった場合は最寄りのエンジニアに頼んでみてください)。これを使って

	% tig

とすると、リポジトリの変更履歴が (コミットした人の名前、コミットメッセージとともに) 表示されます。この画面では矢印キーの上下で履歴を辿り、<kbd>Enter</kbd> でひとつひとつのコミットにおける変更内容などを確認することができます。終了したくなったら <kbd>q</kbd> キーを押してください。

### コンフリクトの解消

`git pull` や `git merge` でコンフリクトが発生し、自分の手で解決したい場合。
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

"=======" を挟んで "HEAD" とある側が自分の変更、反対側が相手の変更です。意図を読むか相談するかして、二つの変更を手動でマージします。分からなくなった場合は、いつでも git reset --hard してエンジニアを呼んでください。

この例だとこれが正しそうです。

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

	% git checkout theme

(master の最新のコミット=誤った変更を theme ブランチに適用)

	% git cherry-pick master

(master の最新のコミットを巻き戻す)

	% git revert master

これで完了です。コンフリクトが発生するなどして分からなくなったら、`git reset --hard` してエンジニアを呼んでください。

### あの言葉が入ったファイルを探す

`git grep '<var>word</var>'` で、リポジトリ内からその単語を含むファイルと行を表示できます。ふつうに検索すると、アプリのコードも検索されてしまうので最後にディレクトリ名を加えて以下のようにするのがいいです。

	% git grep 'TODO' templates

	templates/ch.html:[% # TODO : 外部化したい %]
	templates/campaign.html:            [% #TODO このテーマを使うボタンが動かない %]

### あのときのファイルの内容に戻したい

TBD git checkout $revision -- $file
