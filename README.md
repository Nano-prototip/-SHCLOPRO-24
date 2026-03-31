# Домашнее задание к занятию «Организация сети»


### Задание. Yandex Cloud 

**Что нужно сделать**

1. Создать пустую VPC. Выбрать зону.
2. Публичная подсеть.
 - Создать в VPC subnet с названием public, сетью 192.168.10.0/24.
 - Создать в этой подсети NAT-инстанс, присвоив ему адрес 192.168.10.254. В качестве image_id использовать fd80mrhj8fl2oe87o4e1.
 - Создать в этой публичной подсети виртуалку с публичным IP, подключиться к ней и убедиться, что есть доступ к интернету.
3. Приватная подсеть.
 - Создать в VPC subnet с названием private, сетью 192.168.20.0/24.
 - Создать route table. Добавить статический маршрут, направляющий весь исходящий трафик private сети в NAT-инстанс.
 - Создать в этой приватной подсети виртуалку с внутренним IP, подключиться к ней через виртуалку, созданную ранее, и убедиться, что есть доступ к интернету.

### Выполнение задания. Yandex Cloud

1. Пустая VPC с именем dvl:

```
resource "yandex_vpc_network" "dvl" {
  name = var.vpc_name

variable "vpc_name" {
  type        = string
  default     = "dvl"
  description = "VPC network"
}
```

2. VPC публичная подсеть с названием public, сетью 192.168.10.0/24:

```
resource "yandex_vpc_subnet" "public" {
  name           = var.public_subnet
  zone           = var.default_zone
  network_id     = yandex_vpc_network.dvl.id
  v4_cidr_blocks = var.public_cidr
}

variable "public_cidr" {
  type        = list(string)
  default     = ["192.168.10.0/24"]
  description = "https://cloud.yandex.ru/docs/vpc/operations/subnet-create"
}

variable "public_subnet" {
  type        = string
  default     = "public"
  description = "subnet name"
}
```

* Публичная подсеть NAT-инстанс, присвоить адрес 192.168.10.254. В качестве image_id используя fd80mrhj8fl2oe87o4e1.
[nat_instance.tf](https://github.com/Nano-prototip/-SHCLOPRO-24/blob/main/terraform/nat_instance.tf)

* Публичная подсеть виртуальная машина с публичным IP.
[public.tf](https://github.com/Nano-prototip/-SHCLOPRO-24/blob/main/terraform/public.tf)

* Проверка доступа к интернету

  <img width="734" height="201" alt="01" src="https://github.com/user-attachments/assets/96675725-1639-4f05-a676-e9786f84fe0b" />

 3. VPC приватная подсеть с названием private, сетью 192.168.20.0/24:

```
resource "yandex_vpc_subnet" "private" {
  name           = var.private_subnet
  zone           = var.default_zone
  network_id     = yandex_vpc_network.dvl.id
  v4_cidr_blocks = var.private_cidr
  route_table_id = yandex_vpc_route_table.private-route.id
}

variable "private_cidr" {
  type        = list(string)
  default     = ["192.168.20.0/24"]
  description = "https://cloud.yandex.ru/docs/vpc/operations/subnet-create"
}

variable "private_subnet" {
  type        = string
  default     = "private"
  description = "subnet name"
}
```
[network.tf](https://github.com/Nano-prototip/-SHCLOPRO-24/blob/main/terraform/network.tf)

[variables.tf](https://github.com/Nano-prototip/-SHCLOPRO-24/blob/main/terraform/variables.tf)

* Route table. Статический маршрут, направляющий весь исходящий трафик private сети в NAT-инстанс:

```
resource "yandex_vpc_route_table" "private-route" {
  name       = "private-route"
  network_id = yandex_vpc_network.dvl.id
  static_route {
    destination_prefix = "0.0.0.0/0"
    next_hop_address   = "192.168.10.254"
  }
}
```
* проверка интернета на приватной виртуальной машине

<img width="682" height="133" alt="02" src="https://github.com/user-attachments/assets/7c6d14e4-218f-4dc2-88b2-48b4d5302421" />

Отключение виртуальной машины с NAT-инстансом:
<img width="1002" height="134" alt="03" src="https://github.com/user-attachments/assets/b5d5e778-746f-4480-ab77-fbc7f81f53eb" />

Проверка работы интернета на приватной виртуальной машине:
<img width="539" height="106" alt="04" src="https://github.com/user-attachments/assets/9910e03d-10fe-43a4-8e85-99540afb66b7" />

После разворачивания инфраструктуры получаем следующие виртуальные машины:
<img width="753" height="141" alt="05" src="https://github.com/user-attachments/assets/9908b2cf-d7a0-4d36-9173-c11f421bb88e" />

Output вывод Terraform:

<img width="487" height="478" alt="06" src="https://github.com/user-attachments/assets/c716ff6b-0c91-4389-915d-5953418e592d" />




