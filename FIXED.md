# Виправлення проєкту (6 кроків)

1. Узгоджено змінну середовища для бекенду.
- Файл: compose.yaml
- Було: APP_MODE: dev
- Стало: APP_ENV: dev
- Причина: застосунок читав APP_ENV, через що виникала помилка конфігурації.

2. Додано безпечне читання env у Flask.
- Файл: app/main.py
- Було: os.environ["APP_ENV"]
- Стало: os.getenv("APP_ENV", "dev")
- Причина: контейнер не падає, якщо змінну середовища не передано.

3. Виправлено точку входу Gunicorn.
- Файл: Dockerfile
- Було: main:application
- Стало: main:app
- Причина: у коді Flask-об'єкт має ім'я app, а не application.

4. Узгоджено порт бекенду на 8080.
- Файли: Dockerfile, app/main.py, compose.yaml
- Зміни: EXPOSE 8080, gunicorn на 0.0.0.0:8080, health endpoint орієнтується на 8080
- Причина: прибрано розсинхрон між 8000 і 8080.

5. Виправлено шлях до статичних файлів фронтенду.
- Файл: caddy/Caddyfile
- Було: /srv/www/build
- Стало: /srv/www/dist
- Причина: frontend build складає файли в /out/dist, який монтується як /srv/www/dist.

6. Виправлено роутинг Traefik до Caddy та healthcheck app.
- Файл: compose.yaml
- Зміни:
  - traefik.http.services.caddy.loadbalancer.server.port: 80 (замість 8080)
  - healthcheck для app переведено з curl на python urllib
- Причина: Caddy у контейнері слухає 80, а curl не гарантовано доступний у python-образі.
