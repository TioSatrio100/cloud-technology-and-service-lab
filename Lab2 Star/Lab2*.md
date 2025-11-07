# Лабораторная работа №2 со звездочкой

# Ход работы
## 1. Плохой docker-compose.yml (docker-compose.bad.yml)
```bash
version: '2.0'

services:
  web:
    image: nginx:latest   # bad practice 1: использовать latest
    ports:
      - "5000:80"
    environment:
      - ENV=production
    restart: always

  db:
    image: postgres      # bad practice 2: не указывать версию
    environment:
      POSTGRES_PASSWORD: mysecretpassword
    volumes:
      - db_data:/var/lib/postgresql/data

  cache:
    image: redis          # bad practice 3: отсутствие ресурсов, зависит от остальных
    restart: always
    environment:
      - REDIS_PASSWORD=secret

volumes:
  db_data:
```
## 2. Хороший docker-compose.yml (docker-compose.good.yml)
```bash
version: '2.0'

services:
  web:
    image: nginx:1.25.0        # фиксированная версия
    ports:
      - "5000:80"
    environment:
      ENV: production
    restart: unless-stopped
    networks:
      - isolated_network


  db:
    image: postgres:16.1       # фиксированная версия
    environment:
      POSTGRES_PASSWORD: mysecretpassword
    volumes:
      - db_data:/var/lib/postgresql/data
    networks:
      - isolated_network

  cache:
    image: redis:7.2
    environment:
      REDIS_PASSWORD: secret
    networks:
      - isolated_network


volumes:
  db_data:

networks:
  isolated_network:
    driver: bridge
```
## 3. Плохие практики в docker-compose.bad.yml
| № | Плохая практика | Почему плохо | Как исправлено | Эффект |
|---|------------------|---------------|----------------|--------|
| 1 | Использование `latest` или не указана версия | При сборке может подтянуться любая новая версия образа, что приведёт к неожиданным поломкам. | Фиксирована версия (`nginx:1.25.0`, `postgres:16.1`, `redis:7.2`) | Сборка всегда предсказуема |
| 2 | Формат переменных окружения `- ENV=production` | Используется старый синтаксис списка, который иногда сложно читать | Использована более современный и читаемый синтаксис `ENV: production` | Улучшает читаемость и поддержку Compose файлов |
| 3 | Настройки перезапуска `restart: always` | Контейнер будет постоянно перезапускаться, даже если пользователь остановил проект вручную. | Использован `restart: unless-stopped` | Удобнее управлять контейнерами без неожиданных перезапусков. |
## 4. Как контейнеры поднимаются, но не "видят" друг друга
### Принцип изоляции:
В изолированной версии каждый сервис помещен в отдельную Docker network. Docker создает изолированные сетевые пространства, где контейнеры могут общаться только с теми, кто находится в той же сети. 

### Как достигнута изоляция:  
- Раздельные сети: Каждый сервис имеет свою собственную сеть (`web_network`, `db_network`, `cache_network`)

- Отсутствие общих сетей: Сервисы не разделяют общую сеть

- **Bridge driver**: Каждая сеть использует **bridge driver**, создавая изолированный сетевой мост
### Изолированный Docker Compose файл
```bash
version: '2.0'

services:
  web:
    image: nginx:1.25.0        # фиксированная версия
    ports:
      - "5000:80"
    environment:
      ENV: production
    restart: unless-stopped
    networks:
      - web_network


  db:
    image: postgres:16.1       # фиксированная версия
    environment:
      POSTGRES_PASSWORD: mysecretpassword
    volumes:
      - db_data:/var/lib/postgresql/data
    networks:
      - db_network

  cache:
    image: redis:7.2
    environment:
      REDIS_PASSWORD: secret
    networks:
      - cache_network


volumes:
  db_data:

networks:
  web_network:
    driver: bridge
  db_network:
    driver: bridge
  cache_network:
    driver: bridge
```
![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2*/Images/run2.png)

![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2*/Images/list2.png)

### В результате:
В хорошем файле контейнеры помещены в разные сети:
- `web` в `web_network`

![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2*/Images/web.png)

- `db` в `db_network`

![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2*/Images/db.png)

- `cache` в `cache_network`

![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2*/Images/cache.png)

## 5. Как запустить
```bash
sudo docker-compose -f docker-compose.bad.yml up -d
```
```bash
sudo docker-compose -f docker-compose.good.yml up -d
```
![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2*/Images/run.png)

### Проверка:

![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2*/Images/list1.png)

![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2*/Images/bad1.png)

![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2*/Images/good1.png)

# Вывод
- В результате работы были продемонстрированы плохие и хорошие практики при создании Docker Compose файлов.
- Исправленные ошибки позволили:

повысить безопасность (нет утечек секретов, нет root-доступа к хосту),

обеспечить повторяемость сборки (зафиксированные версии образов),

сделать архитектуру чище и изолированнее (разные внутренние сети),

упростить управление настройками через .env.

- Также была реализована изоляция контейнеров на уровне сети, что предотвращает их прямое взаимодействие при 
совместном запуске, сохраняя при этом их одновременный запуск и управление через один Compose-проект.
