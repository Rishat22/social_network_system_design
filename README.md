# Designing a social network
Welcome to the repository, where **I will develop the architecture of the social network** — from the collection of requirements to the final technical implementation.

## 🔍 What will happen here?
In this repository, I:

- I will define functional and non-functional requirements
- I will develop architectural solutions
- I will highlight the key components of the system
- I will analyze scalability and fault tolerance
- I will evaluate different approaches to design

## 🎯 Goal
Create an optimal architecture for a social network, taking into account the workload, scalability and ease of development.

## 📌 Note
This repository is not a codebase, but a research project dedicated to design.


## 🎯 Функциональные требования (Functional Requirements)
- публикация постов из путешествий с фотографиями, небольшим описанием и привязкой к конкретному месту путешествия;
- оценка и комментарии постов других путешественников;
- подписка на других путешественников, чтобы следить за их активностью;
- поиск популярных мест для путешествий и просмотр постов с этих мест;
- просмотр ленты других путешественников и ленты пользователя, основанной на подписках в обратном хронологическом порядке;

## 📊 Нефункциональные требования (Non-Functional Requirements)
- DAU (Daily Active Users): 10 000 000
- Цель по доступности: 99.99% uptime (SLA).
- Катастрофоустойчивость: Репликация данных в нескольких регионах (СНГ).
- Пользователь в среднем будет делать (посты, реакции, комментарии) -> (1, 10, 5) = 15 действий в сумме
- Локация только СНГ
- Храним всегда
- 1 000 000 подписчиков может быть у одного пользователя
- Максимальная задержка при отображении списка постов по месту: 3000 мс.
- Максимальная задержка при поиске популярных мест: 3000 мс.
- Максимальная задержка при загрузке поста - 2000мс.

![Screenshot of the structure of the system elements](/images-diagrams/data_structure.png)
![Screenshot of the structure of the system elements](/images-diagrams/allocation_by_databases.jpg)

## 📊 Оценка нагрузки (Load assessment)
- RPS(постов) = 100
- RPS(реакции) = 1000
- RPS(комментарии) = 500
### Расчет трафика (Traffic calculation)
- Трафик(изображений) = 100 * ( image(15mb) + meta(1kb) ) = 1.5gb
- Трафик(постов) = 100 * ( text(20kb) + meta(1kb) ) = 2.1 mb
- Трафик(реакции) = 1000 * (meta(1kb)) = 1mb
- Трафик(комментарии) = 500 * ( text(1kb) + meta(1kb) ) = 1000kb = 1mb
- Трафик(метаинформации - мета) = 1500RPS * 1kb = 1.5mb

## 📊 Оценка нагрузки в сезон(Load assessment * 10)
- RPS(постов) = 1000
- RPS(реакции) = 10000
- RPS(комментарии) = 5000
### Расчет трафика в сезон (Traffic calculation * 10)
- Трафик(постов) = 1.5gb * 10 = 15gb
- Трафик(реакции) = 1mb * 10 = 10mb
- Трафик(комментарии) = 1mb * 10 = 10mb
- Трафик(метаинформации - мета) = 1.5mb * 10 = 15mb

## Расчет одновременных соединений (Calculation of simultaneous connections)
Connections = 10 000 000 * 0.1 = 1 000 000

## Capacity
- Данные постов (текст поста + комментарии + реакции + метаинформации)
5.6mb * 86400 * 365 ~ 176tb
Пусть используем HDD 20TB. Тогда потребуется:
**176𝑇𝐵 /20𝑇𝐵= 9 дисков**

- изображения = image(15mb) * 100 = 1.5gb
1.5 gb * 86400 * 365 ~ 48pb

Общий RPS 1600 при скорости диска 100mb/s потребуются  16 дисков
при 1.5gb трафика при скорости диска 100mb/s потребуются  15 дисков
Чтобы уместить 48pb, если каждый диск 20TB, потребуется **2400 дисков**
**HDD: 2400 дисков**

**SSD не даст прироста так как мы упираемся не в IOPS. Но мы можем сжимать изображения!**
**Если мы используем WebP (80% качество, визуально почти неотличимо), то:**
**48PB / 5 = 9.6 PB** => **480 дисков**

## Оценка хостов

**PgSQL** основные компоненты(реляционная связи, транзакционная бд БД)
- sync RF 1
- async RF 1-2
- master-slave
- С учетом репликации (x3): 9×3= 27 дисков
Пусть на одном сервере 2 диска. Тогда:
**27/2= 14 серверов**
- шардироваться не нужно 

**S3**
- async RF 3
- После сжатия WebP (5x) у нас вышло ≈9.6PB.
Пусть используем HDD 20TB. Тогда:
9.6 𝑃𝐵 / 20𝑇𝐵=480дисков 

С учетом репликации (x3): 1440 дисков
Пусть на одном сервере 10 дисков: **144 серверов**
