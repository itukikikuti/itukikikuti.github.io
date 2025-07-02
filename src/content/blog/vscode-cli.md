---
title: Visual Studio Code Serverをインストールした
pubDate: 2025-07-02T15:33:00+09:00
tags: ['VSCode', '自宅サーバ']
---
VS CodeのTunnelを使ってリモートから自宅サーバへ接続したいので、Visual Studio Code Serverをインストールした。
```shell
mkdir -p ~/vscode-cli && cd ~/vscode-cli  # フォルダを作る
curl -L "https://code.visualstudio.com/sha/download?build=stable&os=cli-alpine-x64" -o vscode_cli.tar.gz  # とりあえずウェブサイトのダウンロードリンクからダウンロードしたけど、パッケージマネージャとかでダウンロードできないんだろうか…
tar -xzf vscode_cli.tar.gz
rm vscode_cli.tar.gz
mkdir -p ~/.local/bin
mv ~/vscode-cli/code ~/.local/bin/
export PATH="$HOME/.local/bin:$PATH"  # 環境変数追加
```
下記は動作確認。
```console
$ code --version
code 1.101.2 (commit 2901c5ac6db8a986a5666c3af51ff804d05af0d4)
```

下記のコマンドでトンネルを開通できるんだけどその前に、認証をする。
```console
$ code tunnel --accept-server-license-terms
*
* Visual Studio Code Server
*
* By using the software, you agree to
* the Visual Studio Code Server License Terms (https://aka.ms/vscode-server-license) and
* the Microsoft Privacy Statement (https://privacy.microsoft.com/en-US/privacystatement).
*
✔ How would you like to log in to Visual Studio Code? · GitHub Account
To grant access to the server, please log into https://github.com/login/device and use code XXXX-XXXX
✔ What would you like to call this machine? · xxxx
[2025-07-01 13:31:07] info Creating tunnel with the name: xxxx

Open this link in your browser https://vscode.dev/tunnel/xxxx/xxxx
```
うろ覚えだけど、最初に認証に使うアカウント(上記の例ではGitHubアカウント)を選択して、認証用のURLと認証コード(上記の例ではXXXX-XXXX)が出るので、URLにアクセスして、コードを入れる。
そのあと、マシンの名前を入力して完了。最後に表示されるURLにアクセスすればトンネルでサーバにアクセスできる🤗

このままだと、シェルを立ち上げっぱなしにしないとトンネルが切れてしまうので、systemdに登録して永続化する。
```shell
sudo nano /etc/systemd/system/code-tunnel.service
```
ファイルを作成して、下記の内容で保存する。ExecStartは、`which code`コマンドを実行した結果のパスにする。Userはユーザー名にする。
```ini
[Unit]
Description=VSCode Tunnel Service
After=network.target

[Service]
Type=simple
ExecStart=/xxxx/code tunnel --accept-server-license-terms
User=xxxx
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
ファイルを保存したら、下記のコマンドでサービスを実行する。
```shell
sudo systemctl daemon-reload
sudo systemctl enable code-tunnel
sudo systemctl start code-tunnel
sudo systemctl status code-tunnel
```
Activeが「active (running)」になってたらOK。これで永続化完了。
