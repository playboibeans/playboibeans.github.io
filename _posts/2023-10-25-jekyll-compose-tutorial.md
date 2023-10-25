---
layout: post
title: Jekyll Compose 擴充功能介紹
date: 2023-10-25 14:25 +0800
description: Jekyll Compose 教學
categories: [Blog]
tags: [jekyll] 
---
# Jekyll Compose 
在運用 jekyll 撰寫部落格的時候我們會需要在文章 md 檔的開頭手動註明 **Front Matter**<br>
例如這邊文章就需要列出：<br>
```markdown
---
layout: post
title: Jekyll Compose 擴充功能介紹
date: 2023-10-25 14:25 +0800
categories: [Blog]
tags: [jekyll] 
---
```
而且必須將文章檔名設定為 **YYYY-MM-DD-TITLE.EXTENSION** 的格式<br>
例如這邊文章的檔案名稱就是：<br>
`2023-10-25-jekyll-compose-tutorial.md`<br><br>
說實在每次寫文章都要重新打一次有點麻煩<br>
這時候我們就可以使用 [Jekyll Compose](https://github.com/jekyll/jekyll-compose) 這個擴充功能來使用像 Hexo 一樣的自動產生模板<br>

# 使用方式
首先我們要先在部落格資料夾中的 Gemfile 中加上這條：<br>
```
gem 'jekyll-compose', group: [:jekyll_plugins]
```
然候在 terminal 中執行
```shell
$ bundle
```
完成後如果我們執行 `bundle exec jekyll help` 就能看到多出了幾個選項可以使用
```shell
  draft      # 創建一篇草稿
  post       # 創建一篇文章
  publish    # 將草稿文章移動到 _post 資料夾並發表出去
  unpublish  # 將已發表文章移動回草稿
  page       # 創建一個頁面，例如關於我
```
如果要新增一篇文章可以使用以下指令
```shell
$ bundle exec jekyll post "My New Post"
```
之後在 _posts 資料夾中就會得到一個叫做 `2023-10-25-my-new-post.md` 的檔案<br>
並且會自動帶入預設的 Front Matter：
```md
---
layout: post
title: My New Post
date: 2023-10-25 15:26 +0800
---
```
# 自訂選項
基本上這樣子就完成了 jekyll compose 的設定和使用<br>
但如果覺得預設的 Front Matter 太精簡的話也可以自訂成喜歡的樣子<br>
首先我們需要在 _config.yml 檔案中新增以下內容：
```yml
jekyll_compose:
  default_front_matter:
```
然後再依需要的功能新增設定進去，例如：
```yml
jekyll_compose:
  default_front_matter:
    drafts:
      description:
      categories: []
      tags: []
    posts:
      description:
      categories: []
      tags: []
```
其中 description 可以為文章新增簡介<br>
categories 可以填入分類<br>
tags 則可加入標籤<br>
更多能增加到 Front Matter 中的變數可以參考 [Hugo 這篇文章](https://gohugo.io/content-management/front-matter/#front-matter-variables)<br>