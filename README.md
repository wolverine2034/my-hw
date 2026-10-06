# Домашнее задание к занятию «Отказоустойчивость в облаке»

### Цель задания

В результате выполнения этого задания вы научитесь:  
1. Конфигурировать отказоустойчивый кластер в облаке с использованием различных функций отказоустойчивости. 
2. Устанавливать сервисы из конфигурации инфраструктуры.

------

### Чеклист готовности к домашнему заданию

1. Создан аккаунт на YandexCloud.  
2. Создан новый OAuth-токен.  
3. Установлено программное обеспечение  Terraform.   


### Инструкция по выполнению домашнего задания

1. Сделайте fork [репозитория c Шаблоном решения](https://github.com/netology-code/sys-pattern-homework) к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
   - впишите вверху название занятия и вашу фамилию и имя
   - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
   - для корректного добавления скриншотов воспользуйтесь инструкцией ["Как вставить скриншот в шаблон с решением"](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
   - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
6. Любые вопросы по выполнению заданий спрашивайте в разделе “Вопросы по заданию” в личном кабинете.


### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Документация сетевого балансировщика нагрузки](https://cloud.yandex.ru/docs/network-load-balancer/quickstart)

 ---

## Задание 1 

Возьмите за основу [решение к заданию 1 из занятия «Подъём инфраструктуры в Яндекс Облаке»](https://github.com/netology-code/sdvps-homeworks/blob/main/7-03.md#задание-1).

1. Теперь вместо одной виртуальной машины сделайте terraform playbook, который:

- создаст 2 идентичные виртуальные машины. Используйте аргумент [count](https://www.terraform.io/docs/language/meta-arguments/count.html) для создания таких ресурсов;
- создаст [таргет-группу](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/lb_target_group). Поместите в неё созданные на шаге 1 виртуальные машины;
- создаст [сетевой балансировщик нагрузки](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/lb_network_load_balancer), который слушает на порту 80, отправляет трафик на порт 80 виртуальных машин и http healthcheck на порт 80 виртуальных машин.

Рекомендуем изучить [документацию сетевого балансировщика нагрузки](https://cloud.yandex.ru/docs/network-load-balancer/quickstart) для того, чтобы было понятно, что вы сделали.

2. Установите на созданные виртуальные машины пакет Nginx любым удобным способом и запустите Nginx веб-сервер на порту 80.

3. Перейдите в веб-консоль Yandex Cloud и убедитесь, что: 

- созданный балансировщик находится в статусе Active,
- обе виртуальные машины в целевой группе находятся в состоянии healthy.

4. Сделайте запрос на 80 порт на внешний IP-адрес балансировщика и убедитесь, что вы получаете ответ в виде дефолтной страницы Nginx.

*В качестве результата пришлите:*

*1. Terraform Playbook.*

*2. Скриншот статуса балансировщика и целевой группы.*

*3. Скриншот страницы, которая открылась при запросе IP-адреса балансировщика.*

---
#### Ответ:
1. Terraform Playbook:

1) versions.tf 
```
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    yandex = {
      source  = "yandex-cloud/yandex"
      version = ">= 0.200.0"
    }
  }
}

provider "yandex" {
  cloud_id  = var.cloud_id
  folder_id = var.folder_id
  zone      = var.zone
}

```

2) variables.tf 
```
variable "cloud_id" {
  description = "ID облака Yandex Cloud"
  type        = string
}

variable "folder_id" {
  description = "ID каталога Yandex Cloud"
  type        = string
}

variable "zone" {
  description = "Зона доступности"
  type        = string
  default     = "ru-central1-a"
}

variable "vm_count" {
  description = "Количество виртуальных машин"
  type        = number
  default     = 2
}

```

3) main.tf
```

# Получаем актуальный образ Debian 13 из публичного каталога
data "yandex_compute_image" "debian" {
  family    = "debian-13"
  folder_id = "standard-images"
}

# VPC-сеть
resource "yandex_vpc_network" "network" {
  name = "netology-network"
}

# Подсеть
resource "yandex_vpc_subnet" "subnet" {
  name           = "netology-subnet"
  zone           = var.zone
  network_id     = yandex_vpc_network.network.id
  v4_cidr_blocks = ["10.10.0.0/24"]
}

# Правила сетевого доступа
resource "yandex_vpc_security_group" "web_sg" {
  name       = "netology-web-sg"
  network_id = yandex_vpc_network.network.id

  ingress {
    protocol       = "TCP"
    description    = "HTTP"
    port           = 80
    v4_cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    protocol       = "ANY"
    description    = "Разрешить исходящий трафик"
    from_port      = 0
    to_port        = 65535
    v4_cidr_blocks = ["0.0.0.0/0"]
  }
}

# Две одинаковые виртуальные машины
resource "yandex_compute_instance" "web" {
  count = var.vm_count

  name        = "netology-web-${count.index + 1}"
  platform_id = var.vm_platform_id
  zone        = var.zone

  resources {
    cores  = var.vm_cores
    memory = var.vm_memory
  }

  boot_disk {
    initialize_params {
      image_id = data.yandex_compute_image.debian.id
      size     = 10
      type     = "network-hdd"
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.subnet.id
    nat       = true
    security_group_ids = [
      yandex_vpc_security_group.web_sg.id
    ]
  }

  metadata = {
    user-data = <<-EOF
      #cloud-config
      package_update: true
      packages:
        - nginx
      runcmd:
        - [systemctl, enable, nginx]
        - [systemctl, restart, nginx]
    EOF
  }
}

# Целевая группа: обе виртуальные машины
resource "yandex_lb_target_group" "web_tg" {
  name      = "netology-web-target-group"
  region_id = "ru-central1"

  dynamic "target" {
    for_each = yandex_compute_instance.web

    content {
      subnet_id = yandex_vpc_subnet.subnet.id
      address   = target.value.network_interface[0].ip_address
    }
  }
}

# Сетевой балансировщик нагрузки
resource "yandex_lb_network_load_balancer" "web_lb" {
  name = "netology-web-load-balancer"
  type = "external"

  listener {
    name        = "http-listener"
    port        = 80
    target_port = 80
    protocol    = "tcp"

    external_address_spec {
      ip_version = "ipv4"
    }
  }

  attached_target_group {
    target_group_id = yandex_lb_target_group.web_tg.id

    healthcheck {
      name                = "http-healthcheck"
      interval            = 2
      timeout             = 1
      healthy_threshold   = 2
      unhealthy_threshold = 2

      http_options {
        port = 80
        path = "/"
      }
    }
  }
}

```

4) outputs.tf
```
output "load_balancer_external_ip" {
  description = "Внешний IP сетевого балансировщика"

  value = one(flatten([
    for listener in yandex_lb_network_load_balancer.web_lb.listener : [
      for address in listener.external_address_spec : address.address
    ]
  ]))
}

output "virtual_machines" {
  description = "Имена и IP-адреса виртуальных машин"

  value = [
    for vm in yandex_compute_instance.web : {
      name       = vm.name
      private_ip = vm.network_interface[0].ip_address
      public_ip  = vm.network_interface[0].nat_ip_address
    }
  ]
}

output "target_group_id" {
  description = "ID целевой группы"

  value = yandex_lb_target_group.web_tg.id
}

```

5) terraform.tfvars
```
cloud_id  = "**********"
folder_id = "**********"

zone           = "ru-central1-a"
vm_count       = 2
vm_cores       = 2
vm_memory      = 2
vm_platform_id = "standard-v3"

```

2. Скриншот статуса балансировщика и целевой группы

![Cкриншот 1](https://github.com/wolverine2034/8-03-hw/blob/main/img/1.png?raw=true)

3. Скриншот страницы, которая открылась при запросе IP-адреса балансировщика

![Cкриншот 2](https://github.com/wolverine2034/8-03-hw/blob/main/img/2.png?raw=true)
