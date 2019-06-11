# Docker Cookbook [Rus]


###  Кое-какие Docker команды

В некоторых примерах будет использоваться образ Ubuntu
https://hub.docker.com/_/ubuntu
```bash
docker pull ubuntu
```

(*TODO*) Эти команды (_будут_) продублированы в моём гисте: https://gist.github.com/ozor/b5dad4b74cbb61379f00779cfacacbd4

#### Общие команды

```bash
docker run ubuntu echo "Hello World"

# Запустим командную оболочку shell внутри контейнера, а так же зададим для него имя хоста с помощью флага -h
docker run -h CONTAINER -it ubuntu /bin/bash

# Предположим, что id нашего docker-контейнера: 0077799fdbfa

# Получить информацию о контейнере (можно передать имя или id контейнера)
docker inspect 0077799fdbfa
docker inspect 0077799fdbfa | grep IPAddress
docker inspect --format {{.NetworkSettings.IPAddress}} 0077799fdbfa

# Посмотреть список файлов, измененных в работающем контейнере
docker diff 0077799fdbfa

# Посмотреть список всех событий, произошедших внутри заданного контейнера
docker logs 0077799fdbfa
```

#### Основные действия с контейнером

```bash
# Создание контейнера
docker create -t -i some/name01 --name MyContainer

# Первый запуск контейнера
docker run -it --name MyContainer -d some/name01

# Переименование контейнера
docker rename MyContainer MyContainerRenamed

# Удаление контейнера
docker rm MyContainer

# Обновление контейнера
docker update --cpu-shares 512 -m 300M MyContainer
```

#### Запуск и остановка контейнеров

```bash
# Запуск остановленного контейнера
docker start nginx

# Остановка
docker stop nginx

# Перезагрузка
docker restart nginx

# Пауза (приостановка всех процессов контейнера)
docker pause nginx

# Снятие паузы
docker unpause nginx

# Блокировка (до остановки контейнера)
docker wait nginx

# Отправка SIGKILL (завершающего сигнала)
docker kill nginx

# Отправка другого сигнала
docker kill -s HUP nginx

# Подключение к существующему контейнеру
docker attach nginx
```

#### Получение информации о контейнерах

```bash
# Работающие контейнеры
docker ps
# Все контейнеры
docker ps -a

# Логи контейнера
docker logs infinite

# Информация о контейнере
docker inspect infinite
docker inspect --format '{{ .NetworkSettings.IPAddress }}' $(docker ps -q)

# События контейнера
docker events infinite

# Публичные порты
docker port infinite

# Выполняющиеся процессы
docker top infinite

# Использование ресурсов
docker stats infinite

# Изменения в файлах или директориях файловой системы контейнера
docker diff infinite
```

#### Управление образами

```bash
# Список образов
docker images

# Создание образов
docker build .
docker build github.com/creack/docker-firefox
docker build - < Dockerfile
docker build - < context.tar.gz
docker build -t eon/infinite .
docker build -f myOtherDockerfile .
curl example.com/remote/Dockerfile | docker build -f - .

# Удаление образа
docker rmi nginx

# Загрузка репозитория в tar (из файла или стандартного ввода)
docker load < ubuntu.tar.gz
docker load --input ubuntu.tar

# Сохранение образа в tar-архив
docker save busybox > ubuntu.tar

# Просмотр истории образа
docker history

# Создание образа из контейнера
docker commit nginx

# Тегирование образа
docker tag nginx eon01/nginx

# Push (загрузка в реестр) образа
docker push eon01/nginx
```

#### Сеть

