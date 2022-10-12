## Установка системы оповещения PANIC для вашей ноды на примере HAQQ Testnet

### Установка утилит 
```
  apt update && apt upgrade -y
  sudo apt install git
  
  ```
#### Установка Docker

```
cd $HOME 
apt -qq purge docker docker-engine docker.io containerd docker-compose -y 
rm /usr/bin/docker-compose /usr/local/bin/docker-compose 
```
_не обращайте внимание на возможно появившиеся ошибки_
```
curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh && systemctl restart docker 
curl -SL https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose 
chmod +x /usr/local/bin/docker-compose && ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose 
```
### Установка PANIC
```
git clone https://github.com/SimplyVC/panic
cd panic
```
