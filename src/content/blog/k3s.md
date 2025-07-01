---
title: 自宅サーバにK3sをインストールした
pubDate: 2025-06-30T22:07:00+09:00
tags: ['Kubernetes', '自宅サーバ']
---
Kubernetesを勉強するために、簡易版のK3sをインストールした。
めっちゃ簡単だった。

インストールは、このコマンドを実行するだけ。
```console
$ curl -sfL https://get.k3s.io | sh -
```
このコマンドでActiveが「active (running)」になってたら、動作してる。
```console
$ sudo systemctl status k3s
```

次に、kubectlコマンドのパスを通す。
```console
$ kubectl get nodes
WARN[0000] Unable to read /etc/rancher/k3s/k3s.yaml, please start server with --write-kubeconfig-mode or --write-kubeconfig-group to modify kube config permissions
error: error loading config file "/etc/rancher/k3s/k3s.yaml": open /etc/rancher/k3s/k3s.yaml: permission denied
```
まだこうなってコマンドが使えない。
```console
$ mkdir -p ~/.kube
$ sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
$ sudo chown $(id -u):$(id -g) ~/.kube/config  # 権限を設定してる。
$ chmod 600 ~/.kube/config                     # 権限を設定してる。
$ nano ~/.kube/config                          # あたりまえだけど、nanoじゃなくてもいい。
```
configファイルを👇のように編集する。
```yaml
clusters:
- cluster:
    server: https://127.0.0.1:6443 # これをK3sをインストールした端末のIPにする。
```
```console
$ echo 'export KUBECONFIG=~/.kube/config' >> ~/.bashrc
$ source ~/.bashrc
$ kubectl get nodes
NAME     STATUS   ROLES                  AGE     VERSION
ubuntu   Ready    control-plane,master   6m51s   v1.32.5+k3s1
```
これで、kubectlが使えるようになる。
ちなみに、Windowsでもwingetでkubectlをインストールできる。
```powershell
winget install -e --id Kubernetes.kubectl
```
そのあと、ユーザーフォルダの直下に.kubeフォルダを作って、configファイルを作って、中身をさっきのIPを変えたYAMLにすれば、Windowsからでもkubectlが使える。
