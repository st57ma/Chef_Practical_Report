# 第4章:クックブックの活用			

## コミュニティクックブックはここ？
https://supermarket.chef.io/cookbooks<br>
apache2,nginx,Java,Pythonなどメジャーなソフトのクックブックがある。

## Berkshelf
* Chefにおける外部クックブックを管理するツール
* Berkshelfでクックブックのインポートで依存関係もダウンロードされる。

## Chef Soloで複数ノードを管理
#### ・VagrantのマルチVM機能
* VagrantにマルチVM機能がある
 * マルチVMにそれぞれのノードオブジェクトが必要

#### ・ロール
複数サーバでそれぞれのノードの用途が異なる場合はロールを使う

* ロールの作成(/roles/web.json)
<pre><code>{
   "name": "web",
   "chef_type": "role",
   "json_class": "Chef::Role",
   "run_list": [
     "recipe[git]",
     "recipe[apache2]"
   ]
}</code></pre>

* ロールの割り当て(ノードに記載　nodes/node01.json)
<pre><code>{
  "run_list":[
    "role[web]"
   ]
}</code></pre>

* ロールでAttributeを管理する
 * ランリストの他にAttributeを使うこともできる
 <pre><code>{
  "name": "web",
  "chef_type": "role",
  "json_class": "Chef::Role",
  "default_attributes": {
      "apache": {
          "listen_ports": [
            "80",
            "443"
          ]
      }
  },
  "override_attributes": {
      "apache": {
          "max_children" : "50"
      }
  },
  "run_list": [
      "recipe[git]",
      "recipe[apache2]"
  ]
}</code></pre>
default_attributesはロールを割り当てた側にAttributeが定義されていない場合に利用される
override_attributesは上書きする値

#### ・Environments
* 「開発」、「ステージング」、「プロダクション」といったノードの環境に応じて状態を定義できる機能
<> ロールは「Webサーバ」、「データベースサーバ」

* Environmentsの設定(environments/development.json)
 <pre><code>{
  "name": "development",
  "description":"Development environment",
  "chef_type": "environment",
  "json_class": "Chef::Environment",
  "default_attributes": {
    "apache": {
      "max_children": "10"
    }
  },
  "override_attributes": {}
}</code></pre>

* Nodeオブジェクト側でEnvironmentsを設定
 <pre><code>{
   "environment": "development",
   "run_list":[
     "role[web]"
   ]
 }</code></pre>

* Attributeの優先度
Nodeオブジェクト > ロール > Environments > レシピの中で定義されたAttribute > クックブック内のAttributeファイル

## 所感
この章は川口さんによると、
クックブックやロール、Environmentsなどをよく使うとのことなので、重点的に学習をした。
しかし、読み進めただけなので理解は浅いと思われるので、定期的に読み返したい。
