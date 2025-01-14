University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)  
Year: 2024/2025  
Group: K34212  
Author: Deineko Roman  
Lab: Lab4  
Date of create: 14.01.2025  
Date of finished: 14.01.2025

## Лабораторная работа №4 "Базовая 'коммутация' и туннелирование используя язык программирования P4"

## <a name="section0">Цель работы</a>
Изучить синтаксис языка программирования P4 и выполнить 2 обучающих задания от Open network foundation для ознакомления на практике с P4.

## <a name="section1">Ход работы</a>

Готовый образ виртуальной машины с Ubuntu 24.04 был скачан со страницы с инструкцией, чтобы не ждать сборки через vagrant.

<p align="center"><img src="./Screenshots/Capture.PNG" width=700></p>

С помощью команды make run была поднята виртуальная сеть mininet.

<p align="center"><img src="./Screenshots/Capture1.PNG" width=700></p>

Проверка связности показывает, что узлы не пингуются, так как по умолчанию в компилируемом файле basic.p4 коммутаторы настроены так, чтобы отбрасывать все пакеты.

<p align="center"><img src="./Screenshots/Capture2.PNG" width=700></p>

Для того, чтобы узлы стали доступны для передачи пакетов, в парсер и депарсер были добавлены заголовки ipv4 и ethernet. Также в раздел MyIngress была добавлена функция ipv4_forward и проверка на валидность заголовка.

```
parser MyParser(packet_in packet,
                out headers hdr,
                inout metadata meta,
                inout standard_metadata_t standard_metadata) {

    state start {
        transition parse_ethernet;
    }

    state parse_ethernet {
        packet.extract(hdr.ethernet);
  transition select(hdr.ethernet.etherType) {
      TYPE_IPV4: parse_ipv4;
      default: accept;
  }
    }

    state parse_ipv4 {
        packet.extract(hdr.ipv4);
        transition accept;
    }
}


control MyDeparser(packet_out packet, in headers hdr) {
    apply {
        packet.emit(hdr.ethernet);
        packet.emit(hdr.ipv4);
    }
}


control MyIngress(inout headers hdr,
                  inout metadata meta,
                  inout standard_metadata_t standard_metadata) {
    action drop() {
        mark_to_drop(standard_metadata);
    }

    action ipv4_forward(macAddr_t dstAddr, egressSpec_t port) {
        standard_metadata.egress_spec = port;
        hdr.ethernet.srcAddr = hdr.ethernet.dstAddr;
        hdr.ethernet.dstAddr = dstAddr;
        hdr.ipv4.ttl = hdr.ipv4.ttl - 1;
    }

    table ipv4_lpm {
        key = {
            hdr.ipv4.dstAddr: lpm;
        }
        actions = {
            ipv4_forward;
            drop;
            NoAction;
        }
        size = 1024;
        default_action = NoAction();
    }

    apply {
        if (hdr.ipv4.isValid()) {
            ipv4_lpm.apply();
        }
    }
}
```

В результате внесенных изменений узлы начали пинговаться.

<p align="center"><img src="./Screenshots/Capture3.PNG" width=700></p>

Для выполнения второго задания понадобится редактировать файл basic_tunnel.p4, в результате чего будет получен коммутатор, способный осуществлять пересылку как на основе содержимого пользовательского заголовка инакпсуляции, так и обычного ipv4. В парсер был добавлен фрагмент кода, позволяющий извлекать либо myTunnel заголовок, либо ipv4 заголовок на основе etherType поля в заголовке Ethernet.

```
    state parse_ethernet {
        packet.extract(hdr.ethernet);
        transition select(hdr.ethernet.etherType) {
            TYPE_MYTUNNEL: parse_myTunnel;
            TYPE_IPV4: parse_ipv4;
            default: accept;
        }
    }

    state parse_myTunnel {
        packet.extract(hdr.myTunnel);
        transition select(hdr.myTunnel.proto_id) {
            TYPE_IPV4: parse_ipv4;
            default: accept;
        }
    }

```

Затем было прописано действие myTunnel_forward и таблица с туннельной переадресацией.

```
    action myTunnel_forward(egressSpec_t port) {
        standard_metadata.egress_spec = port;
    }

    table myTunnel_exact {
        key = {
            hdr.myTunnel.dst_id: exact;
        }
        actions = {
            myTunnel_forward;
            drop;
        }
        size = 1024;
        default_action = drop();
    }

    apply {
        if (hdr.ipv4.isValid() && !hdr.myTunnel.isValid()) {
            ipv4_lpm.apply();
        }

        if (hdr.myTunnel.isValid()) {
            myTunnel_exact.apply();
        }
    }

```

Далее были проведены проверки связности узлов:

- Без туннелирования

<p align="center"><img src="./Screenshots/Capture4.PNG" width=700></p>

- Без туннелирования (адрес назначения изменен)

<p align="center"><img src="./Screenshots/Capture7.PNG" width=700></p>

- С туннелированием

<p align="center"><img src="./Screenshots/Capture5.PNG" width=700></p>

- С туннелированием (адрес назначения изменен, но dst_id оставлен таким же, как при прошлой проверке)

<p align="center"><img src="./Screenshots/Capture6.PNG" width=700></p>

Топология сети:

<p align="center"><img src="./Screenshots/Capture8.PNG" width=700></p>

### <a name="section2">Вывод</a>

В ходе выполнения данной лабораторной работы, было произведено знакомство со специализированным языком программирования для работы с сетевым трафиком на уровне чипа - P4 (Programming Protocol-Independent Packet Processors) и выполнено 2 задания с официального git репозитория (Implementing Basic Forwarding и Implementing Basic Tunneling)
