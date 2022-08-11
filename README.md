# Foodgram-project-react (grocery_assistant)

 <!--
![Foodgram-project-react](https://github.com/2artem/foodgram-project-react/actions/workflows/main.yml/badge.svg)
-->

Адреса развернутого приложения:
```
http://158.160.9.55/ - ПРИОСТАНОВЛЕНО
```
## Описание проекта:
Проект Foodgram продуктовый помощник - платформа для публикации рецептов.
Cайт, на котором пользователи будут публиковать рецепты, добавлять чужие рецепты в избранное и подписываться на публикации других авторов. Сервис «Список покупок» позволит пользователям создавать список продуктов, которые нужно купить для приготовления выбранных блюд.

## Тестирование и описание работы:
Вы можете склонировать проект у себе на локальную машину, далее создать свой репозиторий с проектом. Для запуска приложения необходимо создать сервер, сдружить его с Вашим GitHub, создать 2 своих репозитроия на DockerHub (frontend/backend) и далее совершенствовать проект, который будет пересобираться и тестироваться с помощью workflow Actions GitHub по пушу (push) в главную ветку (с отправкой отчета в телеграм). 

По окончании запуска приложения, Вы можете вручную создать суперпользователя и далее все остальные объекты. Для логической целостности, при создании рецептов необходимо иметь теги и ингредиенты в БД.
Предлагается загрузить первоначальные данные в БД при помощи фикстур (см. раздел 5).
Юзеры фикстур:
```
superuser@mail.ru
adminuser@mail.ru
user@mail.ru
```
Их общий пароль:
```
allTestUsersPASS$!
```

## Запуск приложения.
### 1.Склонировать репозиторий на локальную машину
```
git clone git@github.com:2artem/foodgram-project-react.git
```
### 2.Настройка сервера (на Ubuntu)
Подключаемся к виртуальной машине с линукс
```
ssh admin@158.160.9.55
```
обновим индекс менеджера пакетов apt
```
sudo apt update
```
обновим установленные в системе пакеты
```
sudo apt upgrade -y
```
установим систему контроля версий, утилиту для создания виртуального окружения и менеджер пакетов
```
sudo apt install python3-pip python3-venv git -y
```
##### установим Docker и docker-compose 
__

Установка утилиты для скачивания файлов и ее запуск
```
sudo apt install curl
sudo curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo apt remove docker docker-engine docker.io containerd runc
sudo apt update
```
Установить необходимые пакеты для загрузки через https
```
sudo apt install \
  apt-transport-https \
  ca-certificates \
  curl \
  gnupg-agent \
  software-properties-common -y
```
В консоли должно вывестись ОК.
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

sudo apt update 

sudo apt install docker-ce docker-compose -y

sudo systemctl status docker
```
##### Сдружить GitHub и сервер 
__
Дадим доступ к сервису GitHub нашему серверу, сгенерируем публичный ssh-ключ
```
ssh-keygen (Enter, плюс ввод создание пароля для ключа)
```
вывод ключа
```
cat ~/.ssh/id_rsa.pub
```
GitHub-> Settings->SSH and GPG keys->Add SSH key->добавить ключ его название
__
Перенесем файлы из проекта docker-compose.yaml и nginx/default.conf на сервер,
в home/<ваш_username>/docker-compose.yaml и home/<ваш_username>/nginx/default.conf соответственно,
так как на основании них и будут собраны все настройки проекта.
На сервере:
```
pwd   # /home/admin
mkdir nginx
```
На рабочем компьютере, в папке infra/:
копируем yaml
```
scp docker-compose.yml admin@158.160.9.55:/home/admin
scp nginx.conf admin@158.160.9.55:/home/admin/nginx
```

### 3.Создание докер-образов на DockerHub.
Запушим докер-образы на DockerHub (по лекалам Dockerfile).
```
sudo docker build -t avchasovskikh/foodgram_frontend:latest .
sudo docker build -t avchasovskikh/foodgram_backend:latest .
sudo docker login -u avchasovskikh (пароль)
sudo docker push avchasovskikh/foodgram_frontend:latest
sudo docker push avchasovskikh/foodgram_backend:latest
```

### 4.DevOps (Development Operations) —  методика увеличения скорости, качества и безопасности разработки.
Continuous Integration (англ. «непрерывная интеграция», сокращенно CI) состоит в том, чтобы после внесения изменений в любую часть кода проводилось тестирование не только того модуля, который был изменён, но и всего проекта.
GitHub Actions — это облачный сервис, инструмент для автоматизации процессов тестирования и деплоя проектов.
Для подключения GitHub Actions создана директория .github/workflows, а в ней — yml-файл.
В файле .yml декларативно описывается workflow, процесс автоматизации: пошаговые команды и условия их выполнения. 
##### Добавим переменные окружения на GitHub Actions.
__
2artem/foodgram-project-react -> Settings -> Secrets -> Actions -> New repositiry secret:

* DOCKER_PASSWORD # пароль от DockerHub
* DOCKER_USERNAME # имя пользователя на DockerHub
* DB_ENGINE=django.db.backends.postgresql # указываем, что работаем с postgresql
* DB_NAME = postgres # имя базы данных
* POSTGRES_USER = postgres # логин для подключения к базе данных
* POSTGRES_PASSWORD = postgres # пароль для подключения к БД (установите свой)
* DB_HOST = db # название сервиса (контейнера!)
* DB_PORT = 5432 # порт для подключения к БД 
* TELEGRAM_TO # id своего телеграм-аккаунта (@userinfobot)
* TELEGRAM_TOKEN # токен бота (@BotFather)
* HOST # ip-адрес сервера
* PASSPHRASE  # пароль от ssh-ключа, на сервере 
* USER  # логин пользователя на сервере, от ssh-ключа
* SSH_KEY # приватный ssh ключ локального комьютера имеющего доступ к серверу (cat ~/.ssh/id_rsa)

##### Теперь при коммите в главную ветку, наше приложение будет тестироваться и деплоиться на сервер.

### 5.Инициализация БД и статики (только после первого деплоя)
Подключаемся к серверу через ssh и выполняем миграции БД.
```
sudo docker-compose exec web python manage.py makemigrations
sudo docker-compose exec web python manage.py migrate
```
Собираем статические файлы
```
sudo docker-compose exec web python manage.py collectstatic --no-input
```
По желанию, загружаем фикстуры
```
sudo docker-compose exec web python manage.py loaddata fixtures.json
```

### Под капотом

При внесении изменения в проект (master-ветка) - происходит workflow, запуск блоков команд.
Последовательно будут выполнены следующие блоки:
* "tests" - тестирование проекта на соответствие PEP-8 и тестам.
* "build_and_push_to_docker_hub" - сборка образов (image) для запуска docker-контейнеров и отправка их на DockerHub.
* "deploy" - деплой проекта на сервере с образов уже находящихся на DockerHub.
Происходит копирование файлов из репозитория на сервер, сборка и запуск контейнеров:
  - docker-compose.yaml, необходимый для сборки трех контейнеров:
    + db - контейнер базы данных.
    + web- контейнер Django приложения + wsgi-сервер gunicorn.
    + frontend - контейнер с файлами фронтенда и статики (после сборки - останавливается).
    + nginx - проксирующий веб-сервер.
* "send_message" - после сборки и запуска контейнеров происходит отправка сообщения в телеграм, об успешном окончании workflow.

## Останов приложения (контейнеров)
### Завершить работу контейнеров можно командой
```
sudo docker-compose down -v
```
### Сброс данных (при необходимости). 
```
sudo docker system prune --all --force
sudo docker system prune -a --volumes
```

## Авторы

* **Фронтенд - Яндекс.Практикум**

* **Бекенд - Часовских Артем** [achasovskikh.ru](http://achasovskikh.ru)
