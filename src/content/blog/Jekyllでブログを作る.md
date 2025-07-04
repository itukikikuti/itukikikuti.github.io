---
title: Jekyllでブログを作る
pubDate: 2024-08-05T00:00:00+09:00
tags: ['Jekyll']
---

とりあえず、0から作るのが好きなのでテーマは使わない。

`_layouts`フォルダに`default.html`を作って、普通にhtmlを書く。
`{{`と`}}`で囲うと変数を使える。
https://jekyllrb-ja.github.io/docs/variables/

あと、`index.html`を作って、`default.html`を継承しないなら空のフロントマターを書いて、トップページを作る。
以下を書けば、とりあえず記事リストは出せる。

```HTML
<ul>
    {% for post in site.posts %}
    <li>
        <a href="/blog{{ post.url }}">{{ post.title }}</a>
    </li>
    {% endfor %}
</ul>
```

`_config.yml`に関しては、`title`と`url`と`timezone`を設定。
`timezone`を指定しないと、未来の日付と言われて記事が公開されない時間帯がある(朝とか)。
あとは、`permalink`は自分の好みで拡張子を消した。

```YAML
title: 菊池いつきログ
url: https://itukikikuti.github.io/blog/
permalink: /:year/:month/:day/:title
timezone: Asia/Tokyo
```

あと、Markdownの設定でURLを自動でリンクに出来るようにした。
https://github.com/github/jekyll-commonmark-ghpages

```YAML
markdown: CommonMarkGhPages
commonmark:
  options: ["UNSAFE", "SMART", "FOOTNOTES"]
  extensions: ["strikethrough", "autolink", "table", "tagfilter"]
```

jekyll-commonmark-ghpagesを使うんなら、ルートフォルダに以下の`Gemfile`を作る。

```Ruby
group :jekyll_plugins do
    gem 'jekyll-commonmark-ghpages'
end
```

`_posts`フォルダに記事のファイルを作る。
この記事でいうと、`2024-08-05-Jekyllでブログを作る.md`というファイル名で作る。

最終的にディレクトリ構造はこうなる。
- `_layouts`
  - `default.html`
- `_posts`
  - `9999-99-99-記事.md`
- `_config.yml`
- `Gemfile`
- `index.html`

とりあえず、Jekyllですることは最小限にして、フロントエンドの技術でどうにかしたい。
