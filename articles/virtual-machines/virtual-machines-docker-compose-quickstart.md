<properties
   pageTitle="Начало работы с решениями Docker и Compose в Виртуальных машинах Azure"
   description="Краткое введение по использованию решений Compose и Docker в Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/07/2015"
   ms.author="danlep"/>

# Начало работы с решениями Docker и Compose на виртуальной машине Azure


Вы можете быстро приступить к использованию решения [Compose](http://github.com/docker/compose) (созданного на основе приложения *Fig*) для определения и запуска сложных приложений с помощью средства Docker на виртуальной машине Linux в Azure. Решение Compose позволяет определить многоконтейнерное приложение в одном файле, а затем настроить приложение с помощью одной команды, которая полностью подготавливает его для запуска на виртуальной машине.




## Создание виртуальной машины Azure с помощью расширения виртуальных машин Docker и установка решения Compose

Чтобы создать виртуальную машину Azure и установить на ней решения Docker и Compose, вы можете использовать различные процедуры Azure и образы, доступные в Azure Markeplace. Например, в [статье, в которой рассматривается использование расширения виртуальных машин Docker из интерфейса командной строки Azure](virtual-machines-docker-with-xplat-cli), показано, как быстро создать виртуальную машину Ubuntu с помощью расширения виртуальных машин Docker с использованием интерфейса командной строки Azure для компьютеров под управлением Mac, Linux и Windows (CLI Azure). В примере в этой статье CLI используется в режиме управления службами (**asm**).


После запуска виртуальной машины Ubuntu с Docker подключитесь к этому расширению по протоколу SSH, а затем установите решение [Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md), выполнив две следующие команды:

```
curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]Если появится сообщение об ошибке "Отказано в доступе", возможно, в каталог /usr/local/bin запрещено записывать данные, поэтому решение Compose понадобится установить в режиме суперпользователя. Выполните команду `sudo -i`, а затем — две приведенные выше команды и команду `exit`.

Чтобы проверить установку решения Compose, выполните следующую команду:

```
docker-compose --version
```

Должен появиться результат, аналогичный ```
docker-compose 1.2.0
```.


## Создание файла конфигурации docker-compose.yml

В решении Compose текстовый файл конфигурации `docker-compose.yml` используется для определения контейнеров, которые будут выполняться на виртуальной машине. В файле указывается образ для выполнения в каждом контейнере (или сборка из Dockerfile), необходимые переменные и зависимости среды, порты, ссылки между контейнерами и другие данные. Дополнительные сведения о синтаксисе YML-файла приведены в [справке по файлу docker-compose.yml](http://docs.docker.com/compose/yml/).

Создайте рабочий каталог на своей виртуальной машине и с помощью текстового редактора создайте файл `docker-compose.yml`. Приведем простой пример. Скопируйте следующий текст в файл. Эта конфигурация обеспечивает установку WordPress (системы для управления блогами и содержимым с открытым исходным кодом) и связанной серверной базы данных SQL MariaDB с использованием образов из [реестра DockerHub](https://registry.hub.docker.com/_/wordpress/).

 ``` wordpress: image: wordpress links: - db:mysql ports: - 8080:80

db: image: mariadb environment: MYSQL_ROOT_PASSWORD: <your password>

```

## Start the containers with Compose

In your working directory simply run

```
docker-compose up -d

```

to start the Docker containers specified in `docker-compose.yml`. You'll see output similar to:

```
Creating wordpress_db_1... Creating wordpress_wordpress_1... ```

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

Теперь можно подключиться непосредственно к системе WordPress на виртуальной машине, перейдя по адресу `http://localhost:8080`. Если вы хотите подключиться к виртуальной машине по Интернету, сначала настройте конечную точку HTTP на виртуальной машине, которая сопоставляет общий порт 80 с частным портом 8080. Например, выполните следующую команду CLI Azure:

```
azure vm endpoint create <machine-name> 80 8080

```

Должен отобразиться начальный экран WordPress, на котором можно завершить установку.

![Начальный экран WordPress][wordpress_start]




## Дальнейшие действия

* Дополнительные примеры сборки и развертывания многоконтейнерных приложений см. в [справке по командам Compose](http://docs.docker.com/compose/cli/) и [руководстве пользователя](http://docs.docker.com/compose/).
* Попытайтесь интегрировать Docker Compose с кластером [Docker Swarm](virtual-machines-docker-swarm.md). Доступные сценарии приведены в [статье об интеграции Docker Compose и Swarm](https://github.com/docker/compose/blob/master/SWARM.md).
* Воспользуйтесь шаблоном диспетчера ресурсов Azure (собственным или полученным из [сообщества](http://azure.microsoft.com/documentation/templates/)), чтобы развернуть виртуальную машину Azure с Docker и настроить приложение при помощи решения Compose. Например, [шаблон виртуальной машины Ubuntu с Docker и тремя контейнерами](http://azure.microsoft.com/documentation/templates/docker-simple-on-ubuntu/) поможет быстро развернуть три службы из образов в [реестра DockerHub](https://registry.hub.docker.com/).

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png
 

<!---HONumber=July15_HO2-->