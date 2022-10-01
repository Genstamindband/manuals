# Установка нескольких нод Subspace на одном сервере
Данный мануал применим к Docker версии.

## Устанавливаем ноду 1
### Создание кошелька
Первым делом необходимо сгенерировать кошелек на [сайте](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Feu-2.gemini-2a.subspace.network%2Fws#/accounts) 

### Подготовка сервера

```
sudo -i 

apt -qq update && apt -qq upgrade -y && apt -qq install curl wget jq -y 
```

### Установка 

#### Установка Docker

```
cd $HOME 
apt -qq purge docker docker-engine docker.io containerd docker-compose -y 
rm /usr/bin/docker-compose /usr/local/bin/docker-compose 
curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh && systemctl restart docker 
curl -SL https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose 
chmod +x /usr/local/bin/docker-compose && ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose 
```

### Создаем папку в домашней директории для ноды 1.
```
cd $HOME
mkdir subspace1
```
#### Переходим в созданную папку
```
cd subspace1
```
#### Скачиваем docker-compose.yml (_важно находится при этом в папке ноды 1_)
```
wget -qO - https://raw.githubusercontent.com/Genstamindband/manuals/main/subspace/subspace-docker/docker-compose.yml | envsubst > docker-compose.yml
```
### Правим docker-compose.yml

Через проводник в терминале или с помощью `nano` переходим к нужному файлу

`nano /root/subspace1/docker-compose.yml`

На этом этапе нам необходимо отредактировать docker-compose.yml, вписав туда имя ноды, адрес кошелька и предпочитаемый размер plot 

(_по умолчанию в данном конфиге указано 25GB_)

Для сохранения правок необходимо нажать CTRL + X -> ENTER

Меняем имя ноды:

![alt text](https://i.imgur.com/NfcXmgC.png)

Меняем адресс кошелька и размер plot:

![alt text](https://i.imgur.com/iBZMcHH.png)



#### Запускаем контейнеры (_флаг -d означает, что контейнеры останутся работать в фоновом режиме_)
```
docker-compose up -d
```

#### Смотрим логи (_опять же, обязательно из папки с нодой_)
```
docker-compose logs --tail=1000 -f
```

Заветные логи выглядят так:

![alt text](https://i.imgur.com/vwOBdDc.png)

## Устанавливаем ноду 2

### Создание кошелька 2
Генерируем второй кошелек на [сайте](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Feu-2.gemini-2a.subspace.network%2Fws#/accounts)

### Создаем папку в домашней директории для ноды 2 и переходим в нее.

```
cd $HOME
mkdir subspace2 && cd subspace2
```

#### Скачиваем docker-compose.yml (_важно находится при этом в папке ноды 2_)
```
wget -qO - https://raw.githubusercontent.com/Genstamindband/manuals/main/subspace/subspace-docker/docker-compose.yml | envsubst > docker-compose.yml
```
### Правим конфиг

`nano /root/subspace2/docker-compose.yml`

Меняем имя, адресс и plot.
и самое главное - порты:

Нам необходимо поменять 2 порта. По умолчанию это 30333 и 40333. Меняем их, например, на 30433 и 40433. (Строчки 16, 23, 55, 62) Со следующими нодами на сервере повторять по аналогии.

![alt text](https://i.imgur.com/rtyXIr4.png)


![alt text](https://i.imgur.com/5hGVPE8.png)

Для сохранения правок необходимо нажать CTRL + X -> ENTER

#### Запускаем контейнеры 
```
docker-compose up -d
```

#### Смотрим логи (_из папки с нодой 2_)
```
docker-compose logs --tail=1000 -f
```
