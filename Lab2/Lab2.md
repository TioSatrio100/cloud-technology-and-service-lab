# Лабораторная работа №2

# Ход работы
## 1. Плохой Dockerfile (bad.Dockerfile)
```bash
FROM ubuntu:latest

USER root

WORKDIR /app

COPY . .

RUN apt-get update && \
    apt-get install -y python3 python3-pip && \
    pip3 install flask && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN pip3 install -r requirements.txt

ENV APP_ENV=production
ENV DATABASE_URL=sqlite:///app.db
ENV SECRET_KEY=my-super-secret-key-12345

EXPOSE 8080

CMD python3 app.py
```
## 2. Хороший Dockerfile (good.Dockerfile)
```bash
FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /app

COPY requirements.txt .

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    python3 \
    python3-pip \
    curl && \
    pip3 install --no-cache-dir -r requirements.txt && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN groupadd -r appuser && useradd -r -g appuser appuser

COPY app.py .

RUN chown -R appuser:appuser /app

USER appuser

ENV APP_ENV=production
ENV PYTHONUNBUFFERED=1

EXPOSE 8080

CMD ["python3", "app.py"]
```
## 3. Файл app.py
```bash
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Docker container!"

if __name__ == '__main__':
    # Для демонстрации запускаем на всех интерфейсах
    app.run(host='0.0.0.0', port=5000)
```
## 4. Файл requirements.txt
```bash
flask==2.3.3
```
## 5. Плохие практики в bad.Dockerfile
| № | Плохая практика | Почему плохо | Как исправлено | Результат |
|---|------------------|---------------|----------------|--------|
| 1 | Использование `ubuntu:latest` без конкретной версии |-  `latest` нестабилен и может меняться со временем<br/> - Сборки в разное время могут использовать разные версии ОС<br/> - Невозможно гарантировать воспроизводимость сборок<br/> - Может привести к неожиданным изменениям в поведении приложения | Фиксирована версия (`FROM ubuntu:20.04`) | - Гарантированная воспроизводимость сборок<br/> - Контроль за версией базового образа<br/> - Предсказуемое поведение приложения |
| 2 | Копирование всех файлов до установки зависимостей (`COPY . .`) | Любое изменение кода ломает кэш установки зависимостей | Сначала копируется `requirements.txt`, затем устанавливаются зависимости | Эффективное кэширование и более быстрая сборка |
| 3 | Запуск от имени root пользователя | - Повышение привилегий создает угрозу безопасности<br/> - При компрометации контейнера атакующий получает root-доступ<br/> - Нарушает принцип минимальных привилегий | Объединили команды (`RUN groupadd -r myuser && useradd -r -g myuser myuser` и  `USER myuser` ) | - Улучшенная безопасность<br/> - Ограничение прав при компрометации контейнера<br/> - Соответствие best practices безопасности |
## 6. Плохие практики при работе с контейнерами
### Хранить данные внутри контейнера
**Проблема:**
Запуск контейнеров с данными, хранящимися внутри контейнерной файловой системы.

**Почему это плохо:**

- Данные теряются при удалении контейнера

- Невозможно обновить приложение без потери данных

- Затруднено резервное копирование

- Невозможно масштабировать приложение

**Пример неправильного использования:**

```bash
docker run -d --name my-database good-app
```
![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2/images/bad-prac.png)

**Правильный подход:**

```bash
# Использование volumes
docker run -d --name my-database1 -v postgres-data:/var/lib/postgresql/data my-app

# Или bind mounts
docker run -d --name my-database1 -v /host/path:/var/lib/postgresql/data my-app
```
![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2/images/good-prac.png)
### Запуск контейнеров с избыточными привилегиями
**Проблема:**
Запуск контейнеров с избыточными привилегиями

**Почему это плохо:**

- Флаг `--privileged` дает контейнеру полный доступ к хостовой системе

- Монтирование корневой файловой системы хоста (/) крайне опасно

- Контейнер может модифицировать хост-систему

**Пример неправильного использования:**

```bash
docker run --privileged -v /:/host -p 5000:5000 my-app
```
**Правильный подход:**

```bash
docker run --user 1000:1000 --read-only -v /tmp/app-data:/data -p 5000:5000 my-app
```
![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2/images/prac2.png)
## 7. Запуск проекта
```bash
docker build -t bad-app -f bad.Dockerfile .
docker run -p 5000:5000 bad-app
```
### Результат:
![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2/images/bad1.png)

![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2/images/bad2.png)
```bash
docker build -t good-app -f good.Dockerfile .
docker run -p 5000:5000 good-app
```
### Результат:
![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2/images/good1.png)

![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2/images/good2.png)

![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2/images/run.png)

![1](https://github.com/Tran16062002/Cloud_DevOps/blob/main/Lab2/images/check.png)

# Вывод
- В этой лабораторной работе показано, как выбор базового образа, порядок инструкций и настройка окружения
влияет на размер, безопасность и удобство использования контейнера.
- В результате работы были получены практические навыки по оптимизации Dockerfile и осознание важности
соблюдения принципов минимализма, безопасности и воспроизводимости при создании контейнеров.
