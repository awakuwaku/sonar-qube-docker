# sonar-qube-docker
SonarQubeをDocker Compose起動


# 前提
- [SonarQube (8.4.2)](https://www.sonarqube.org/)
- [PostgreSQL (13.0)](https://www.postgresql.org/)
- [Docker (19.03.13)](https://www.docker.com/)
- [Docker Compose (1.27.4)](https://docs.docker.com/compose/)


# ディレクトリ構成
```
├── docker-compose.yml  : SonarQube ServerのDocker Compose定義
├── data                : SonarQube ServerのDockerコンテナ動作に必要なデータ
│      ├── sonarqube    : SonarQube Serverに必要なPlugin
│      ├── postgresql   : SonarQube Server用PostgreSQLのデータ
```

# 利用方法

## SonarQube Serverの起動

1. SonarQube Server を起動する。

  ```shell
  docker-compose up -d --force-recreate sonarqube-server
  ```

  注意事項) SonarQube Serverの起動はDocker Composeの定義にて、 `PostgreSQL -> SonarQube Server` の順に起動するが、初回のコンテナ作成や実行する端末スペックが低い場合、 `PostgreSQL` の起動完了前に `SonarQube Server` がDB接続を行い、起動に失敗することがある。 その場合、上記のSonarQube Server を起動のコマンドを再度実行し、コンテナを再起動すること。


## SonarQube ServerのPlugin導入

導入方法としては以下の2通りがある。
- SonarQube Marketplaceからのインストール 

  以下参照
  
  https://docs.sonarqube.org/7.4/instance-administration/marketplace/ 

- SonarQube のPluginディレクトリにPluginを配置

  以下ディレクトリ配下に導入したいPluginを格納し、SonarQube Serverを再起動し反映
  
  ./data/sonarqube/extensions/plugin

SonarQubeを日本語化したい場合は、以下Pluginを導入する
- Japanese Pack : http://www.sonarplugins.com/l10nja


## SonarQube Serverの解析ルール・結果の共有

1. SonarQube Server を起動する。

  ```shell
  docker-compose up -d --force-recreate sonarqube-server
  ```

2. SonarQube Server のアクセスURLに管理者ユーザにてログインし解析ルールを変更する。

3. SonarQube Server用PostgreSQLのデータをダンプする。

  ```shell
  docker exec postgres-sonarqube sh -c 'pg_dump -U sonar' > ./data/postgresql/init/1_postgresql_dump.sql
  ```

  注意事項) SonarQube Serverの解析ルールはPostgreSQLのデータとして保持されるため、PostgreSQLのデータをダンプし、コンテナ作成時にロードさせることで反映させる。


## SonarQube Serverのアクセス情報

- アクセスURL：<http://localhost:9000/sonarqube/>
- 管理者ユーザ/パスワード：admin/admin
