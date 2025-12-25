# Лабораторная работа №1 - Cloudtech
## 1. Цель работы

Знакомство с облачными сервисами. Понимание уровней абстракции над инфраструктурой в облаке. Формирование понимания типов потребления сервисов в сервисной-модели.

## 2. Исходные данные
- Файл `Mapping-Rules-AWS-team-9.csv` - частично заполненная таблица с колонками:

  + Product Code (код продукта AWS)

  + Usage Type (тип использования)

  + lineItem/Operation

  + lineItem/LineItemDescription

- Пустые колонки: IT Tower, Service Family, Service Type, Service Sub Type, Service Usage Type

## 3. Выполненные задачи

**3.1. Анализ исходных данных**
- Определены 4 основные группы AWS-сервисов:

  + **Compute** (Вычислительные ресурсы) – AmazonEC2, ComputeSavingsPlans

  + **Networking** (Сетевые сервисы) – AmazonCloudFront, AmazonRoute53

  + **AI & ML** (ИИ и машинное обучение) – AmazonKendra, AmazonLex

**3.2. Разработка логики классификации**

- Для каждого AWS-сервиса определены:

  + IT Tower: основная категория (Compute, Networking, AI & ML)

  + Service Family: подкатегория внутри IT Tower

  + Service Type: конкретный тип сервиса

  + Service Sub Type: специализация типа сервиса

  + Service Usage Type: конкретный вариант использования

- Пример логики для **AmazonEC2**:

  + IT Tower: Compute

  + Service Family: Compute

  + Service Type: Virtual Machines

  + Service Sub Type: On-Demand Instances / Dedicated Hosts / Spot Instances

  + Service Usage Type: BoxUsage / Dedicated Usage / Spot Usage
 
**3.3. Заполнение таблицы**

- Заполнены все пустые ячейки на основе:

  + Официальной документации AWS

  + Логики использования сервисов

  + Стандартных практик категоризации
 
## 4. Результаты

| IT Tower | Service Family | Service Type    | Service Sub Type           | Примеры Service Usage Type        |
|----------|----------------|-----------------|----------------------------|-----------------------------------|
|Compute   |Compute         |Virtual Machines |On-Demand Instances         |BoxUsage, Tax                      |
|Compute   |Compute         |Savings Plans    |Compute Savings Plans       |All Upfront, No Upfron             |
|Compute   |Compute         |Virtual Machines |Dedicated Hosts             |Dedicated Usage, Host Usage        |
|Compute   |Compute         |Virtual Machines |GPU Instances               |GPU Usage                          |
|Compute   |Compute         |Virtual Machines |Spot Instances              |Spot Usage                         |
|Compute   |Compute         |Virtual Machines |Burstable Instances         |CPU Credits                        |
|Compute   |Compute         |Virtual Machines |Purchase/Exchange/RI Resale |Purchase Fee, Exchange Usage       |
|Networking|Content Delivery|CDN              |CloudFront                  |Requests, Data Transfer, SSL       |
|AI & ML   |AI Services     |Enterprise Search|Kendra                      |Enterprise Edition, Document Scan  |
|AI & ML   |AI Services     |Conversational AI|Lex                         |Requests, Tax                      |
|Networking|DNS & Routing   |	DNS             |Route 53                    |DNS Queries, Health Checks, Geo DNS|

## 5. Классификация сервисов

**5.1. Основная модель: IaaS (Infrastructure as a Service)**

- Amazon EC2 — виртуальные машины, выделенные хосты, GPU-инстансы, Spot-инстансы

- Amazon EBS (упоминается косвенно через %EGpuUsage%, ExchangeUsage)

- Compute Savings Plans — резервирование вычислительных ресурсов

- Amazon Route 53 — DNS-сервис (сетевая инфраструктура)

- Amazon CloudFront — CDN (инфраструктура доставки контента)

**5.2. Элементы PaaS (Platform as a Service)**

- Amazon Lex — платформа для создания чат-ботов и голосовых интерфейсов

- Amazon Kendra — платформа интеллектуального поиска

**5.3. Элементы SaaS (Software as a Service)**

- Amazon CloudFront может рассматриваться как SaaS (управляемый CDN-сервис)

- Amazon Route 53 — управляемый DNS-сервис (SaaS-черты)

**5.4. Гибридные/специфические модели**

- Compute Savings Plans — модель ценообразования, а не сервис

- RI Resale — маркетплейс для перепродажи резервирований

## 6. Вывод

В ходе выполнения лабораторной работы была изучена сервисная модель Amazon Web Services
и проведена классификация ресурсов по уровням абстракции (IaaS, PaaS, SaaS), функциональным
категориям и типам потребления. На основе данных биллинга и параметров Usage Type выполнено
сопоставление сервисов с официальными категориями AWS.


