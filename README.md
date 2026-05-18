# Ansible Role for node exporter with auto TLS configuration

[![CI](https://github.com/Asya-kawai/ansible-role-node-exporter/actions/workflows/ci.yml/badge.svg)](https://github.com/Asya-kawai/ansible-role-node-exporter/actions/workflows?query=workflow%3ACI)

## 概要

このAnsibleロールは、Prometheusの[Node Exporter](https://github.com/prometheus/node_exporter)をサーバに自動デプロイします。
TLSやBasic認証など実運用に必要な設定を容易に行えます。

> 本ロールは [prometheus-community/ansible](https://github.com/prometheus-community/ansible/tree/main/roles/node_exporter) の node_exporter ロールをフォークし、日本語ドキュメントや一部機能追加を行ったものです。
> **フォーク元の貢献者・ライセンス等は必ず[元リポジトリ](https://github.com/prometheus-community/ansible/tree/main/roles/node_exporter)をご参照ください。**

## 特徴・仕組み

- 任意バージョンのNode Exporterを自動インストール
- Collectorの有効/無効化やTextfile Collector用ディレクトリの自動作成
- systemdサービスとして有効化
- TLS証明書・鍵の自動配置および設定
- Basic認証ユーザーの設定（bcryptによる自動ハッシュ化）

## 主要変数例

`defaults/main.yml` も参照してください。

```yaml
# Node Exporterのバージョン
node_exporter_version: "1.11.1"
# 9100番で全インターフェース待受
node_exporter_web_listen_address: "0.0.0.0:9100"
# TLSサーバ設定例
node_exporter_tls_server_config:
  cert_file: /etc/node_exporter/tls.cert
  key_file: /etc/node_exporter/tls.key
# Basic認証ユーザー例
node_exporter_basic_auth_users:
  randomuser: examplepassword
# 有効化するCollector例
node_exporter_enabled_collectors:
  - systemd
  - textfile: { directory: "/var/lib/node_exporter" }
```

## 注意事項

- Basic認証を有効にする場合、`passlib`（`pip install passlib[bcrypt]`）が必要です。
- Macでデプロイする場合は`gnu-tar`が必要です（`brew install gnu-tar`）。
- 証明書・鍵ファイルは事前に用意してください。
- 変数の詳細は `defaults/main.yml` を参照してください。

## 実行例

### Playbook例

```yaml
- hosts: all
  become: yes
  roles:
    - node_exporter
  vars:
    # 証明書および鍵は自己署名で自動生成されます
    node_exporter_tls_server_config:
      cert_file: /etc/node_exporter/tls.cert
      key_file: /etc/node_exporter/tls.key
    node_exporter_basic_auth_users:
      randomuser: examplepassword
```

### Makefileによるテスト

```sh
# 依存インストール
make deps
# テスト用コンテナ作成
make create-nodes
# Playbook生成
make generate-playbook
# Inventory生成
make generate-inventory
# テスト実行
make test-role
# 後片付け
make destroy
```

## トラブルシューティング

詳細は [TROUBLESHOOTING.md](TROUBLESHOOTING.md) を参照してください。

## ライセンス

MIT License（詳細は[LICENSE](LICENSE)参照）。
