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
[chrony]
172.27.0.6


#同步时间服务器
ansible-playbook /etc/ansible/roles/chrony/chrony.yml

#docker使用ntp方法
docker run --restart=always --privileged -itd --cap-add SYS_TIME --name chrony --publish 123:123/udp alvistack/chrony-3.5
```

