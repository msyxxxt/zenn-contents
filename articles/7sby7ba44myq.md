---
title: "コピペでrails開発環境の構築をする"
emoji: "🚊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Ruby", "Rails", "Docker"]
published: true
published_at: 2024-10-05 08:00
---

## 構築手順
1.プロジェクトディレクトリを作成する
```Bash
mkdir minimum-rails-kit
cd minimum-rails-kit
```

2.GemfileとGemfile.lockを作成する
```Bash
touch Gemfile Gemfile.lock

vi Gemfile

source 'https://rubygems.org'
gem 'rails', '~>7.0.0'
```

3.Dockerfileを作成する
```Docker:Dockerfile
FROM ruby:3.1

# 必要なパッケージをインストール
RUN apt-get update -qq && apt-get install -y nodejs postgresql-client

# アプリケーションディレクトリを作成
WORKDIR /myapp

# GemfileとGemfile.lockをコピー
COPY Gemfile /myapp/Gemfile
COPY Gemfile.lock /myapp/Gemfile.lock

# Gemをインストール
RUN bundle install

# アプリケーションのソースコードをコピー
COPY . /myapp

# サーバーを起動
CMD ["rails", "server", "-b", "0.0.0.0"]
```

4.docker-compose.ymlを作成する
```yml:docker-compose.yml
version: '3'
services:
  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: password
    volumes:
      - ./tmp/db:/var/lib/postgresql/data
  web:
    build: .
    command: bundle exec rails server -b 0.0.0.0
    volumes:
      - {STEP1で作成したプロジェクトディレクトリの絶対パス}/minimum-rails-kit:/myapp
    ports:
      - "3000:3000"
    depends_on:
      - db
```

※ちなみにwebのvolumesでローカルのディレクトリをコンテナに反映させているので実質ホットリロード
※ファイルは絶対パスで書いておこう(環境変数を使ってシンプルに書いておくと◯)

5.新しいRailsアプリケーションを生成
```Bash
docker-compose run web rails new . --force --database=postgresql --skip-bundle
```

※正しくrails newされないとデータベースの設定を更新できない

6.データベース設定を更新
```yml:config/database.yml
default: &default
  adapter: postgresql
  encoding: unicode
  host: db
  username: postgres
  password: password
  pool: 5

development:
  <<: *default
  database: myapp_development

test:
  <<: *default
  database: myapp_test
```

7.Dockerイメージをビルドする
```Bash
docker-compose build
```

※rails newした後でイメージをビルドすることで、最新のGemfileに基づいて依存関係をクリアできる

8.データベースを作成
```Bash
docker-compose run web rake db:create
```

9.アプリケーション起動
```Bash
docker-compose up
```

10.アプリケーションにアクセス
```
http://localhost:3000
```

## まとめ
今回は最小限の開発環境のため、rails特有の依存関係のもつれみたいなものは発生しません。
しかし、こちらをベースに環境を何度もスクラップ&ビルドしてみてください。
Dockerはこの気軽さが開発者のストレスを少しばかりか軽減していますよね〜
