<properties
   pageTitle="Docker и Compose на виртуальной машине | Microsoft Azure"
   description="Краткое введение в работу с Compose и Docker на виртуальных машинах Linux в Azure."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/10/2016"
   ms.author="danlep"/>

# Приступая к работе с решениями Docker и Compose для определения и запуска многоконтейнерного приложения на виртуальной машине Azure

В этой статье показано, как приступить к работе с решениями Docker и [Compose](http://github.com/docker/compose), чтобы определить и запустить сложное приложение на виртуальной машине Linux в Azure. Решение Compose (преемник решения *Fig*) позволяет определить приложение, состоящее из нескольких контейнеров Docker, с помощью простого текстового файла. После этого приложение разворачивается с помощью одной команды, которая инициирует все действия, необходимые для его запуска на виртуальной машине.

В этой статье показано, как быстро настроить блог WordPress с серверной базой данных SQL MariaDB на виртуальной машине Ubuntu, однако решение Compose можно использовать для настройки и более сложных приложений.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Узнайте, как [выполнить эти действия с помощью модели Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql).

Если вы не знакомы с решением Docker и контейнерами, см. [доску по Docker](https://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/).

## Шаг 1. Настройка виртуальной машины Linux как узла Docker

Чтобы создать виртуальную машину Linux и настроить ее как узел Docker, можно использовать различные процедуры Azure и образы или шаблоны Resource Manager, доступные в Azure Markeplace. Например, в разделе [Использование расширения виртуальной машины Docker для развертывания среды](virtual-machines-linux-dockerextension.md) описана процедура быстрого создания виртуальной машины Ubuntu с расширением виртуальной машины Docker с помощью [шаблона быстрого запуска](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). При использовании этого расширения Docker виртуальная машина настраивается как узел Docker и компонент Compose устанавливается автоматически. Приведенный в этой статье пример демонстрирует применение [интерфейса командной строки Azure (Azure CLI) для Mac, Linux и Windows](../xplat-cli-install.md) в режиме Resource Manager для создания виртуальной машины.

## Шаг 2. Проверка установки Compose

Запустите виртуальную машину Linux с решением Docker, подключите ее к клиентскому компьютеру с помощью SSH.

Чтобы проверить установку Compose на виртуальной машине, выполните следующую команду.

```
$ docker-compose --version
```

Должен появиться результат, аналогичный `docker-compose 1.6.2, build 4d72027`.

>[AZURE.TIP] Если использован другой способ создания узла Docker и необходимо самостоятельно установить Compose, ознакомьтесь с [документацией по Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## Шаг 3. Создание файла конфигурации docker-compose.yml

Теперь необходимо создать файл `docker-compose.yml` (простой текстовый файл конфигурации), определяющий контейнеры Docker для запуска на виртуальной машине. В файле указывается образ для выполнения в каждом контейнере (или сборка из Dockerfile), необходимые переменные и зависимости среды, порты, ссылки между контейнерами и другие данные. Дополнительные сведения о синтаксисе YML-файла приведены в [справке по файлу Compose](http://docs.docker.com/compose/yml/).

Создайте рабочий каталог на своей виртуальной машине и с помощью текстового редактора создайте файл `docker-compose.yml`. Приведем простой пример для подтверждения концепции. Скопируйте следующий текст в файл. Эта конфигурация обеспечивает установку WordPress (системы для управления блогами и содержимым с открытым исходным кодом) и связанной серверной базы данных SQL MariaDB с использованием образов из [реестра DockerHub](https://registry.hub.docker.com/_/wordpress/).

 ```
 wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

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
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Теперь можно подключиться непосредственно к WordPress на виртуальной машине через порт 80. Если для создания виртуальной машины был использован шаблон Resource Manager, попробуйте подключиться к `http://<dnsname>.<region>.cloudapp.azure.com`. Если же виртуальная машина была создана с помощью классической модели развертывания, попробуйте подключиться к `http://<cloudservicename>.cloudapp.net`. Откроется начальный экран WordPress, позволяющий завершить установку и начать работу с приложением.

![Начальный экран WordPress][wordpress_start]


## Дальнейшие действия

* Дополнительные возможности настройки Docker и Compose в виртуальной машине Docker см. в [руководстве пользователя по расширению виртуальной машины Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md). Например, один из вариантов — поместить YML-файл Compose (преобразованный в формат JSON) непосредственно в конфигурацию расширения виртуальной машины Docker.
* Дополнительные примеры сборки и развертывания многоконтейнерных приложений см. в [справочнике по командной строке Compose](http://docs.docker.com/compose/reference/) и [руководстве пользователя](http://docs.docker.com/compose/).
* Воспользуйтесь шаблоном диспетчера ресурсов Azure (собственным или полученным из [сообщества](https://azure.microsoft.com/documentation/templates/)), чтобы развернуть виртуальную машину Azure с Docker и настроить приложение при помощи решения Compose. Например, в шаблоне [Развертывание блога WordPress с помощью Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) решения Docker и Compose используются для быстрого развертывания WordPress с серверной базой данных MySQL на виртуальной машине Ubuntu.
* Попытайтесь интегрировать Docker Compose с кластером [Docker Swarm](virtual-machines-linux-docker-swarm.md). Сценарии приведены в статье [Using Swarm with Compose](https://docs.docker.com/compose/swarm/) (Использование Compose со Swarm).

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png

<!---HONumber=AcomDC_0629_2016-->