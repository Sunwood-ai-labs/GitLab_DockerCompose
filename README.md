# GitLab_DockerCompose

このプロジェクトは、Docker Composeを使用してGitLabをセットアップします。GitLabは、ソースコードのバージョン管理やコラボレーションを行うためのツールです。


## Docker Composeファイルについて

Docker Composeファイルは、GitLabのコンテナを起動するために必要な設定を含んでいます。以下がそのファイルの内容です。 
- `version: '3.6'`：Docker Composeファイルのバージョンを指定します。 
- `services`：起動するサービスを定義します。ここでは`web`という名前のサービスがあります。 
- `image: 'gitlab/gitlab-ee:latest'`：使用するGitLabのイメージとタグを指定します。 
- `restart: always`：コンテナが停止した場合に常に再起動するように設定します。 
- `environment`：GitLabの設定を行います。例えば、`external_url`でGitLabのアクセスURLを設定します。 
- `ports`：ホストとコンテナのポートマッピングを設定します。この例では、ホストの8929ポートがコンテナの80ポートに、ホストの2224ポートがコンテナの22ポートに割り当てられています。 
- `volumes`：ホストとコンテナのデータボリュームをマウントします。これにより、コンテナが再起動してもデータが保持されます。 
- `shm_size: '256m'`：共有メモリのサイズを設定します。


```

version: '3.6'
services:
  web:
    image: 'gitlab/gitlab-ee:latest'
    restart: always
    # hostname: 'gitlab.example.com'
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://192.168.0.131:8929'
        gitlab_rails['gitlab_shell_ssh_port'] = 2224
        nginx['listen_port'] = 80
        # external_url 'https://gitlab.example.com'
        # Add any other gitlab.rb configuration here, each on its own line
    ports:
      - '8929:80'
      - '2224:22'
    volumes:
      - ./config:/etc/gitlab'
      - './logs:/var/log/gitlab'
      - './data:/var/opt/gitlab'
    shm_size: '256m'

```

## 初期パスワードの確認方法

GitLabを初めて起動すると、自動的に生成された初期パスワードがあります。このパスワードは、以下のコマンドを実行することで確認できます。 
1. Docker Composeで`web`サービスのコンテナにアクセスします:

```bash
sudo docker-compose exec web /bin/bash
``` 
2. 初期パスワードが書かれたファイルを表示します:

```bash
cat /etc/gitlab/initial_root_password
```

```

maki@maki-Green:~/Documents/GitLab_DockerCompose$ sudo docker-compose exec  web /bin/bash

root@11e6bfd28182:/# cat /etc/gitlab/initial_root_password 
# WARNING: This value is valid only in the following conditions
#          1. If provided manually (either via `GITLAB_ROOT_PASSWORD` environment variable or via `gitlab_rails['initial_root_password']` setting in `gitlab.rb`, it was provided before database was seeded for the first time (usually, the first reconfigure run).
#          2. Password hasn't been changed manually, either via UI or via command line.
#
#          If the password shown here doesn't work, you must reset the admin password following https://docs.gitlab.com/ee/security/reset_user_password.html#reset-your-root-password.

Password: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

# NOTE: This file will be automatically deleted in the first reconfigure run after 24 hours.
root@11e6bfd28182:/# 

```

このファイルには、初期パスワードが記載されています。ただし、このパスワードは24時間後に削除されますので、注意してください。

これらの手順に従って、GitLabをセットアップし、初期パスワードを取得することができます。



