---
title: "初歩的なミス～Zenn予約投稿について～"
emoji: "📱"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["zenn", "zenncli"]
published: true
published_at: 2024-09-22 20:00
---
## 大前提
mdファイルに予約投稿日時を指定することで指定した日時に公開されます。

```md:zenn-article.md
---
title: "初歩的なミス～Zenn予約投稿について～"
emoji: "📱"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["zenn", "zenncli"]
published: true
published_at: 2024-09-22 20:00
---
Hello world!
```

まさに予約投稿の指定が"published_at"なのですが、
指定したのにも関わらず、速公開されてしまいました。

これは自分のタイポが原因でした。。。恥ずかしい。  
"published at"とスペースで区切っていたため、うまく認識されませんでした。

私と同じようなミスをした人はいないと思いますが、、  
誰かの助けになればうれしいです。。
