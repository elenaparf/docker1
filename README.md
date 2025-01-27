# Практическое применение Docker—  Парфенова Елена
# Задача 0

Убедитесь что у вас НЕ(!) установлен docker-compose, для этого получите следующую ошибку от команды docker-compose --version
Command 'docker-compose' not found, but can be installed with:

sudo snap install docker          # version 24.0.5, or
sudo apt  install docker-compose  # version 1.25.0-1

See 'snap info docker' for additional versions.

В случае наличия установленного в системе docker-compose - удалите его.
 2. Убедитесь что у вас УСТАНОВЛЕН docker compose(без тире) версии не менее v2.24.X, для это выполните команду docker compose version
# Решение 0
Произведено удаление "docker-compose" 
![docker1](https://github.com/user-attachments/assets/d30bcb85-9b99-476c-a310-44bb8065783b)
# Задача 1

Сделайте в своем github пространстве fork репозитория. Примечание: В связи с доработкой кода python приложения. Если вы уверены что задание выполнено вами верно, а код python приложения работает с ошибкой то используйте вместо main.py файл not_tested_main.py(просто измените CMD)
Создайте файл с именем Dockerfile.python для сборки данного проекта(для 3 задания изучите https://docs.docker.com/compose/compose-file/build/ ). Используйте базовый образ python:3.9-slim. Обязательно используйте конструкцию COPY . . в Dockerfile. Не забудьте исключить ненужные в имадже файлы с помощью dockerignore. Протестируйте корректность сборки.
(Необязательная часть, *) Изучите инструкцию в проекте и запустите web-приложение без использования docker в venv. (Mysql БД можно запустить в docker run).
(Необязательная часть, *) По образцу предоставленного python кода внесите в него исправление для управления названием используемой таблицы через ENV переменную.
ВНИМАНИЕ!
!!! В процессе последующего выполнения ДЗ НЕ изменяйте содержимое файлов в fork-репозитории! Ваша задача ДОБАВИТЬ 5 файлов: Dockerfile.python, compose.yaml, .gitignore, .dockerignore,bash-скрипт. Если вам понадобилось внести иные изменения в проект - вы что-то делаете неверно!

# решение 1

Сделаем fork репозитория 
![docker2](https://github.com/user-attachments/assets/3e1d399a-ed0c-4e5a-9b6d-150bc737c295)

листинг Dockerfile.python


  FROM python:3.9-slim

  WORKDIR /app

  COPY requirements.txt ./

  RUN pip install -r requirements.txt

  COPY main.py ./

  CMD ["python", "main.py"]


листинг .dockerignore

    # Git

  .git

  .gitignore

  .gitattributes

    # Python

  __pycache__/

  .py[cod]

  *.pyo

  *.egg-info

  *.egg


    # Общие папки сборки
  dist/
  
  build/
  
  bin/
  
  obj/
  
  lib/
  

    # Переменные окружения
  .env
  
  .env.local

    # Конфигурации IDE и редакторов
  .idea/
  
  .vscode/
  
  *.iml
  
  *.suo
  
  *.user
  
  *.db
  
  *.sln

    # Локальные настройки
  *.local

    # Python кэш
  *.pyo
  
  __pycache__/

    # Docker кэш
  docker-compose.override.yml

  листинг .gitignore
  
    # Переменные окружения
  .env
  
  .env.local
  
  .env.*.local

    # Конфигурации редакторов
    
  .idea/
  
  .vscode/
  
   *.iml

    # Локальные настройки
    
  *.local

    # Общие временные файлы
    
  *.log
  
  *.pid
  
  *.seed
  
  *.pid.lock

    # Python кэш
    
  *.pyc
  
  *.pyo
  
  *.pyd
  
  __pycache__/

    # Docker
    
  docker-compose.override.yml

  .dockerignore

    команда для сборки образа
  docker build -f Dockerfile.python -t dock .

![docker3](https://github.com/user-attachments/assets/f096e406-cb8f-4bcf-9452-a990d5a4a15c)

# Задача 2 (*)
    Создайте в yandex cloud container registry с именем "test" с помощью "yc tool" .
  
   Настройте аутентификацию вашего локального docker в yandex container registry.

  Соберите и залейте в него образ с python приложением из задания №1.

  Просканируйте образ на уязвимости.

  В качестве ответа приложите отчет сканирования.

# решение 2

    создаем контейнер в yandex cloud container registry

  yc container registry create --name test

    сконфигурируем docker для использования
yc container registry configure-docker

    проверем что все сконфигурировано
/home/lamer/.docker/config.json

    тегируем образ
docker tag dock cr.yandex/crpfc95k6pu9pkbp828q/dock:latest

    загрузим образ в реестр
docker push cr.yandex/crpfc95k6pu9pkbp828q/dock:latest


![docker6](https://github.com/user-attachments/assets/1eae0f35-c4c9-4062-8a7d-cee68e87f399)

перейдем в яндекс облако и произведем сканирование на наличие уязвимостей


![docker4](https://github.com/user-attachments/assets/cffc5416-0eed-4437-92d5-b2c4e5690699)

![docker5](https://github.com/user-attachments/assets/bb57dbf6-0e05-48d0-ab1a-0ba3517be87c)

![docker7](https://github.com/user-attachments/assets/5623d0f6-4b95-42fb-9e13-f7d50fb7d50d)

# Задача 3

Изучите файл "proxy.yaml"

Создайте в репозитории с проектом файл compose.yaml. С помощью директивы "include" подключите к нему файл "proxy.yaml".

Опишите в файле compose.yaml следующие сервисы:

web. 
Образ приложения должен ИЛИ собираться при запуске compose из файла Dockerfile.python ИЛИ скачиваться из yandex cloud container registry(из задание №2 со *). Контейнер должен работать в bridge-сети с названием backend и иметь фиксированный ipv4-адрес 172.20.0.5. Сервис должен всегда перезапускаться в случае ошибок. Передайте необходимые ENV-переменные для подключения к Mysql базе данных по сетевому имени сервиса web


db. image=mysql:8. 
Контейнер должен работать в bridge-сети с названием backend и иметь фиксированный ipv4-адрес 172.20.0.10. Явно перезапуск сервиса в случае ошибок. Передайте необходимые ENV-переменные для создания: пароля root пользователя, создания базы данных, пользователя и пароля для web-приложения.Обязательно используйте уже существующий .env file для назначения секретных ENV-переменных!


Запустите проект локально с помощью docker compose , добейтесь его стабильной работы: команда curl -L http://127.0.0.1:8090 должна возвращать в качестве ответа время и локальный IP-адрес. Если сервисы не стартуют воспользуйтесь командами: docker ps -a и docker logs <container_name> . Если вместо IP-адреса вы получаете NULL --убедитесь, что вы шлете запрос на порт 8090, а не 5000.


Подключитесь к БД mysql с помощью команды docker exec -ti <имя_контейнера> mysql -uroot -p<пароль root-пользователя>(обратите внимание что между ключем -u и логином root нет пробела. это важно!!! тоже самое с паролем) . Введите последовательно команды (не забываем в конце символ ; ): show databases; use <имя вашей базы данных(по-умолчанию example)>; show tables; SELECT * from requests LIMIT 10;.


Остановите проект. В качестве ответа приложите скриншот sql-запроса.


рабочий файл находитья в репозитории [fork репозитория shvirtd-example-python](https://github.com/ysatii/shvirtd-example-python.git)

![docker8](https://github.com/user-attachments/assets/e3846363-648f-4a3d-b9e5-e57a08dd8136)

![docker9](https://github.com/user-attachments/assets/8776dceb-8070-47ec-9fc4-ae423eb723dc)

Подключитесь к БД mysql


  docker exec -it shvirtd-example-python-db-1 bash

  mysql -uroot -p

  show databases;

  show tables;

  SELECT * from requests LIMIT 10;

  ![docker10](https://github.com/user-attachments/assets/70486984-f899-4f45-a57c-ca4a3a21016a)

![docker11](https://github.com/user-attachments/assets/d95a0091-ef70-47da-9516-e1712a14df91)

# Задача 4

Запустите в Yandex Cloud ВМ (вам хватит 2 Гб Ram).
Подключитесь к Вм по ssh и установите docker.
Напишите bash-скрипт, который скачает ваш fork-репозиторий в каталог /opt и запустит проект целиком.
Зайдите на сайт проверки http подключений, например(или аналогичный): https://check-host.net/check-http и запустите проверку вашего сервиса http://<внешний_IP-адрес_вашей_ВМ>:8090. Таким образом трафик будет направлен в ingress-proxy. ПРИМЕЧАНИЕ: приложение main.py( в отличие от not_tested_main.py) весьма вероятно упадет под нагрузкой, но успеет обработать часть запросов - этого достаточно. Обновленная версия (main.py) не прошла достаточного тестирования временем, но должна справиться с нагрузкой.
(Необязательная часть) Дополнительно настройте remote ssh context к вашему серверу. Отобразите список контекстов и результат удаленного выполнения docker ps -a
В качестве ответа повторите sql-запрос и приложите скриншот с данного сервера, bash-скрипт и ссылку на fork-репозиторий.

# решение 4

подготовим виртуальную машину на яндекс облаке. 

![docker12](https://github.com/user-attachments/assets/523e59a4-c4f0-4018-a1d7-37f90e63619e)

Установим docker с помощью скрипта


#!/bin/bash

 Проверяем, выполняется ли скрипт с правами root
 
    if [ "$EUID" -ne 0 ]; then
  
      echo "Пожалуйста, запустите скрипт с правами root (sudo)."
  
      exit 1
  
    fi

 Обновляем систему
 
  echo "Обновляем списки пакетов..."

  apt-get update -y

 Устанавливаем необходимые пакеты
 
echo "Устанавливаем необходимые зависимости..."

  apt-get install -y \

  ca-certificates \
  
  curl \
  
  gnupg \
  
 lsb-release

 Добавляем GPG-ключ Docker
 
  echo "Добавляем ключ Docker GPG..."

  mkdir -p /etc/apt/keyrings

  curl -fsSL https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]')/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg

 Добавляем репозиторий Docker
 
  echo "Добавляем Docker-репозиторий..."

echo \

  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]') \
  
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
  

 Обновляем списки пакетов
 
  echo "Обновляем списки пакетов (после добавления репозитория)..."

  apt-get update -y


 Устанавливаем Docker
 
  echo "Устанавливаем Docker CE, CLI и контейнерный runtime..."

  apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin


 Проверяем установку
 
  echo "Проверяем версию Docker..."

  docker --version


 Устанавливаем автозапуск Docker
 
  echo "Включаем автозапуск Docker..."

  systemctl enable docker

  systemctl start docker

  echo "Установка Docker завершена!"

![docker13](https://github.com/user-attachments/assets/3880ef3e-2375-4a6e-8794-e3063808d806)

![docker14](https://github.com/user-attachments/assets/d287c246-73f0-4c61-86a1-fdd0ea4797ae)

выполнение команды docker ps -a

![docker15](https://github.com/user-attachments/assets/4c730a85-aba8-441b-8bb6-e8b90fe26ae0)

ссылка на fork репозиторий https://github.com/ysatii/shvirtd-example-python

![docker16](https://github.com/user-attachments/assets/adad71fe-ca98-491a-98e2-88f14f77a773)

# Задача 5 (*)
Напишите и задеплойте на вашу облачную ВМ bash скрипт, который произведет резервное копирование БД mysql в директорию "/opt/backup" с помощью запуска в сети "backend" контейнера из образа schnitzler/mysqldump при помощи docker run ... команды. Подсказка: "документация образа."
Протестируйте ручной запуск
Настройте выполнение скрипта раз в 1 минуту через cron, crontab или systemctl timer. Придумайте способ не светить логин/пароль в git!!
Предоставьте скрипт, cron-task и скриншот с несколькими резервными копиями в "/opt/backup"

# Задача 6
Скачайте docker образ hashicorp/terraform:latest и скопируйте бинарный файл /bin/terraform на свою локальную машину, используя dive и docker save. Предоставьте скриншоты действий .

# ответ 6

Сохраните образ Docker в файл

docker save -o terraform.tar hashicorp/terraform:latest

Распакуйте содержимое сохранённого образа:

mkdir terraform_image

tar -xf terraform.tar -C terraform_image

найдем файл /bin/terraform

find extracted_layers -type f -executable -name "*terraform*"

ответ extracted_layers/bin/terraform

скопируем файл себе в домашнюю директорию

cp extracted_layers/bin/terraform ~/terraform

![docker17](https://github.com/user-attachments/assets/0a6dfa4a-8253-442a-aa8a-be2391435db7)

# Задача 6.1

Добейтесь аналогичного результата, используя docker cp.

 Предоставьте скриншоты действий .
 
# Ответ 6.1

Скачиваем образ

docker pull hashicorp/terraform:latest

создаем контейнер

docker run -d --name terraform-container hashicorp/terraform:latest tail -f /dev/null

копируем файл используя docker cp

docker cp terraform-container:/bin/terraform ~/terraform

# Задача 6.2 (**)

Предложите способ извлечь файл из контейнера, используя только команду docker build и любой Dockerfile.

 Предоставьте скриншоты действий .
 
# Задача 7 (***)
Запустите ваше python-приложение с помощью runC, не используя docker или containerd.

 Предоставьте скриншоты действий .




