# ドリコムGit基礎勉強会

# はじめに
## 本日の目的「Gitがどのように動いているかの 基礎 を学びます」
* **基礎 ≠ 入門**
 * **基礎** の具体例：[Java基礎勉強会](http://kokucheese.com/event/index/44210/), [Scala基礎勉強会](http://kokucheese.com/event/index/51993/), [Groovy基礎勉強会](http://connpass.com/event/1593/), [.NET基礎勉強会](http://connpass.com/event/2441/)
 * コンパイラの処理系、GCの仕組み辺りを基礎と言う
 * 基礎 = 名古屋系（偏見）
* Gitを普通に使うのは簡単だが、何かあった時に元に戻すのが難しい
 * [やりなおせる Git 入門 ](http://www.slideshare.net/TomohikoHimura/git-22237343) や [gitでアレを元に戻す108の方法](http://labs.timedia.co.jp/2011/08/git-undo-999.html) のような手順はまとまっているが、どんな状態になってるのかが分からなければどんな方法を使えばいいかも分からない
 * やり方を覚えるのではなくGitの仕組みを心で理解する！

## 対象者
* 日常的にGitを使っている人
 * コマンドでGitを使っていればなおよし

## 本日やること
* Gitの履歴を履歴管理する
 * .gitディレクトリ(以降：リポジトリ)を履歴管理することで、各コマンド実行時にリポジトリがどのように変化しているか調べる

## 本日やらないこと
* Gitのコマンドの基本的な使い方

----------------------
# 前提：Gitには3つの状態がある
参考：[Git - Gitの基本](http://git-scm.com/book/ja/%E4%BD%BF%E3%81%84%E5%A7%8B%E3%82%81%E3%82%8B-Git%E3%81%AE%E5%9F%BA%E6%9C%AC#三つの状態)

## working directory / work tree
作業中の場所

## stating area / index
`git add` した時に更新される

## git directory / repository
`git commit` した時に更新される

---------------------------------------------------------------------------------------------
# git initする
```sh
$ git --version
git version 1.8.3.1
$ mkdir git-base-study
$ cd git-base-study/
$ git init
Initialized empty Git repository in ~/git-base-study/.git/
```

# .git/ の中身を履歴管理する
```sh
$ cd .git/
$ git init
Initialized empty Git repository in ~/git-base-study/.git/.git/
```

https://twitter.com/sue445/status/347729384846811137
![.git ディレクトリの中で git init すると、gitのメタデータを履歴管理できるのか。。。 ](http://cdn-ak.f.st-hatena.com/images/fotolife/s/sue445/20130625/20130625003148.png?1372087914)

---------------------------------------------------------------------------------------------
# git init
```sh
$ git init
```

https://github.com/sue445/git-base-study/commit/93106240e0f62f3dfb41cf21ddebda4a7b14d632

### ディレクトリ構成
```sh
$ tree -a .
.
└── .git
    ├── HEAD
    ├── config
    ├── description
    ├── hooks
    │   ├── applypatch-msg.sample
    │   ├── commit-msg.sample
    │   ├── post-update.sample
    │   ├── pre-applypatch.sample
    │   ├── pre-commit.sample
    │   ├── pre-push.sample
    │   ├── pre-rebase.sample
    │   ├── prepare-commit-msg.sample
    │   └── update.sample
    ├── info
    │   └── exclude
    ├── objects
    │   ├── info
    │   └── pack
    └── refs
        ├── heads
        └── tags
```

## .git/config
* `git config` した時の設定が保存されている
 * リポジトリごとの個人設定
* `git config --global` だと `~/.gitconfig` に保存される
 * リポジトリに依存しない個人設定

## .git/hooks
* `commit` などの特定のコマンド実行前後にフックされるシェルスクリプト
* ファイル名の `.sample` を削って使う
* ステータスコード0以外で終了するとコマンドの実行を中止することができる
 * 例）コミットに `binding.pry` が含まれていたらコミットさせないとか

### 例）git commit実行時
1. `git commit`
2. pre-commit hook
 * コミット内容の検証
3. prepare-commit-msg hook
 * コミットメッセージのデフォルト内容を変更する
4. コミットメッセージの編集
 * `vi` が立ち上がる
5. commit-msg hook
 * コミットメッセージの検証
6. コミットオブジェクトが作られる

---------------------------------------------------------------------------------------------
# git add
```
$ touch readme.txt && git add readme.txt
$ git st
## Initial commit on master
A  readme.txt
```

https://github.com/sue445/git-base-study/commit/4b4655b26eb81f495f5d6dd6a11ce667e74ae9aa

## .git/index
```sh
$ hexdump -C .git/index
00000000  44 49 52 43 00 00 00 02  00 00 00 01 51 c3 1b 83  |DIRC........Q...|
00000010  00 00 00 00 51 c3 1b 83  00 00 00 00 01 00 00 02  |....Q...........|
00000020  02 21 57 8c 00 00 81 a4  00 00 01 f5 00 00 00 14  |.!W.............|
00000030  00 00 00 00 e6 9d e2 9b  b2 d1 d6 43 4b 8b 29 ae  |...........CK.).|
00000040  77 5a d8 c2 e4 8c 53 91  00 0a 72 65 61 64 6d 65  |wZ....S...readme|
00000050  2e 74 78 74 00 00 00 00  00 00 00 00 a6 61 0c ce  |.txt.........a..|
00000060  46 1d b3 b7 de c6 62 90  2e ea 42 35 a4 3e 40 d6  |F.....b...B5.>@.|
00000070
```

---------------------------------------------------------------------------------------------
# git commit
```sh
$ git commit -am "initial commit"
[master (root-commit) 46657b9] initial commit
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 readme.txt
```

https://github.com/sue445/git-base-study/commit/c7a8579ce61f82ee2d48a03d21cb6961f1509391

## .git/COMMIT_EDITMSG
直前のコミットメッセージ
`git commit --amend` はこいつを編集してるらしい

## .git/refs/heads/master
masterブランチのコミットIDっぽい？

念のため `git log` コミットIDを確認する

```sh
$ git log
commit 46657b9ae9ea39f970c2a38ea6db4766762f5b29
Author: sue445 <sue445@sue445.net>
Date:   Fri Jun 21 00:19:18 2013 +0900

    initial commit
```

## .git/refs/HEAD
`git reflog` はこいつを見ているみたい

```sh
$ git reflog
46657b9 HEAD@{0}: reset: moving to HEAD^
25670bc HEAD@{1}: reset: moving to 25670bc6940eafb3d530d668e7256b8acbef51cd
46657b9 HEAD@{2}: reset: moving to HEAD^
25670bc HEAD@{3}: reset: moving to 25670bc6940eafb3d530d668e7256b8acbef51cd
46657b9 HEAD@{4}: reset: moving to HEAD^
25670bc HEAD@{5}: checkout: moving from new_branch to master
25670bc HEAD@{6}: checkout: moving from master to new_branch
25670bc HEAD@{7}: commit (amend): add text
1f7df6e HEAD@{8}: commit: add test
46657b9 HEAD@{9}: commit (initial): initial commit
```

---------------------------------------------------------------------------------------------
# もう一度commitしてみる

```sh
$ echo "1st line" >> readme.txt
$ git commit -am "add test" # typo
$ git commit --amend # add text に修正
```
https://github.com/sue445/git-base-study/commit/10b4c817d48db423caa7534ba0bf60abe2f8f829

---------------------------------------------------------------------------------------------
# git branch
```sh
$ git branch new_branch
```
https://github.com/sue445/git-base-study/commit/e4fc5df5e5d4fa68e8499d89634e52af1b037f77

---------------------------------------------------------------------------------------------
# git checkout
```sh
$ git checkout new_branch
Switched to branch 'new_branch'
```
https://github.com/sue445/git-base-study/commit/05c17d5101878e41d44f96b01cb30371c38e0596

---------------------------------------------------------------------------------------------
# git tag
```sh
$ git tag -a new_tag -m "tag description"
```
https://github.com/sue445/git-base-study/commit/ff28ce2cabbca95d73b9156b684702e104c8526b

---------------------------------------------------------------------------------------------
# git gc
```sh
$ git gc
```
https://github.com/sue445/git-base-study/commit/96ffd61f59ada2f873714f77685eb3eb5f18a4e4

---------------------------------------------------------------------------------------------
# git reset
`git reset` のオプションの違い(特に `--mixed` と `--soft` )がよく分からんかったので調べてみた

参考：[git reset についてもまとめてみる](http://d.hatena.ne.jp/murank/20110327/1301224770)

## git reset --soft
> HEAD の位置のみを変更する。インデックス、ワーキングツリーには影響なし。

```
$ git reset --soft HEAD^
$ git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#
#	modified:   readme.txt
#
```

https://github.com/sue445/git-base-study/commit/767dc62d135e37ca6638765478c2664a8d974805

## git reset --mixed
> HEAD の位置とインデックスを変更する。ワーキングツリーには影響なし。

```sh
$ git reset --mixed HEAD^
Unstaged changes after reset:
M	readme.txt
$ git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   readme.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
```
https://github.com/sue445/git-base-study/commit/2c56c560e995fd1120db3b653777fafe06fd0e69

## git reset --hard
> HEADの位置、インデックス、ワーキングツリーをすべて変更する。

```sh
$ git reset --hard HEAD^
HEAD is now at 46657b9 initial commit
```
https://github.com/sue445/git-base-study/commit/3d6b841dd48c17c5f524e02634e5a388fd9d3514

------------------------------
# git remote
```sh
git remote add origin git@github.com:sue445/git-base-study-git.git
```
https://github.com/sue445/git-base-study/commit/fb18e7259950b4fe4dd5d36f07ed0858b9c21c25

------------------------------
# git push
```sh
git push origin master
```
https://github.com/sue445/git-base-study/commit/f5ef784bf1cc41806afbeb7399773c0b28cee059

```sh
git push origin new_branch
```
https://github.com/sue445/git-base-study/commit/3d9d85c7d2f1ef604d23c3118180fa5d4f4a65cc

`git push --tags` (tagのpush)ではリポジトリは変化しない

---------------------------------------------------------------------------------------------
# まとめ
* gitには3つの状態がある(work tree, index, repository)
 * どのコマンドでどの状態が変化していることを意識できると、どういう風に戻せばいいか自然と分かってくる
* gitのリポジトリもgitで履歴管理できる
* .gitディレクトリの中は面白い

---------------------------------------------------------------------------------------------
# 参考文献
## 入門Git
http://www.amazon.co.jp/dp/4798023809 （黒い方）
Gitのメンテナが書いているだけあって **基礎的** な内部構造がかなり詳しく解説されている

## 「入門git」という全く別の本があるので注意ｗ
http://www.amazon.co.jp/dp/427406767X （白い方）

