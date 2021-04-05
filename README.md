# rails6_setup_with_docker_using_wsl


## 説明
こちらのテンプレートは、WindowsのWSL2上にDockerコンテナを作って、さらにその上でRailsの最新versionとRuby3.0を使うために作ったものです。
Macでも問題なく動くかもしれませんが、WindowsのWSL2上で作ったものなので自信はありません。

## 使い方

### １からrailsのプロジェクトを作成する場合
まず、以下のコマンドを入力します。

```
git clone https://github.com/nakamon-death/rails6_setup_with_docker_using_wsl
```

ローカル端末にクローンされたフォルダ名は好きなものに変えてください。この説明では、rails6_setup_with_docker_using_wslというフォルダ名を想定して書いているので、その部分の文字は変更したものに置き換えてください。

ローカルにクローンした後は、以下のコマンドをうって、rails6_setup_with_docker_using_wslのフォルダに移動し、dockerコンテナを構築してコンテナ上でrailsのプロジェクトを作ります。


```
cd rails6_setup_with_docker_using_wsl
docker-compose run web rails new . --force --no-deps --database=mysql --skip-test --webpacker
```

処理が終わるとsrcフォルダ内のGemfileとGemfile.lockが書き変わっているので、以下のコマンドを打ち、dockerのimageを作り直します。

```
docker-compose build
```

その後、src/config/database.ymlファイルの以下の部分を変更します。
```
**Before**

default: &default
  adapter: mysql2
  encoding: utf8mb4
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password: 
  host: localhost

**After**

default: &default
  adapter: mysql2
  encoding: utf8mb4
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: <%= ENV.fetch("MYSQL_USERNAME", "root") %>
  password: <%= ENV.fetch("MYSQL_PASSWORD", "password") %>
  host: <%= ENV.fetch("MYSQL_HOST", "db") %>
```


dockerコンテナ上でrailsで使用するデータベースを作成します。

```
docker-compose run web rails db:create
```

しばらく待つと正常にデータベースが作成されると思うので、その後以下のコマンドを実行します。

```
docker-compose up
```


http://localhost:3000/

こちらのURLにアクセスし、「Yay! You're on Rails」と表示されたら成功です。





