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

* Публичная подсеть NAT-инстанс, присвоив ему адрес 192.168.10.254. В качестве image_id используя fd80mrhj8fl2oe87o4e1.
[nat_instance.tf]https://github.com/Nano-prototip/-SHCLOPRO-24/blob/main/terraform/nat_instance.tf

* Публичная подсеть виртуальная машина с публичным IP.
[public.tf]https://github.com/Nano-prototip/-SHCLOPRO-24/blob/main/terraform/public.tf

* Проверка доступа к интернету
  <img width="734" height="201" alt="01" src="https://github.com/user-attachments/assets/96675725-1639-4f05-a676-e9786f84fe0b" />

  


