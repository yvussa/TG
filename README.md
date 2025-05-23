# docker部署MTProxyTLS
1.安装docker
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```
2.安装xdd
```
apt-get install xxd
```
3.拉取镜像
```
docker pull ellermister/nginx-mtproxy:latest
```
4.添加伪装
```
domain="azure.microsoft.com"
```
## 5.自定义安装
A.部署nginx-mtproxy不添加TAG
```
docker run --name nginx-mtproxy -d -e secret="$secret" -e domain="$domain" -e ip_white_list="OFF" -p 8081:80 -p 8443:443 ellermister/nginx-mtproxy:latest
```
B.部署nginx-mtproxy 添加TAG
-获取secret
```
secret=$(head -c 16 /dev/urandom | xxd -ps)
```
查看secret
```
echo $secret
```
从 https://t.me/MTProxybot 获取tag
```
tag="你的atg"
```
安装
```
docker run --name nginx-mtproxy -d -e tag="$tag" -e secret="$secret" -e domain="$domain" -e ip_white_list="OFF" -p 8082:80 -p 8443:443 ellermister/nginx-mtproxy:latest
```
C.部署nginx-mtproxy添加白名单不添加TAG
```
docker run --name nginx-mtproxy -d -e secret="$secret" -e domain="$domain" -e ip_white_list="IP" -p 8083:80 -p 8443:443 ellermister/nginx-mtproxy:latest
```
ip_white_list 可选参数为:

IP 允许单个 IP 访问

IPSEG 允许 IP 段访问

OFF 允许所有 IP 访问

-p端口可自定义

http://ip/add.php

6.创建监控脚本
```
cat > /root/check_mtproxy.sh << 'EOF'
#!/bin/bash

# 检查 nginx-mtproxy 容器是否在运行中
if ! docker ps | grep -q nginx-mtproxy; then
    echo "$(date): nginx-mtproxy not running. Starting..." >> /var/log/mtproxy_monitor.log
    docker start nginx-mtproxy
fi
EOF
```
7.赋予脚本权限
```
chmod +x /root/check_mtproxy.sh
```
8.编辑crontab
```
crontab -e
```
尾部加入
```
* * * * * /root/check_mtproxy.sh >> /var/log/mtproxy_cron.log 2>&1
```
验证
```
crontab -l
```
查看配置
```
docker logs nginx-mtproxy
```
停止
```
docker stop nginx-mtproxy
```
开启
```
docker start nginx-mtproxy
```
删除
```
docker rm nginx-mtproxy
```
重新部署需要1停止2删除3添加伪装网站4复制安装内容即可
