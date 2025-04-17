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



#场景：方案一、服务器A在本地没公网IP但是可以上网,服务器B不能上网有公网IP,通过服务器A ssh到服务器B反向代理来同步时间.
服务器A: 可以上网的服务器执行
docker run --restart=always --privileged -itd --cap-add SYS_TIME --name chrony --publish 123:123/udp alvistack/chrony-3.5
yum install socat -y
socat TCP-LISTEN:123,fork,reuseaddr UDP:localhost:123
ssh -R 123:localhost:123 root@115.231.181.121 -N


服务器B: 不可以上网的服务器执行
yum install socat -y
socat UDP-LISTEN:123,fork,reuseaddr TCP:localhost:123
ntpdate -u 127.0.0.1
chronyd -q 'server 127.0.0.1 iburst'



#场景：方案二
#有公网IP没互联网的机器执行
wget https://github.com/fatedier/frp/releases/download/v0.38.0/frp_0.38.0_linux_amd64.tar.gz && tar xzf frp_0.38.0_linux_amd64.tar.gz && cd frp_0.38.0_linux_amd64
cat > frps.ini << EOF
[common]
bind_port = 7000
bind_udp_port = 7001
allow_ports = 123
EOF

# 启动 frp 服务端
./frps -c frps.ini



#本地没公网IP,可以上网的机器执行
wget https://github.com/fatedier/frp/releases/download/v0.38.0/frp_0.38.0_linux_amd64.tar.gz && tar xzf frp_0.38.0_linux_amd64.tar.gz && cd frp_0.38.0_linux_amd64
cat > frpc.ini << EOF
[common]
server_addr = 公网IP
server_port = 7000

[ntp-udp]
type = udp
local_ip = pool.ntp.org
local_port = 123
remote_port = 123
EOF

./frpc -c frpc.ini


ntpdate -u 127.0.0.1



```

