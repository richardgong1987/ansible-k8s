# 一. Install almalinux-Minimal https://almalinux.org/get-almalinux/

    (In Mac os, m2, aarch64 , I should choose: https://almalinux.org/get-almalinux/#Incus-LXC-x86_64-10)

## 1). Install it in fusion VM Custom: 

CPU：2 cores
Memory：6144 MB（6GB）
Disk: 100G
Network adapter: Bridged Networking -> Wifi

u:root,password:asdf

Then,

Settings → CD/DVD → Disconnect / Remove ISO


reboot

```shell
  ssh root@192.168.2.145
  
  cat /etc/os-release
  
  uname -m
  
  ip a ( modern tool, ifconfig已经被Linux官方弃用)
  
  ip route

  hostnamectl set-hostname k3s-server-1
  
  swapon --show
  
  swapoff -a
  sed -i.bak '/\bswap\b/d' /etc/fstab
   
```        
```shell

## For study purpose , stop 

systemctl disable --now firewalld 2>/dev/null || true

```
## 2). Install k3s in VM
```shell
curl -sfL https://get.k3s.io | sh -

systemctl status k3s --no-pager
kubectl get nodes
kubectl get pods -A
```
get token,for client to connect:
```shell
cat /var/lib/rancher/k3s/server/node-token

5ea5f7ef6977d3f5010403ffabcc25f5477722633e1a22f92f486f99436de6a5::server:1318c7daf16e6c02f9d37c0bb014ed47
```
## 3). bind ip
```shell
mkdir -p /etc/rancher/k3s
vi /etc/rancher/k3s/config.yaml
```

```yaml
node-ip: 192.168.2.145
advertise-address: 192.168.2.145
tls-san:
  - 192.168.2.145
  - k3s-server-1
```
```yaml
systemctl restart k3s
```

在Mac上安装
```shell
brew install kubectl
kubectl version --client
```

## 4). 让你从 Mac 直接 kubectl 管理（不需要每次进 VM）
```yaml
cat /etc/rancher/k3s/k3s.yml
```
把里面的：
•	server: https://127.0.0.1:6443
改成：
•	server: https://192.168.2.145:6443

然后把这个文件内容保存到你 Mac 的：
```shell
~/.kube/config
```
## Install k3s-agent-1, k3s-agent-2

**VMware Fusion 里怎么复制（推荐：Full Clone）**

在 Fusion 中对这台 VM：

右键 → Duplicate / Clone（复制/克隆）

选择：Full Clone（完整克隆） ✅
（不要 linked clone，后面磁盘/性能/稳定性更麻烦）

如果它问你 “Moved / Copied?”
```shell
hostnamectl set-hostname k3s-agent-1

/usr/local/bin/k3s-uninstall.sh 2>/dev/null || true
/usr/local/bin/k3s-agent-uninstall.sh 2>/dev/null || true
rm -rf /etc/rancher /var/lib/rancher
reboot

```
Copy k3s-agent-1 to k3s-agent-2

```shell
hostnamectl set-hostname k3s-agent-2
```


