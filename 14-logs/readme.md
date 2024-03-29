# Цель домашнего задания
Научится проектировать централизованный сбор логов. Рассмотреть особенности разных платформ для сбора логов. 



---


## Описание домашнего задания

1. В Vagrant разворачиваем 2 виртуальные машины web и log
2. на web настраиваем nginx
3. на log настраиваем центральный лог сервер на любой системе на выбор
journald;
rsyslog;
elk.
4. настраиваем аудит, следящий за изменением конфигов nginx 

Все критичные логи с web должны собираться и локально и удаленно.
Все логи с nginx должны уходить на удаленный сервер (локально только критичные).
Логи аудита должны также уходить на удаленную систему.

Формат сдачи ДЗ - vagrant + ansible


---



Задание выпонено по Методическому пособию по выполнению домашнего задания. 

Центральный лог сервер настроен на rsyslog.

Для сдачи Valgartfile и ansible.

* set-localtime роль настройки времени на серверах
* nginx-install роль устанавливает nginx и подготавливает конфиг
* rsyslog-server роль настраивает rsyslog сервер для возможности принимать логи
* auditd-web роль настройки аудита, контролирующего изменения конфигурации nginx на сервере web
* auditd-log роль настройки аудита на сервере log (открывает порт и перезапускает службу auditd)


Для проверки отправки логов на удалённый сервер можно удалить картинку, к которой будет обращаться nginx во время открытия веб-сраницы: rm /usr/share/nginx/html/img/header-background.png.  Попробуем несколько раз зайти по адресу http://192.168.50.10. Далее заходим на log-сервер и смотрим информацию об nginx:

●	cat /var/log/rsyslog/web/nginx_access.log 

●	cat /var/log/rsyslog/web/nginx_error.log 

![](https://github.com/buravtsovpavel/OTUS-homeworks/blob/master/14-logs/png/nginx_access_error.png)

Чтобы проверить, что логи аудита начали записываться на log-сервере,нужно внести изменения в файл /etc/nginx/nginx.conf или поменять его атрибут, потом посмотреть информацию об изменениях: 

`ausearch -f /etc/nginx/nginx.conf`

Также можно воспользоваться поиском по файлу /var/log/audit/audit.log, указав наш тэг: 

`grep nginx_conf /var/log/audit/audit.log`

![](https://github.com/buravtsovpavel/OTUS-homeworks/blob/master/14-logs/png/ausearch.png)

![](https://github.com/buravtsovpavel/OTUS-homeworks/blob/master/14-logs/png/grep%20audit.png)
