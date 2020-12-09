# 参考

## カスタマイズ方法

今回の Playbook は、コンフィグファイルの生成のみのため、コンフィグ投入までは行いません。

もし実際にネットワーク機器へのコンフィグ投入したい場合は、以下の3つのファイルを変更します。ネットワーク機器側は予め SSHでログインできるようにしておく必要があります。


### 1. 変数定義ファイル `group_vars/ios.yml`
変数 `ansible_user` と `ansible_password` の値をネットワーク機器へのログインユーザー名、パスワードに変更する。

例: ユーザー名 `admin`、パスワード `password` の場合
```yaml
---
ansible_network_os: ios
ansible_connection: network_cli
ansible_user: admin
ansible_password: password
```

ここで指定するユーザー名は、特権ユーザーの想定です。


### 2. インベントリファイル `inventory.ini` 

変数 `ansible_host` の値をネットワーク機器の IPアドレスに変更する。

例: ネットワーク機器の IPアドレスが `172.16.0.254` の場場合
```ini
ios01 ansible_host=172.16.0.254
```

### 3. Playbook `yml`
実行しないための指定である `tags: never` の行を削除します。


### Playbook の実行
Playbook の実行コマンドは変更ありません。
```
ansible-playbook -i inventory.ini set_config.yml
```

## 参考資料
- Ansible 公式ドキュメント: https://docs.ansible.com/ansible/latest/index.html