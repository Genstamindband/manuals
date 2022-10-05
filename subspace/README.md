# Запуск нескольких нод Subspace Gemini 2a на одном сервере


## Требования

[Официальный гайд](https://docs.subspace.network/protocol/farm/farming/)

Требования из документации для 1 ноды с размером plot 100 GB.


Hardware  | Specs
------------- | -------------
CPU  | 2+
RAM | 4GB+ (Rec. 8GB
Storage | 150GB

>• в нашем случае мы будем использовать от 25GB plot + размер самой ноды, который хоть и не сразу, но растет до 50GB+. Итого 1 нода потребует у вас около 70+GB.

>• рекомендуется не использовать HDD, он плохо справляется с этой задачей

>• синхронизированная нода потребляет порядка 5 Мбит траффика

>• будьте внимательны и следите за потреблением ресурсов ваших серверов, после достижения определенных лимитов траффика на некоторых хостингах может взиматься дополнительная плата!

![](https://i.imgur.com/tuSI1el.png)

## Установка
### Устанавливаем переменные

> будьте внимательны на этом этапе, в переменных легко потеряться. Ниже я все подробно описал.

Измените значения на свои:

*NODE_NAME1* - название вашей 1 ноды.

*WALLET1* - адрес вашего кошелька, который можно создать на [сайте.](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Feu-2.gemini-2a.subspace.network%2Fws#/accounts) 

*ACCOUNT1* - subspace + порядковый номер ноды на сервере. Например, для первой ноды пишем subspace1, для следующей ноды пишем subspace2 и т.д. по аналогии.

*PORT1* - порт вашей ноды. Для первой ноды можно выбрать 30356. Для последующей ноды - 30456 или 30556 или 30656 и т.д.

*WS_PORT1*- ws_port вашей ноды. Для первой ноды выбираем значение 9980. Для последующих, например 9990, 9970, 9960 и т.д.

*PLOT_SIZE1* - количество памяти, выделяемое под plot. От этого значения зависит вероятность добыть монетку. Но в текущих условиях нам нужно нафармить не менее 0.5 tssc. Это реально сделать и с плотом в 25 Гб. 

```
echo 'export SUBSPACE_NODENAME='NODE_NAME1 >> $HOME/.bash_profile
echo 'export SUBSPACE_WALLET='WALLET1 >> $HOME/.bash_profile
echo 'export SUBSPACE_ACCOUNT='ACCOUNT1 >> $HOME/.bash_profile
echo 'export SUBSPACE_PORT='PORT1 >> $HOME/.bash_profile
echo 'export SUBSPACE_WS_PORT='WS_PORT1 >> $HOME/.bash_profile
echo 'export SUBSPACE_PLOT_SIZE='PLOT_SIZE1 >> $HOME/.bash_profile
```

![](https://i.imgur.com/Xo8N2tI.png)

*Примерно так должно получится у вас. Данные на скрине даны для примера! Впишите свои значения.* 

### Обновляем репозитории, устанавливаем необходимые утилиты.

```
apt update && apt upgrade -y
sudo apt install ocl-icd-opencl-dev libopencl-clang-dev libgomp1 -y
```

### Установка 1 ноды

Вставляем команды по отдельности

```
cd $HOME
wget -O subspace-node https://github.com/subspace/subspace/releases/download/gemini-2a-2022-sep-10/subspace-node-ubuntu-x86_64-gemini-2a-2022-sep-10
wget -O subspace-farmer https://github.com/subspace/subspace/releases/download/gemini-2a-2022-sep-10/subspace-farmer-ubuntu-x86_64-gemini-2a-2022-sep-10
chmod +x subspace*
source ~/.bash_profile
mkdir /usr/local/bin/$SUBSPACE_ACCOUNT /root/.local/share/$SUBSPACE_ACCOUNT
mv subspace* /usr/local/bin/$SUBSPACE_ACCOUNT
```

Копируем весь блок целиком

```
echo "[Unit]
Description=Subspace Node
After=network.target

[Service]
User=root
Type=simple
ExecStart=/usr/local/bin/$SUBSPACE_ACCOUNT/subspace-node --base-path /root/.local/share/$SUBSPACE_ACCOUNT/subspace-node --chain gemini-2a --pruning 1024 --validator --keep-blocks 1024 --port $SUBSPACE_PORT --ws-port $SUBSPACE_WS_PORT --name $SUBSPACE_NODENAME
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/$SUBSPACE_ACCOUNT.service
```


```
echo "[Unit]
Description=Subspaced Farm
After=network.target

[Service]
User=root
Type=simple
ExecStart=/usr/local/bin/$SUBSPACE_ACCOUNT/subspace-farmer --base-path /root/.local/share/$SUBSPACE_ACCOUNT/subspace-farmer farm --reward-address $SUBSPACE_WALLET --node-rpc-url ws://127.0.0.1:$SUBSPACE_WS_PORT --plot-size $SUBSPACE_PLOT_SIZE 
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/$SUBSPACE_ACCOUNT-farmer.service
```

```
mv $HOME/subspace* /etc/systemd/system/ 
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload 
sudo systemctl enable $SUBSPACE_ACCOUNT $SUBSPACE_ACCOUNT-farmer 
sudo systemctl restart $SUBSPACE_ACCOUNT 

```

Ждем 10 секунд

```
sudo systemctl restart $SUBSPACE_ACCOUNT-farmer
```




### Проверить логи ноды и фармера 1

```
journalctl -u subspace1 -f -o cat
journalctl -u subspace1-farmer -f -o cat

```

> обращаем внимание на цифру в командах - она означает порядковый номер вашей ноды.


Успешный вывод ноды

![](https://i.imgur.com/Ro7IlWg.png)

Успешныё вывод фармера

![](https://i.imgur.com/lxIxnIC.png)



Установка 1 ноды завершена.

После установки 1 ноды, для того, чтобы начать установку 2 ноды, нам необходимо снова задать переменные, поэтому повторяем действия из шага *Устанавливаем переменные*, подставив свое *Имя Ноды, Кошелек, Порты и порядковый номер ноды* _(subspace1, subspace2,... subspace5)_

### Проверить логи ноды и фармера 2

```
journalctl -u subspace2 -f -o cat
journalctl -u subspace2-farmer -f -o cat
```

### Проверить логи ноды и фармера 3

```
journalctl -u subspace3 -f -o cat
journalctl -u subspace3-farmer -f -o cat
```

### Удаление 

Останавливаем работу

```
sudo systemctl stop subspace1 subspace1-farmer
sudo systemctl disable subspace1 subspace1-farmer
```
Удаляем директории 1 ноды
```
rm -rf /root/.local/share/subspace1
rm -rf /etc/systemd/system/subspace1
rm -rf /usr/local/bin/subspace1
```

Опять обращаем внимание на цифры. Для последуюих нод меняем ее на нужную. Например для ноды 2:

```
#sudo systemctl stop subspace1 subspace2-farmer
#sudo systemctl disable subspace1 subspace2-farmer
#rm -rf /root/.local/share/subspace2
#rm -rf /etc/systemd/system/subspace2
#rm -rf /usr/local/bin/subspace2
```

