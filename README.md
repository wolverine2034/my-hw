# Домашнее задание к занятию  «Очереди RabbitMQ»

### Инструкция по выполнению домашнего задания

1. Сделайте fork [репозитория c шаблоном решения](https://github.com/netology-code/sys-pattern-homework) к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
2. Выполните клонирование этого репозитория к себе на ПК с помощью команды `git clone`.
3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
   - впишите вверху название занятия и ваши фамилию и имя;
   - в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
   - для корректного добавления скриншотов воспользуйтесь инструкцией [«Как вставить скриншот в шаблон с решением»](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md);
   - при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в [инструкции по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md).
4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`).
5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
6. Любые вопросы задавайте в разделе «Вопросы по заданию» в личном кабинете.

Желаем успехов в выполнении домашнего задания.

---

### Задание 1. Установка RabbitMQ

Используя Vagrant или VirtualBox, создайте виртуальную машину и установите RabbitMQ.
Добавьте management plug-in и зайдите в веб-интерфейс.

*Итогом выполнения домашнего задания будет приложенный скриншот веб-интерфейса RabbitMQ.*

---

### Задание 2. Отправка и получение сообщений

Используя приложенные скрипты, проведите тестовую отправку и получение сообщения.
Для отправки сообщений необходимо запустить скрипт producer.py.

Для работы скриптов вам необходимо установить Python версии 3 и библиотеку Pika.
Также в скриптах нужно указать IP-адрес машины, на которой запущен RabbitMQ, заменив localhost на нужный IP.

```shell script
$ pip install pika
```

Зайдите в веб-интерфейс, найдите очередь под названием hello и сделайте скриншот.
После чего запустите второй скрипт consumer.py и сделайте скриншот результата выполнения скрипта

*В качестве решения домашнего задания приложите оба скриншота, сделанных на этапе выполнения.*

Для закрепления материала можете попробовать модифицировать скрипты, чтобы поменять название очереди и отправляемое сообщение.

---

### Задание 3. Подготовка HA кластера

Используя Vagrant или VirtualBox, создайте вторую виртуальную машину и установите RabbitMQ.
Добавьте в файл hosts название и IP-адрес каждой машины, чтобы машины могли видеть друг друга по имени.

Пример содержимого hosts файла:
```shell script
$ cat /etc/hosts
192.168.0.10 rmq01
192.168.0.11 rmq02
```
После этого ваши машины могут пинговаться по имени.

Затем объедините две машины в кластер и создайте политику ha-all на все очереди.

*В качестве решения домашнего задания приложите скриншоты из веб-интерфейса с информацией о доступных нодах в кластере и включённой политикой.*

Также приложите вывод команды с двух нод:

```shell script
$ rabbitmqctl cluster_status
```

Для закрепления материала снова запустите скрипт producer.py и приложите скриншот выполнения команды на каждой из нод:

```shell script
$ rabbitmqadmin get queue='hello'
```

После чего попробуйте отключить одну из нод, желательно ту, к которой подключались из скрипта, затем поправьте параметры подключения в скрипте consumer.py на вторую ноду и запустите его.

*Приложите скриншот результата работы второго скрипта.*

---
#### Ответ:
1. Скриншот 1

   ![Cкриншот 1](https://github.com/wolverine2034/8-03-hw/blob/main/img/1.png?raw=true)

2. Скриншот 2

   ![Cкриншот 2](https://github.com/wolverine2034/8-03-hw/blob/main/img/2.png?raw=true)

   Скриншот 3

   ![Cкриншот 3](https://github.com/wolverine2034/8-03-hw/blob/main/img/3.png?raw=true)
   
   Скриншот 4

   ![Cкриншот 4](https://github.com/wolverine2034/8-03-hw/blob/main/img/4.png?raw=true)

3. Скриншот 5
   
   ![Cкриншот 5](https://github.com/wolverine2034/8-03-hw/blob/main/img/5.png?raw=true)
   
   Скриншот 6
   
   ![Cкриншот 6](https://github.com/wolverine2034/8-03-hw/blob/main/img/6.png?raw=true)
   
   Вывод команд:
```shell
(.venv) root@srv-pterodactyl:/opt/rabbitmq-lab# docker exec rmq01 rabbitmqctl cl                                                                                                                                                             uster_status
Cluster status of node rabbit@rmq01 ...
Basics

Cluster name: rabbit@rmq01
Total CPU cores available cluster-wide: 12

Disk Nodes

rabbit@rmq01
rabbit@rmq02

Running Nodes

rabbit@rmq01
rabbit@rmq02

Versions

rabbit@rmq01: RabbitMQ 3.13.7 on Erlang 26.2.5.16
rabbit@rmq02: RabbitMQ 3.13.7 on Erlang 26.2.5.16

CPU Cores

Node: rabbit@rmq01, available CPU cores: 6
Node: rabbit@rmq02, available CPU cores: 6

Maintenance status

Node: rabbit@rmq01, status: not under maintenance
Node: rabbit@rmq02, status: not under maintenance

Alarms

(none)

Network Partitions

(none)

Listeners

Node: rabbit@rmq01, interface: [::], port: 15672, protocol: http, purpose: HTTP                                                                                                                                                              API
Node: rabbit@rmq01, interface: [::], port: 15692, protocol: http/prometheus, pur                                                                                                                                                             pose: Prometheus exporter API over HTTP
Node: rabbit@rmq01, interface: [::], port: 25672, protocol: clustering, purpose:                                                                                                                                                              inter-node and CLI tool communication
Node: rabbit@rmq01, interface: [::], port: 5672, protocol: amqp, purpose: AMQP 0                                                                                                                                                             -9-1 and AMQP 1.0
Node: rabbit@rmq02, interface: [::], port: 15672, protocol: http, purpose: HTTP                                                                                                                                                              API
Node: rabbit@rmq02, interface: [::], port: 15692, protocol: http/prometheus, pur                                                                                                                                                             pose: Prometheus exporter API over HTTP
Node: rabbit@rmq02, interface: [::], port: 25672, protocol: clustering, purpose:                                                                                                                                                              inter-node and CLI tool communication
Node: rabbit@rmq02, interface: [::], port: 5672, protocol: amqp, purpose: AMQP 0                                                                                                                                                             -9-1 and AMQP 1.0

Feature flags

Flag: classic_mirrored_queue_version, state: enabled
Flag: classic_queue_type_delivery_support, state: enabled
Flag: detailed_queues_endpoint, state: enabled
Flag: direct_exchange_routing_v2, state: enabled
Flag: drop_unroutable_metric, state: enabled
Flag: empty_basic_get_metric, state: enabled
Flag: feature_flags_v2, state: enabled
Flag: implicit_default_bindings, state: enabled
Flag: khepri_db, state: disabled
Flag: listener_records_in_ets, state: enabled
Flag: maintenance_mode_status, state: enabled
Flag: message_containers, state: enabled
Flag: message_containers_deaths_v2, state: enabled
Flag: quorum_queue, state: enabled
Flag: quorum_queue_non_voters, state: enabled
Flag: restart_streams, state: enabled
Flag: stream_filtering, state: enabled
Flag: stream_queue, state: enabled
Flag: stream_sac_coordinator_unblock_group, state: enabled
Flag: stream_single_active_consumer, state: enabled
Flag: stream_update_config_command, state: enabled
Flag: tracking_records_in_ets, state: enabled
Flag: user_limits, state: enabled
Flag: virtual_host_metadata, state: enabled
(.venv) root@srv-pterodactyl:/opt/rabbitmq-lab# docker exec rmq02 rabbitmqctl cl                                                                                                                                                             uster_status
Cluster status of node rabbit@rmq02 ...
Basics

Cluster name: rabbit@rmq02
Total CPU cores available cluster-wide: 12

Disk Nodes

rabbit@rmq01
rabbit@rmq02

Running Nodes

rabbit@rmq01
rabbit@rmq02

Versions

rabbit@rmq02: RabbitMQ 3.13.7 on Erlang 26.2.5.16
rabbit@rmq01: RabbitMQ 3.13.7 on Erlang 26.2.5.16

CPU Cores

Node: rabbit@rmq02, available CPU cores: 6
Node: rabbit@rmq01, available CPU cores: 6

Maintenance status

Node: rabbit@rmq02, status: not under maintenance
Node: rabbit@rmq01, status: not under maintenance

Alarms

(none)

Network Partitions

(none)

Listeners

Node: rabbit@rmq02, interface: [::], port: 15672, protocol: http, purpose: HTTP                                                                                                                                                              API
Node: rabbit@rmq02, interface: [::], port: 15692, protocol: http/prometheus, pur                                                                                                                                                             pose: Prometheus exporter API over HTTP
Node: rabbit@rmq02, interface: [::], port: 25672, protocol: clustering, purpose:                                                                                                                                                              inter-node and CLI tool communication
Node: rabbit@rmq02, interface: [::], port: 5672, protocol: amqp, purpose: AMQP 0                                                                                                                                                             -9-1 and AMQP 1.0
Node: rabbit@rmq01, interface: [::], port: 15672, protocol: http, purpose: HTTP                                                                                                                                                              API
Node: rabbit@rmq01, interface: [::], port: 15692, protocol: http/prometheus, pur                                                                                                                                                             pose: Prometheus exporter API over HTTP
Node: rabbit@rmq01, interface: [::], port: 25672, protocol: clustering, purpose:                                                                                                                                                              inter-node and CLI tool communication
Node: rabbit@rmq01, interface: [::], port: 5672, protocol: amqp, purpose: AMQP 0                                                                                                                                                             -9-1 and AMQP 1.0

Feature flags

Flag: classic_mirrored_queue_version, state: enabled
Flag: classic_queue_type_delivery_support, state: enabled
Flag: detailed_queues_endpoint, state: enabled
Flag: direct_exchange_routing_v2, state: enabled
Flag: drop_unroutable_metric, state: enabled
Flag: empty_basic_get_metric, state: enabled
Flag: feature_flags_v2, state: enabled
Flag: implicit_default_bindings, state: enabled
Flag: khepri_db, state: disabled
Flag: listener_records_in_ets, state: enabled
Flag: maintenance_mode_status, state: enabled
Flag: message_containers, state: enabled
Flag: message_containers_deaths_v2, state: enabled
Flag: quorum_queue, state: enabled
Flag: quorum_queue_non_voters, state: enabled
Flag: restart_streams, state: enabled
Flag: stream_filtering, state: enabled
Flag: stream_queue, state: enabled
Flag: stream_sac_coordinator_unblock_group, state: enabled
Flag: stream_single_active_consumer, state: enabled
Flag: stream_update_config_command, state: enabled
Flag: tracking_records_in_ets, state: enabled
Flag: user_limits, state: enabled
Flag: virtual_host_metadata, state: enabled
(.venv) root@srv-pterodactyl:/opt/rabbitmq-lab#
```
   
   Скриншот 7

   ![Cкриншот 7](https://github.com/wolverine2034/8-03-hw/blob/main/img/7.png?raw=true)

   Скриншот 8
   
   ![Cкриншот 8](https://github.com/wolverine2034/8-03-hw/blob/main/img/8.png?raw=true)
