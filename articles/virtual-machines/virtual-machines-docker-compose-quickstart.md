<properties
   pageTitle="Docker и Compose на виртуальной машине | Microsoft Azure"
   description="Краткое введение по использованию решений Compose и Docker на виртуальных машинах Azure."
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="11/16/2015"
   ms.author="danlep"/>

# Приступая к работе с решениями Docker и Compose для определения и запуска многоконтейнерного приложения на виртуальной машине Azure

В этой статье показано, как приступить к работе с решениями Docker и [Compose](http://github.com/docker/compose) с целью определения и запуска сложного приложения на виртуальной машине Linux в Azure. Решение Compose (преемник решения *Fig*) позволяет определить приложение, состоящее из нескольких контейнеров Docker, с помощью простого текстового файла. После этого приложение разворачивается с помощью одной команды, которая инициирует все действия, необходимые для его запуска на виртуальной машине. В этой статье показано, как быстро настроить блог WordPress с серверной базой данных SQL MariaDB, однако решение Compose можно использовать для настройки и более сложных приложений.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/).


Если вы не знакомы с решением Docker и контейнерами, см. [доску по Docker](http://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/).

## Шаг 1. Настройка виртуальной машины Linux как узла Docker

Чтобы создать виртуальную машину Linux и настроить ее как узел Docker, можно использовать различные процедуры Azure и образы, доступные в Azure Markeplace. Например, в статье [Использование расширения виртуальных машин Docker в интерфейсе командной строки Azure](virtual-machines-docker-with-xplat-cli.md) описана процедура быстрого создания виртуальной машины Ubuntu с расширением виртуальных машин Docker. При использовании этого расширения виртуальная машина настраивается как узел Docker автоматически. Приведенный в этой статье пример демонстрирует, как использовать [интерфейс командной строки Azure для Mac, Linux и Windows](../xplat-cli-install.md) в режиме управления службами для создания виртуальной машины.

## Шаг 2. Установка решения Compose

Запустите виртуальную машину Linux с решением Docker, подключите ее к клиентскому компьютеру с помощью SSH. При необходимости установите решение [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md), выполнив две указанные ниже команды.

>[AZURE.TIP]Если при создании виртуальной машины использовалось расширение Docker, то решение Compose уже установлено. Пропустите эти команды и перейдите к шагу 3. Решение Compose необходимо устанавливать только в том случае, если вы установили Docker на виртуальную машину самостоятельно.

```
$ curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

$ chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]Если появится сообщение об ошибке "Отказано в доступе", возможно, что в каталог /usr/local/bin на виртуальной машине запрещено записывать данные, поэтому решение Compose понадобится установить в режиме суперпользователя. Выполните команду `sudo -i`, а затем — две приведенные выше команды и команду `exit`.

Чтобы проверить установку решения Compose, выполните следующую команду:

```
$ docker-compose --version
```

Должен появиться результат, аналогичный
```
docker-compose 1.3.2
```.


## Шаг 3. Создание файла конфигурации docker-compose.yml

Теперь необходимо создать файл `docker-compose.yml` (простой текстовый файл конфигурации), определяющий контейнеры Docker для запуска на виртуальной машине. В файле указывается образ для выполнения в каждом контейнере (или сборка из Dockerfile), необходимые переменные и зависимости среды, порты, ссылки между контейнерами и другие данные. Дополнительные сведения о синтаксисе YML-файла приведены в [справке по файлу docker-compose.yml](http://docs.docker.com/compose/yml/).

Создайте рабочий каталог на своей виртуальной машине и с помощью текстового редактора создайте файл `docker-compose.yml`. Приведем простой пример. Скопируйте следующий текст в файл. Эта конфигурация обеспечивает установку WordPress (системы управления блогами и содержимым с открытым исходным кодом) и связанной серверной базы данных MariaDB с использованием образов из [реестра DockerHub](https://registry.hub.docker.com/_/wordpress/).

 ```
 wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 8080:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>

```

## Шаг 4: Start the containers with Compose

In the working directory on your VM, simply run the following command.

```
$ docker-compose up -d

```

This starts the Docker containers specified in `docker-compose.yml`. Должен появиться результат, аналогичный:

```
Creating wordpress\_db\_1... Creating wordpress\_wordpress\_1... ```

>[AZURE.NOTE]При запуске обязательно используйте параметр **-d**, чтобы контейнеры непрерывно выполнялись в фоновом режиме.

Чтобы убедиться, что контейнеры работоспособны, введите `docker-compose ps`. Вы увидите нечто вроде этого:

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:8080->80
ess_1              apache2-for ...                       /tcp
```

Теперь можно подключиться непосредственно к системе WordPress на виртуальной машине, перейдя по адресу `http://localhost:8080`. Если вы хотите подключиться к виртуальной машине по Интернету, сначала настройте конечную точку HTTP на виртуальной машине, которая сопоставляет общий порт 80 с частным портом 8080. Например, при развертывании службы управления Azure выполните через интерфейс командной строки Azure следующую команду:

```
$ azure vm endpoint create <machine-name> 80 8080

```

Откроется начальный экран WordPress, позволяющий завершить установку и начать работу с приложением.

![Начальный экран WordPress][wordpress_start]


## Дальнейшие действия

* Дополнительные примеры сборки и развертывания многоконтейнерных приложений см. в [справке по интерфейсу командной строки Compose](http://docs.docker.com/compose/cli/) и [руководстве пользователя](http://docs.docker.com/compose/).
* Воспользуйтесь шаблоном диспетчера ресурсов Azure (собственным или полученным из [сообщества](http://azure.microsoft.com/documentation/templates/)), чтобы развернуть виртуальную машину Azure с Docker и настроить приложение при помощи решения Compose. Например: в шаблоне [Развертывание блога WordPress с помощью Docker](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/) решения Docker и Compose используются для быстрого развертывания WordPress с серверной базой данных MySQL на виртуальной машине Ubuntu.
* Попытайтесь интегрировать Docker Compose с кластером [Docker Swarm](virtual-machines-docker-swarm.md). Доступные сценарии приведены в
[статье об интеграции Docker Compose и Swarm](https://github.com/docker/compose/blob/master/SWARM.md).

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png

<!---HONumber=AcomDC_1203_2015--->