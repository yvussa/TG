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
docker run --name nginx-mtproxy -d \
  -e secret="$secret" \
  -e domain="$domain" \
  -e ip_white_list="OFF" \
  -p 10000-60000:80 \
  -p 10000-60000:443 \
  ellermister/nginx-mtproxy:latest
```
- 查看配置
```
docker logs nginx-mtproxy
```
