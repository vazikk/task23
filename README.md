# task23

Поднял Jenkins на отдельном пользователе Jenkins: <br>
<img width="427" height="56" alt="image" src="https://github.com/user-attachments/assets/6e16f525-e701-462b-b061-fd71098af9ed" /> <br>

<img width="928" height="278" alt="image" src="https://github.com/user-attachments/assets/68e4f88a-b57a-4d0b-8920-95ecb3d558aa" /> <br>

______________________________________________________________________________________________________________________________________
Подключил Git, написал там Dockerfile и html код: <br>

<img width="1160" height="390" alt="image" src="https://github.com/user-attachments/assets/54117d6c-6797-4aae-b79a-0ee5a33bd31a" /> <br>
<img width="391" height="148" alt="image" src="https://github.com/user-attachments/assets/023cbf86-db58-4b92-af77-3b8d8140a481" /> <br>

```
FROM ubuntu:20.04

RUN apt-get update && \
    apt-get install -y nginx && \
    apt-get clean

COPY ./html /var/www/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

```
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Простой Сайт</title>
    <style>
        body {
            text-align: center;
            margin-top: 50px;
        }
    </style>
</head>
<body>
    <h1>Добро пожаловать! Проходите уже ёмае!</h1>
    <p>Это простой сайт.</p>
</body>
</html>
```
_______________________________________________________________________________________________________________________

Подключил Jenkins-agent: <br>

<img width="1688" height="215" alt="image" src="https://github.com/user-attachments/assets/dcf48086-1475-46aa-abcd-4be002980a68" />

_______________________________________________________________________________________________________________________

Подключил Credentials: <br>

<img width="1351" height="281" alt="image" src="https://github.com/user-attachments/assets/3bee3739-e7b0-4723-92d6-9fbce34a3e27" /> <br>

________________________________________________________________________________________________________________________

Добавил WebHook: <br>

<img width="1061" height="142" alt="image" src="https://github.com/user-attachments/assets/94b7a67c-c3ca-4d0e-8afd-192ff2dee7d5" /> <br>

____________________________________________________________________________________________________________________________

Создал Personal Access Token: <br>

<img width="1317" height="415" alt="image" src="https://github.com/user-attachments/assets/07076fce-9fb4-467b-a0a3-43f6a65b0014" /> <br>

________________________________________________________________________________________________________________________________










