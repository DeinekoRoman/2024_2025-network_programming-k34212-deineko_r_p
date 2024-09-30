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

На стороне клиента был использован файл с прописанной конфигурацией, включая необходимые для подключения сертификаты и ключи









