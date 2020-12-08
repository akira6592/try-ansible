# try-ansible


# 参考


## カスタマイズ方法

実際にネットワーク機器へのコンフィグ投入したい場合は、以下の3つのファイルを変更します。

なお、ネットワーク機器側は予めSSHでログインできることが前提です。


### 1. 変数定義ファイル `group_vars/ios.yml`
変数 `ansible_user` と `ansible_password` の値をネットワーク機器へのログインユーザー名、パスワードに変更

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

変数 `ansible_host` の値をネットワーク機器の IPアドレスに変更

例: ネットワーク機器の IPアドレスが `172.16.0.254` の場場合
```ini
ios01 ansible_host=172.16.0.254
```

### 3. Playbook `yml`
実行しないための指定である `tags: never` の行を削除します。


### Playbook の実行
Playbook の実行コマンドは変更ありません。
```
ansible-playbook -i inventory.ini generate_config.yml
```

## 参考資料
- Ansible 公式ドキュメント: https://docs.ansible.com/ansible/latest/index.html