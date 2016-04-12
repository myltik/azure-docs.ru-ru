<properties
   pageTitle="Docker и Compose на виртуальной машине | Microsoft Azure"
   description="Краткое введение в работу с Compose и Docker на виртуальных машинах Linux в Azure."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/02/2016"
   ms.author="danlep"/>

# Приступая к работе с решениями Docker и Compose для определения и запуска многоконтейнерного приложения на виртуальной машине Azure

В этой статье показано, как приступить к работе с решениями Docker и [Compose](http://github.com/docker/compose), чтобы определить и запустить сложное приложение на виртуальной машине Linux в Azure. Решение Compose (преемник решения *Fig*) позволяет определить приложение, состоящее из нескольких контейнеров Docker, с помощью простого текстового файла. После этого приложение разворачивается с помощью одной команды, которая инициирует все действия, необходимые для его запуска на виртуальной машине.

В этой статье показано, как быстро настроить блог WordPress с серверной базой данных SQL MariaDB, однако решение Compose можно использовать для настройки и более сложных приложений.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql).


Если вы не знакомы с решением Docker и контейнерами, см. [доску по Docker](https://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/).

## Шаг 1. Настройка виртуальной машины Linux как узла Docker

Чтобы создать виртуальную машину Linux и настроить ее как узел Docker, можно использовать различные процедуры Azure и образы, доступные в Azure Markeplace. Например, в разделе [Использование расширения виртуальных машин Docker в интерфейсе командной строки Azure](virtual-machines-linux-classic-cli-use-docker.md) описана процедура быстрого создания виртуальной машины Ubuntu с расширением виртуальных машин Docker. При использовании этого расширения виртуальная машина настраивается как узел Docker автоматически. Приведенный в этой статье пример демонстрирует применение [интерфейса командной строки Azure для Mac, Linux и Windows](../xplat-cli-install.md) (Azure CLI) в режиме управления службами для создания виртуальной машины.

## Шаг 2. Установка решения Compose

Запустите виртуальную машину Linux с решением Docker, подключите ее к клиентскому компьютеру с помощью SSH. При необходимости установите решение [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md), выполнив две указанные ниже команды.

>[AZURE.TIP] Если при создании виртуальной машины использовалось расширение Docker, то решение Compose уже установлено. Пропустите эти команды и перейдите к шагу 3. Решение Compose необходимо устанавливать только в том случае, если вы установили Docker на виртуальную машину самостоятельно.

```
$ curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

$ chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]Если появится сообщение об ошибке "Отказано в доступе", возможно, что в каталог /usr/local/bin на виртуальной машине запрещено записывать данные, поэтому решение Compose понадобится установить в режиме суперпользователя. Выполните команду `sudo -i`, а затем — две приведенные выше команды и команду `exit`.

Чтобы проверить установку решения Compose, выполните следующую команду:

```
$ docker-compose --version
```

Должен появиться результат, аналогичный `docker-compose 1.5.1`.


## Шаг 3. Создание файла конфигурации docker-compose.yml

Теперь необходимо создать файл `docker-compose.yml` (простой текстовый файл конфигурации), определяющий контейнеры Docker для запуска на виртуальной машине. В файле указывается образ для выполнения в каждом контейнере (или сборка из Dockerfile), необходимые переменные и зависимости среды, порты, ссылки между контейнерами и другие данные. Дополнительные сведения о синтаксисе YML-файла приведены в [справке по файлу docker-compose.yml](http://docs.docker.com/compose/yml/).

Создайте рабочий каталог на своей виртуальной машине и с помощью текстового редактора создайте файл `docker-compose.yml`. Приведем простой пример. Скопируйте следующий текст в файл. Эта конфигурация обеспечивает установку WordPress (системы для управления блогами и содержимым с открытым исходным кодом) и связанной серверной базы данных SQL MariaDB с использованием образов из [реестра DockerHub](https://registry.hub.docker.com/_/wordpress/).

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

## Шаг 4. Запуск контейнеров с Compose

В рабочем каталоге на виртуальной машине выполните следующую команду. (В зависимости от среды может потребоваться выполнить `docker-compose` с помощью `sudo`.)

```
$ docker-compose up -d

```

Запустятся контейнеры Docker, указанные в `docker-compose.yml`. Должен появиться результат, аналогичный приведенному ниже.

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
```

>[AZURE.NOTE] При запуске обязательно используйте параметр **-d**, чтобы контейнеры непрерывно выполнялись в фоновом режиме.

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

Теперь можно подключиться непосредственно к системе WordPress на виртуальной машине, перейдя по адресу `http://localhost:8080`. Если вы хотите подключиться к виртуальной машине по Интернету, сначала настройте конечную точку HTTP на виртуальной машине, которая сопоставляет общий порт 80 с частным портом 8080. Например, если вы создали виртуальную машину с помощью классической модели развертывания, выполните следующую команду Azure CLI.

```
$ azure vm endpoint create <machine-name> 80 8080

```

При попытке подключиться к `http://<cloudservicename>.cloudapp.net` должен появиться начальный экран WordPress, позволяющий завершить установку и приступить к работе с приложением.

![Начальный экран WordPress][wordpress_start]


## Дальнейшие действия

* Дополнительные возможности настройки Docker и Compose в виртуальной машине Docker см. в [руководстве пользователя по расширению виртуальной машины Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md).
* Дополнительные примеры сборки и развертывания многоконтейнерных приложений см. в [справочнике по интерфейсу командной строки Compose](http://docs.docker.com/compose/reference/) и [руководстве пользователя](http://docs.docker.com/compose/).
* Воспользуйтесь шаблоном диспетчера ресурсов Azure (собственным или полученным из [сообщества](https://azure.microsoft.com/documentation/templates/)), чтобы развернуть виртуальную машину Azure с Docker и настроить приложение при помощи решения Compose. Например, в шаблоне [Развертывание блога WordPress с помощью Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) решения Docker и Compose используются для быстрого развертывания WordPress с серверной базой данных MySQL на виртуальной машине Ubuntu.
* Попытайтесь интегрировать Docker Compose с кластером [Docker Swarm](virtual-machines-linux-docker-swarm.md). Сценарии см. в статье [Using Swarm with Compose](https://docs.docker.com/compose/swarm/).

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png

<!---HONumber=AcomDC_0323_2016-->