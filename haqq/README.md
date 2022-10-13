## Установка системы оповещения PANIC для вашей ноды для HAQQ Testnet

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

### Настройка файла .env


Далее нам потребуется знать IP нашего сервера, для этого есть команда:
```
curl ifconfig.me
```
Придумываем имя и пароль, прафим .env
```
nano/root/panic/.env
```

![](https://i.imgur.com/hzxTuoY.png)

Сохраняем комбинацией CTRL + X --- ENTER

### БОТ

Добавляем @BotFather в телеграм

![](https://i.imgur.com/YwEzs1s.png)

Производим интересный диалог с ботом, благо, требования его просты и вполне понятны

![](https://i.imgur.com/C2Ocj9b.png)



Бот даст нам ссылку на нашего бота, пишем ему Start 


Переходим по адрессу 

`api.telegram.org/bot<token>/getUpdates` 

,где <token> меняем на токен API, которым вежливо поделился с нами бот в последнем сообщении
  
   
