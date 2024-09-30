University: [ITMO University](https://itmo.ru/ru/)\
Faculty: [FICT](https://fict.itmo.ru)\
Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)\
Year: 2024/2025\
Group: K34212\
Author: Deineko Roman Pavlovich\
Lab: Lab1\
Date of create: 28.09.2024\
Date of finished: 30.09.2024

## Лабораторная работа №1 "Установка CHR и Ansible, настройка VPN"

## <a name="section1">Описание</a>
Данная работа предусматривает обучение развертыванию виртуальных машин (VM) и системы контроля конфигураций Ansible а также организации собственных VPN серверов.

## <a name="section2">Цель работы</a>
Целью данной работы является развертывание виртуальной машины на базе платформы Microsoft Azure с установленной системой контроля конфигураций Ansible и установка CHR в VirtualBox.

## <a name="section4">Ход работы</a>
В качестве платформы для развертывания VPN-сервера был арендован сервер VDS Selectel
![VirtualBox_Ubuntu_29_09_2024_20_56_15](https://github.com/user-attachments/assets/9d9794a0-2c65-4381-9b7b-8375544a2606)

Для подключения к нему был сгенерирован ssh-ключ. Затем на сервер были установлены python и ansible
![VirtualBox_Ubuntu_29_09_2024_21_21_28](https://github.com/user-attachments/assets/cfceeb04-dd97-4123-8bd0-8b6edbf7e930)

Далее была установлена RouterOS CHR на новую виртуальную машину
![VirtualBox_CHR_30_09_2024_21_58_21](https://github.com/user-attachments/assets/3af03526-1a1d-4543-86e8-432803f8c638)

В дальнейшем все настройки роутера производились посредством подключения к нему через WinBox
![VirtualBox_Ubuntu_29_09_2024_22_04_42](https://github.com/user-attachments/assets/a582c0fe-5636-46fc-8464-cf587261d7b3)

При помощи скрипта на хосте был развернут OpenVPN-сервер
![VirtualBox_Ubuntu_29_09_2024_22_30_54](https://github.com/user-attachments/assets/724b43bb-a9d0-4181-8f6a-fec2097b0e48)
![VirtualBox_Ubuntu_29_09_2024_22_52_39](https://github.com/user-attachments/assets/f98040e1-07c8-421c-ac21-0c653c806d3c)

На стороне клиента был использован файл с прописанной конфигурацией, включая необходимые для подключения сертификаты и ключи "client.ovpn"
![VirtualBox_Ubuntu_30_09_2024_22_06_10](https://github.com/user-attachments/assets/d2b2c43b-25ca-431f-a306-fb28234ca938)\
Конфигурация клиента:
```
client
dev tun
proto udp
remote 1194
resolv-retry infinite
nobind
persist-key
persist-tun
remote-cert-tls server
tls-version-min 1.2
cipher AES-128-CBC
auth SHA256
tls-client
auth-nocache
verb 3
<ca>
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
</ca>
<cert>
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
</cert>
<key>
-----BEGIN PRIVATE KEY-----
...
-----END PRIVATE KEY-----
</key>
key-direction 1
<tls-auth>
-----BEGIN OpenVPN Static key V1-----
...
-----END OpenVPN Static key V1-----
</tls-auth>
```

В процессе подключения возникла ошибка "TLS error: handshake timed out", которая со стороны сервера отображалась, как "tls-crypt unwrap error: packet too short". В результате директива tls-crypt была заменена на tls-auth, так как первая не поддерживается операционной системой CHR.

Наконец была произведена проверка доступности на сервере (сначала на адрес клиента 10.8.0.2, а затем на адрес dns-сервера гугл для проверки возможности выхода в интернет):
![VirtualBox_Ubuntu_30_09_2024_22_22_25](https://github.com/user-attachments/assets/986191ae-4cf3-441a-80a0-037382569b48)

То же самое было сделано на клиенте (10.8.0.1 - адрес сервера):
![VirtualBox_Ubuntu_30_09_2024_22_22_46](https://github.com/user-attachments/assets/c44d05be-14ec-4f20-ad53-6b0d2d5254dd)

## <a name="section4.6">Вывод</a> 

В ходе выполнения данной лабораторной работы был поднят сервер на платформе Selectl VDS, на нем установлены система контроля конфигураций Ansible и python, также была поднята ВМ с CHR в VirtualBox. На данных устройствах был поднят OpenVPN интерфейс и настроен тоннель между ними.





