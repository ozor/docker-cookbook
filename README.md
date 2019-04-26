# Docker Cookbook [Rus]


###  Кое-какие Docker команды

В некоторых примерах будет использоваться образ Ubuntu
https://hub.docker.com/_/ubuntu
```bash
docker pull ubuntu
```

Эти команды продублированы в моём гисте: https://gist.github.com/ozor/b5dad4b74cbb61379f00779cfacacbd4

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
