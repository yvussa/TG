# TG代理
- 安装docker
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```
- 安装xdd
```
apt-get install xxd
```
- 拉取镜像
```
docker pull ellermister/nginx-mtproxy:latest
```
- 添加伪装
```
domain="azure.microsoft.com"
```
- 安装
```
docker run --name nginx-mtproxy -d --restart unless-stopped -e secret="$secret" -e domain="$domain" -e ip_white_list="OFF" -p 8888:80 -p 88:443 ellermister/nginx-mtproxy:latest
```
- 创建监控脚本
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
- 赋予脚本权限
```
chmod +x /root/check_mtproxy.sh
```
- 编辑crontab
```
crontab -e
```
- 尾部加入
```
* * * * * /root/check_mtproxy.sh >> /var/log/mtproxy_cron.log 2>&1
```
- 验证
```
crontab -l
```
- 查看配置
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
停止并删除
```
docker stop nginx-mtproxy
docker rm nginx-mtproxy
```
