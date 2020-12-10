# Ansible 体験

Ansible で、ネットワーク機器のコンフィグを生成する処理を体験してたいただきます。

# 手順

## 1. SSH ログイン
ご自身のパソコンから TeraTerm などのターミナルソフトを利用して、以下のサーバーに SSH でログインします。

IPアドレス: `TODO:（当日修正予定）`

※ ユーザー名とパスワードは当日講師からご連絡します。


## 2.　Ansible 環境の有効化と作業ディレクトリの移動

ログイン後、以下コマンドで、Ansible 環境を有効化します。

```
[centos@ip-x-x-x-x ~]$ source ~/envs/ansible/bin/activate
```

※ 厳密には、Ansible インストール済みの Python の venv の有効化です。

プロンプトが以下のように、先頭に `(ansible)` が付加されることを確認します。

```
[centos@ip-x-x-x-x ~]$ source ~/envs/ansible/bin/activate
(ansible) [centos@ip-x-x-x-x ~]$ 
```

次に、生徒ごとに用意された作業ディレクトリ（`~/studentXXX/try-ansible`）に移動します。

`studnetXXX` の `XXX` 部分は、クラス番号1桁 + 学籍番号下2桁です。

例: 1組の下2桁が `01`であれば `student101`

```sh
(ansible) [centos@ip-172-31-5-153 ~]$ cd ~/studentXXX/try-ansible/   # XXX は生徒ごとに異なる
(ansible) [centos@ip-172-31-5-153 try-ansible]$
```


## 3. Playbook の修正（任意）

処理の内容を定義した Playbook `set_config.yml` を修正します。

なお、今回の環境には、エディターは `vi`、`vim`、`emacs`、`nano` をインストール済みです。

`vi` を使う場合は、以下のコマンドでファイルを開き、修正します。
```
vi set_config.yml
```

以下の `hogehoge1` の部分を任意の値に修正します。`description: hogehoge1` の `:` の後は半角スペースが必要な点に注意してください。

```yaml
#...(略)...
  vars:
    # 設定するインターフェース
    interfraces:
      - name: GigabitEthernet1
        address: 172.16.1.254
        mask: 255.255.255.0
        description: hogehoge1      # ★任意の値に修正する
      - name: GigabitEthernet2
        address: 172.16.2.254
        mask: 255.255.255.0
        description: hogehoge2
    # 設定する Syslog サーバーのリスト
    syslog_servers:
      - 10.0.1.1
      - 10.0.1.2
#...(略)...
```

【オプション課題】余裕がある方は、他の値（`: `の後ろ側は）も修正していただいても構いません。

Playbook `set_config.yml` を修正後、ファイルを保存します。



上記で定義した変数が、テンプレートファイル `config.j2` に適用されて、コンフィグが生成される仕組みです。

```
{% for i in interfraces %}
interface {{ i.name }}
 ip address {{ i.address }} {{ i.mask}}
 description {{ i.description }}
 no shutdown
 
{% endfor %}

{% for s in syslog_servers %}
logging host {{ s }}
{% endfor %}
```

## 4. Playbook の実行

以下のコマンドで、Playbook を実行します。

```sh
ansible-playbook -i inventory.ini set_config.yml
```

以下のように、最後の行に `ok=1` が表示されたら成功です。

```sh
(ansible) [centos@ip-x-x-x-x try-ansible]$ ansible-playbook -i inventory.ini set_config.yml 

PLAY [ios] ******************************************************************************************

TASK [generate config] ******************************************************************************
ok: [ios01]

PLAY RECAP ******************************************************************************************
ios01        : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0       

```

コンフィグファイル `config.txt` が生成されます。

## 5. 実行結果の確認

生成されたコンフィグファイル `config.txt` の内容を確認します。

```sh
cat config.txt          # 任意のエディタでも構いません
```

コンフィグファイルの内容が確認できれば作業完了です。


【オプション課題】余裕がある方は、Playbook `set_config.yml` の他の箇所は、テンプレートファイル `config.j2` も修正して、Playbook を再実行していただいても構いません。

# 補足

## その他の IOS モジュールについて

今回の Playbook は体験用に準備した一例です。ほかにも [Cisco IOS 対応モジュールは多数](https://docs.ansible.com/ansible/latest/collections/cisco/ios/index.html)あります。


## カスタマイズ方法
今回の Playbook は、コンフィグファイルの生成のみのため、コンフィグ投入までは行いません。
[カスタマイズ方法はこちら](./extra.md)。


## 参考資料
- Ansible 公式ドキュメント: https://docs.ansible.com/ansible/latest/index.html
- Ansible ではじめるネットワーク自動化:（[資料](https://www.slideshare.net/akira6592/20208-beyond-ansible/akira6592/20208-beyond-ansible)・[動画](https://www.youtube.com/watch?v=qQaTi3WAUs8)）