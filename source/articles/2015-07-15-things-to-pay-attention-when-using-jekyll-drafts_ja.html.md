---
title: "JekyllでDrafts機能を使うときに気をつけること"
lead: "便利だが、一つに統一されて日が浅い上、誰もが使ってるわけではないので、いくつか落とし穴がある。"
type: "thought"
category: web
lang: ja
tags: [jekyll]
---

Drafts機能は、ポストをかきながらブラウザーから確認するのに便利だが、あくまでポスト自体とは扱いが違うため、いくつか注意しなければいけないことがある。

READMORE

Jekyll Version1.x.xではいくつかあった草稿の管理方法も、`_drafts`以下に日付が名前に含まれていない記事を置く、という方法に統一された。これには、YAML headerを加える方法と違いポストのファイルを名前以外草稿と本稿で同じできたり、`_config.yaml`で`future: false`にして未来の日付を設定する方法と違って投稿予定日を無理に決めたりする必要がない。詳細は[Jekyllのドキュメンテーション](http://jekyllrb.com/docs/drafts/)を参考にしてほしい。

ひとつのポストを書き上げるというのはなかなかの仕事であり、中途半端に書かれたポストが公開前とはいえ増えていくのを見るに耐えず、私も数日前に使い始めた。もちろん、非常に便利なのだが、気を配っていないと更新が反映されず、デバッグに時間を費やすはめになる点をいくつか見つけたので、共有したいと思う。

## オプションを忘れると反映されない
まず、当たり前だが一番重要な点である。`_drafts`下にあるポストを反映するためには、`--drafts`オプションを指定しないといけない。そんなことはわかっているのだが、差分buildを使いつつ、自動buildを切っている私の場合、インデックスを更新したいときなど時々手動で`jekyll build -f`を実行しないといけない。その時にオプションを付け忘れると、なぜ一覧にポストが現れないのか悩む羽目になる。

対策としては、`gulp`や`rake`、`sh`や`alias`など、何でも良いから、間違えないようのないコマンドを作ってしまうことだろう。私の場合は、ローカルで走らせている分にはほとんど常に草稿が表示されていても問題ないので、新しい短めのgulpタスクを作ることにした。

## 日付をまたぐとURLが変更される
Drafts機能のひとつ良い所は、草稿に自動で最新の日付を適用してくれるところである。ところが、24時をまたぐ形で作業をしていると、作業中のポストに、新しい日付に基づいたURLが設定されてしまう。不便なことに、`_site`以下の古いファイルは自動で消去されたりしないので、いくらブラウザーの更新ボタンを押しても、ポストのファイルに施した変更が反映されないという問題に悩まされることになる。差分buildを使っているとインデックスも古い日付ベースのままなので、ブラウザ上から新しいURLにたどり着くには、手動でURLの日付部分を書き換えたりしないといけない。

抜本的な対策としては、Jekyll側の対応が不可欠である。サーバー起動中は草稿の日付を変えないとか、`_site`の古いファイルを消去するようにするあたりが妥当だろうか。

今のところは対症療法として、日付をまたまたいだら一旦差分buildを切ってbuildし直し、インデックスページからポストに改めてアクセスするようにしている。

## ポストに対してのデフォルトが適用されない
`_config.yaml`に記述する`default`におけるtypeは、ポスト用と草稿用で`posts`と`drafts`にそれぞれ分かれている。

もしdefaultに依存した記述をしていると、草稿にそれが反映されないことに悩むことになる。私の場合は結局丸写しするはめになったのだが、ポストとドラフトで違うdefaultを設定するケースというのはそんなに多いのだろうか。あるいは、typeにorで設定できればまだ良かったのだが。

