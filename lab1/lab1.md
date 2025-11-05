# Лабораторная №1 — Настройка Nginx с HTTPS и виртуальными хостами

## Цели работы
- Настроить веб-сервер **nginx** с поддержкой HTTPS.  
- Реализовать автоматическое **перенаправление** HTTP (порт 80) → HTTPS (порт 443).  
- Использовать директиву **alias** для отдельных директорий.  
- Настроить **виртуальные хосты** для нескольких доменов.  
- Запустить **два pet-проекта** (HTML-страницы).

---

## Этапы выполнения

### 1. Установка Nginx
```bash
sudo apt update
sudo apt install nginx -y
```

<img width="1366" height="768" alt="Screenshot from 2025-10-06 01-41-57" src="https://github.com/user-attachments/assets/d1662c18-0711-404e-b0a8-03dfb5d5c65c" />

### 2. Конфигурация hosts
```bash
sudo nano /etc/hosts
```
Добавить строки:
```bash
127.0.0.1   project1.local
127.0.0.1   project2.local
```

<img width="1366" height="768" alt="Screenshot from 2025-10-06 01-48-48" src="https://github.com/user-attachments/assets/183a0d0f-7722-4f2d-8d21-08571afd1284" />

### 3. Создание pet-проектов

Project 1 → HTML-страница портфолио

Project 2 → HTML-страница блога

<img width="1366" height="768" alt="Screenshot from 2025-10-06 01-49-58" src="https://github.com/user-attachments/assets/a785f056-af59-4f7c-b2b4-7ad513747239" />


Пример содержимого (/var/www/project1/index.html):
<img width="1366" height="768" alt="Screenshot from 2025-10-06 01-52-23" src="https://github.com/user-attachments/assets/bd2fb625-8923-4cf1-a492-85b889a1fa18" />

Пример содержимого (/var/www/project2/index.html):
<img width="1366" height="768" alt="Screenshot from 2025-10-06 01-58-14" src="https://github.com/user-attachments/assets/f29f0d69-f51b-452c-aaec-c56d7fff7f87" />

### 4. Создание SSL-сертификата

<img width="1366" height="768" alt="Screenshot from 2025-10-06 02-01-57" src="https://github.com/user-attachments/assets/30dd8885-e545-43f7-8089-fee84e13b0b9" />

### 5. Настройка виртуальных хостов
Project 1
<img width="1366" height="768" alt="Screenshot from 2025-10-06 02-00-43" src="https://github.com/user-attachments/assets/b3f0a866-d9d6-4f10-9d90-91f4df7f95e4" />

Project 2
<img width="1366" height="768" alt="Screenshot from 2025-10-06 02-01-23" src="https://github.com/user-attachments/assets/ff4a515a-704d-4fc3-9595-a90a5443b403" />

Активация:
<img width="1366" height="768" alt="Screenshot from 2025-10-06 02-01-57" src="https://github.com/user-attachments/assets/d56f65a1-14b7-4fc6-b212-ac697e502e4d" />

<img width="1366" height="768" alt="Screenshot from 2025-10-06 02-02-16" src="https://github.com/user-attachments/assets/84a78de1-b0c8-4383-a2f2-bdf4368d72e5" />

### 6.Проверка и перезапуск
<img width="1366" height="768" alt="Screenshot from 2025-10-06 02-02-44" src="https://github.com/user-attachments/assets/2f2c6136-b0ac-405b-a4b1-3a2224cddfaa" />

## Результат

Сервер обслуживает два домена: project1.local и project2.local.

HTTP-запросы перенаправляются на HTTPS.

Pet-проекты доступны по HTTPS.

Виртуальные хосты работают корректно.


<img width="1366" height="768" alt="Screenshot from 2025-10-06 02-03-42" src="https://github.com/user-attachments/assets/c8fa9237-8a5d-454d-a75c-9353d0a2eb0f" />

<img width="1366" height="768" alt="Screenshot from 2025-10-06 02-04-06" src="https://github.com/user-attachments/assets/2829b221-0820-4a39-8963-26fa3da75743" />