```bash
# Создание сети
docker network create -d overlay MyOverlayNetwork
docker network create -d bridge MyBridgeNetwork
docker network create -d overlay \
  --subnet=192.168.0.0/16 \
  --subnet=192.170.0.0/16 \
  --gateway=192.168.0.100 \
  --gateway=192.170.0.100 \
  --ip-range=192.168.1.0/24 \
  --aux-address="my-router=192.168.1.5" --aux-address="my-switch=192.168.1.6" \
  --aux-address="my-printer=192.170.1.5" --aux-address="my-nas=192.170.1.6" \
  MyOverlayNetwork

# Удаление сети
docker network rm MyOverlayNetwork

# Список сетей
docker network ls

# Получение информации о сети
docker network inspect MyOverlayNetwork

# Подключение работающего контейнера к сети
docker network connect MyOverlayNetwork nginx

# Подключение контейнера к сети при его запуске
docker run -it -d --network=MyOverlayNetwork nginx

# Отключение контейнера от сети
docker network disconnect MyOverlayNetwork nginx
```

#### Очистка Docker

```bash
# Удаление работающего контейнера
docker rm nginx

# Удаление контейнера и его тома (volume)
docker rm -v nginx

# Удаление всех контейнеров со статусом exited
docker rm $(docker ps -a -f status=exited -q)

# Удаление всех остановленных контейнеров
docker container prune
docker rm `docker ps -a -q`

# Удаление контейнеров, остановленных более суток назад
docker container prune --filter "until=24h"

# Удаление образа
docker rmi nginx

# Удаление неиспользуемых (dangling) образов
docker image prune
docker rmi $(docker images -f dangling=true -q)

# Удаление неиспользуемых (dangling) образов даже с тегами
docker image prune -a

# Удаление всех образов
docker rmi $(docker images -a -q)

# Удаление всех образов без тегов
docker rmi -f $(docker images | grep "^<none>" | awk "{print $3}")

# Остановка и удаление всех контейнеров
docker stop $(docker ps -a -q) && docker rm $(docker ps -a -q)
# Более интересная команда для остановки и удаления всех контейнеров
docker rm $(docker stop $(docker ps -aq --format="{{.ID}}"))

# Удаление неиспользуемых (dangling) томов
docker volume prune
docker volume rm $(docker volume ls -f dangling=true -q)

# Удаление неиспользуемых (dangling) томов по фильтру
docker volume prune --filter "label!=keep"

# Удаление неиспользуемых сетей
docker network prune

# Удаление всех неиспользуемых объектов
docker system prune

# По умолчанию для Docker 17.06.1+ тома не удаляются. Чтобы удалились и они тоже:
docker system prune --volumes
```

#### Реестры и репозитории Docker

```bash
# Вход в реестр
docker login
docker login localhost:8080

# Выход из реестра
docker logout
docker logout localhost:8080

# Поиск образа
docker search nginx
docker search nginx -- filter stars=3 --no-trunc busybox

# Pull (выгрузка из реестра) образа
docker pull nginx
docker pull eon01/nginx localhost:5000/myadmin/nginx

# Push (загрузка в реестр) образа
docker push eon01/nginx
docker push eon01/nginx localhost:5000/myadmin/nginx
```

#### Docker Swarm

```bash
# Установка Docker Swarm
# * в Docker версий 1.12.0+ ничего дополнительно устанавливать не требуется, т.к. Docker Swarm встроен в Docker Engine в виде специального режима (Swarm mode)
curl -ssl https://get.docker.com | bash

# Инициализация Swarm
docker swarm init --advertise-addr 192.168.10.1

# Подключение рабочего узла (worker) к Swarm
docker swarm join-token worker

# Подключение управляющего узла (manager) к Swarm
docker swarm join-token manager

# Список сервисов
docker service ls

# Список узлов
docker node ls

# Создание сервиса
docker service create --name vote -p 8080:80 instavote/vote

# Список заданий Swarm
docker service ps

# Масштабирование сервиса
docker service scale vote=3

# Обновление сервиса
docker service update --image instavote/vote:movies vote
docker service update --force --update-parallelism 1 --update-delay 30s nginx
docker service update --update-parallelism 5--update-delay 2s --image instavote/vote:indent vote
docker service update --limit-cpu 2 nginx
docker service update --replicas=5 nginx
```