# ansible + k3s + before

## Prepare:
#### 1).Run in vm linux
```shell
dnf -y install openssh-server python3 sudo
systemctl enable --now sshd
 
useradd -m -s /bin/bash devops
usermod -aG wheel devops



mkdir -p /home/devops/.ssh
chmod 700 /home/devops/.ssh

install -d -m 700 -o devops -g devops /home/devops/.ssh
echo 'PASTE_PUBLIC_KEY_HERE' | tee -a /home/devops/.ssh/authorized_keys >/dev/null
chown devops:devops /home/devops/.ssh/authorized_keys

## 把mac public key放进来. 请用id_ed25519.pub的.这个比较好.为了免登陆
chmod 600 /home/devops/.ssh/authorized_keys

#用于devops提权时,免密码
visudo -f /etc/sudoers.d/devops
#输入以下配置,用于devops提权时,免密码,不然ansible会失败.
devops ALL=(ALL) NOPASSWD: ALL

# 验证:
su - devops
sudo whoami
```

#### 2). Run In Mac os:
```shell
# 试试看.

ssh devops@192.168.2.145
ssh devops@192.168.2.146
ssh devops@192.168.2.147

# 也可以顺便把root也塞进模板机：

ssh-copy-id root@192.168.2.145
ssh-copy-id root@192.168.2.146
ssh-copy-id root@192.168.2.147

```

# 二. Run ansible, install k3s in agent-1,agent-2
```shell
cd ansible

ansible-playbook playbooks/site.yml

```