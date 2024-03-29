## Домашнее задание

Архитектура сетей

#### Цель домашнего задания

Научится менять базовые сетевые настройки в Linux-based системах.

---

#### Описание домашнего задания

---

1. Скачать и развернуть Vagrant-стенд https://github.com/erlong15/otus-linux/tree/network
2. Построить следующую сетевую архитектуру: Сеть office1

- 192\.168.2.0/26      - dev
- 192\.168.2.64/26     - test servers
- 192\.168.2.128/26    - managers
- 192\.168.2.192/26    - office hardware

Сеть office2

- 192\.168.1.0/25      - dev
- 192\.168.1.128/26    - test servers
- 192\.168.1.192/26    - office hardware

Сеть central

- 192\.168.0.0/28     - directors
- 192\.168.0.32/28    - office hardware
- 192\.168.0.64/26    - wifi

![](https://github.com/buravtsovpavel/OTUS-homeworks/blob/master/network%20laboratory/png/topology.png)

Итого должны получиться следующие сервера:

inetRouter
centralRouter
office1Router
office2Router
centralServer
office1Server
office2Server

**Задание состоит из 2-х частей: теоретической и практической.**

**В теоретической части требуется:**

* Найти свободные подсети
* Посчитать количество узлов в каждой подсети, включая свободные
* Указать Broadcast-адрес для каждой подсети
* Проверить, нет ли ошибок при разбиении

**В практической части требуется:**

* Соединить офисы в сеть согласно логической схеме и настроить роутинг
* Интернет-трафик со всех серверов должен ходить через inetRouter
* Все сервера должны видеть друг друга (должен проходить ping)
* У всех новых серверов отключить дефолт на NAT (eth0), который vagrant поднимает для связи
* Добавить дополнительные сетевые интерфейсы, если потребуется

Рекомендуется использовать Vagrant + Ansible для настройки данной схемы.

## **1. Теоретическая часть**

**количество узлов в каждой подсети, включая свободные:**

Сеть office1-dev 192.168.2.0/26:

* Маска 255.255.255.192
* Всего возможно хостов в подсети - 62
* Broadcast-адрес: 192.168.2.63

Сеть office1-test servers 192.168.2.64/26:

* Маска 255.255.255.192
* Всего возможно хостов в подсети - 62
* Broadcast-адрес: 192.168.2.127

Сеть office1-managers 192.168.2.128/26:

* Маска 255.255.255.192
* Всего возможно хостов в подсети - 62
* Broadcast-адрес: 192.168.2.191

Сеть office1-office hardware 192.168.2.192/26:

* Маска 255.255.255.192
* Всего возможно хостов в подсети - 62
* Broadcast-адрес: 192.168.2.255

Сеть office2-dev 192.168.1.0/25:

* Маска 255.255.255.128
* Всего возможно хостов в подсети - 126
* Broadcast-адрес: 192.168.1.127

Сеть office2-test servers 192.168.1.128/26:

* Маска 255.255.255.192
* Всего возможно хостов в подсети - 62
* Broadcast-адрес: 192.168.1.191

Сеть office2-office hardware 192.168.1.192/26:

* Маска 255.255.255.192
* Всего возможно хостов в подсети - 62
* Broadcast-адрес: 192.168.1.255

Сеть central-directors 192.168.0.0/28:

* Маска 255.255.255.240
* Всего возможно хостов в подсети - 14
* Broadcast-адрес: 192.168.0.15

Сеть central-office hadware 192.168.0.32/28:

* Маска 255.255.255.240
* Всего возможно хостов в подсети - 14
* Broadcast-адрес: 192.168.0.47

Сеть central-wifi 192.168.0.64/26:

* Маска 255.255.255.192
* Всего возможно хостов в подсети - 62
* Broadcast-адрес: 192.168.0.127

**После создания таблицы топологии, мы можем заметить, что ошибок в задании нет, также мы сразу видим следующие свободные сети:**

192.168.0.16/28
192.168.0.48/28
192.168.0.128/25
192.168.255.64/26
192.168.255.32/27
192.168.255.16/28
192.168.255.8/29  
192.168.255.4/30

После расчета всех сетей, мы получаем следующую таблицу топологии:

![](https://github.com/buravtsovpavel/OTUS-homeworks/blob/master/network%20laboratory/png/topology%20table.png)

## **2. Практическая часть**

Изучив таблицу топологии сети и Vagrant-стенд из задания, мы можем построить полную схему сети:

![](https://github.com/buravtsovpavel/OTUS-homeworks/blob/master/network%20laboratory/png/full%20topology.png)

На схеме, мы сразу можем увидеть, что нам потребуется создать дополнительно 2 сети (на схеме обозначены полужирными фиолетовыми линиями):
* Для соединения office1Router c centralRouter
* Для соединения office2Router c centralRouter

На основании этой схемы мы получаем готовый список серверов.

Для практической части задания подготовлен [Vagrantfile](https://github.com/buravtsovpavel/OTUS-homeworks/blob/master/network%20laboratory/Vagrantfile) и [ansible-playbook](https://github.com/buravtsovpavel/OTUS-homeworks/blob/master/network%20laboratory/ansible/provision.yml), который включает маршрутизацию транзитных пакетов на роутерах, отключает маршрут по умолчанию и назначает новый маршрут по умолчанию на всех машинах кроме InetRouter, настраивает роутинг.

В результате получаем соединённые в сеть офисы согласно логической схеме, интернет-трафик со всех серверов ходит через inetRouter, все сервера видят друг друга (проходит ping), у всех новых серверов отключен дефолт на NAT (eth0), который vagrant поднимает для связи.

В качестве примера пинги с office1Server:

![](https://github.com/buravtsovpavel/OTUS-homeworks/blob/master/network%20laboratory/png/office1server.png)

Пинг с office2Server до centralServer и наоборот:

![](https://github.com/buravtsovpavel/OTUS-homeworks/blob/master/network%20laboratory/png/office2server.png)

![](https://github.com/buravtsovpavel/OTUS-homeworks/blob/master/network%20laboratory/png/centralServer.png)

Пример проверки выхода в Интернет через сервер inetRouter c хоста office1Server:

![](https://github.com/buravtsovpavel/OTUS-homeworks/blob/master/network%20laboratory/png/traceroute%20office1Server.png)

Пример проверки выхода в Интернет через сервер inetRouter c хоста office2Server:

![](https://github.com/buravtsovpavel/OTUS-homeworks/blob/master/network%20laboratory/png/traceroute%20office2Server.png)
