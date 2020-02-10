### 安装部署时间服务器chronyd

```javascript
yum install git epel-release -y
yum install python-pip -y
pip install ansible==2.6.18 netaddr==0.7.19 -i https://mirrors.aliyun.com/pypi/simple/
pip install pip --upgrade -i https://mirrors.aliyun.com/pypi/simple/
pip install --upgrade setuptools -i https://mirrors.aliyun.com/pypi/simple/

```

### 下载项目源码

```javascript
git clone https://github.com/luo964973791/chrony-server.git
mkdir -p /etc/ansible && mv chrony-server/* /etc/ansible
#修改一下三个置参数
vi /etc/ansible/hosts
[kube-master]
172.27.0.6
172.27.0.7

# work node(s)
[kube-node]
172.27.0.8
172.27.0.9

[chrony]
172.27.0.6
```

