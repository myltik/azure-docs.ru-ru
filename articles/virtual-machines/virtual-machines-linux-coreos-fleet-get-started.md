<properties
	pageTitle="Приступая к работе с Fleet в CoreOS | Microsoft Azure"
	description="Предоставляет простые примеры использования fleet и Docker на кластере CoreOS виртуальных машин Linux, созданного с помощью классической модели развертывания в Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="11/16/2015"
	ms.author="danlep"/>

# Приступая к работе с кластером виртуальных машин CoreOS в Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://azure.microsoft.com/documentation/templates/coreos-with-fleet-multivm/).


В этой статье даются два кратких примера использования [fleet](https://github.com/coreos/fleet) и [Docker](https://www.docker.com/) для запуска приложений на кластере виртуальных машин[CoreOS].

Для использования этих примеров сначала необходимо установить трехузловой кластер CoreOS в соответствии со статьей [Использование CoreOS в Azure]. Сделав это, вы поймете самые основные элементы развертывания CoreOS и получите рабочий кластер и клиентский компьютер. В этих примерах мы используем одно и то же имя кластера. Кроме того, в этих примерах предполагается, что для выполнения команд **fleetctl** используется локальный узел Linux. См. раздел [Использование клиента](https://coreos.com/fleet/docs/latest/using-the-client.html), чтобы больше узнать о клиенте **fleetctl**.


## <a id='simple'>Пример 1. Hello World с Docker</a>

Вот простое приложение «Hello World», которое запускается в одном контейнере Docker. В этом случае используется [образ busybox для Docker Hub].

На клиентском компьютере Linux используйте предпочитаемый текстовый редактор для создания следующего модульного файла **systemd** и назовите его `helloworld.service`. (Более подробную информацию о синтаксисе см. в статье [Модульные файлы].)

```
[Unit]
Description=HelloWorld
After=docker.service
Requires=docker.service

[Service]

TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill busybox1
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

```

Теперь подключитесь к кластеру CoreOS и запустите модуль, выполнив следующую команду **fleetctl**. Результат показывает, что модуль запущен, а также его местоположение.


```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start helloworld.service


Unit helloworld.service launched on 62f0f66e.../100.79.86.62
```

>[AZURE.NOTE]Для дистанционного выполнения команд **fleetctl** без параметра **--tunnel** можно при желании задать значение переменной среды FLEETCTL\_TUNNEL для туннелирования запросов. Например, `export FLEETCTL_TUNNEL=coreos-cluster.cloudapp.net:22`.


Можно подключиться к контейнеру для просмотра выходных данных службы:

```
fleetctl --tunnel coreos-cloudapp.cluster.net:22 journal helloworld.service

Mar 04 21:29:26 node-1 docker[57876]: Hello World
Mar 04 21:29:27 node-1 docker[57876]: Hello World
Mar 04 21:29:28 node-1 docker[57876]: Hello World
Mar 04 21:29:29 node-1 docker[57876]: Hello World
Mar 04 21:29:30 node-1 docker[57876]: Hello World
Mar 04 21:29:31 node-1 docker[57876]: Hello World
Mar 04 21:29:32 node-1 docker[57876]: Hello World
Mar 04 21:29:33 node-1 docker[57876]: Hello World
Mar 04 21:29:34 node-1 docker[57876]: Hello World
Mar 04 21:29:35 node-1 docker[57876]: Hello World
```

Для очистки остановите и выгрузите модуль.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop helloworld.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload helloworld.service
```


## <a id='highavail'>Пример 2. Высокодоступный сервер nginx</a>

Одним из преимуществ использования CoreOS, Docker и **fleet** являются простота запуска высокодоступных служб. В этом примере будет развернута служба, состоящая из трех идентичных контейнеров, в которых будет запущен веб-сервер nginx. Контейнеры будут работать на трех виртуальных машинах в кластере. Этот пример подобен примеру [Запуск контейнеров с fleet] и использует [образ nginx для Docker Hub].

>[AZURE.IMPORTANT]Чтобы запустить высокодоступный сервер nginx, необходимо настроить конечную точку HTTP с балансировкой нагрузки на виртуальных машинах (общий порт 80, частный порт 80). Это можно сделать после создания кластера CoreOS с помощью портала Azure или команды **azure vm endpoint**. Более подробную информацию см. в разделе [Настройка набора с балансировкой нагрузки].

На клиентском компьютере используйте удобный для вас текстовый редактор для создания шаблонного модульного файла **systemd** с именем nginx@.service. Этот шаблон будет использоваться для запуска трех разных экземпляров: nginx@1.service, nginx@2.service и nginx@3.service.

```
[Unit]
Description=High Availability Nginx
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=/usr/bin/docker pull nginx
ExecStart=/usr/bin/docker run --rm --name nginx1 -p 80:80 nginx
ExecStop=/usr/bin/docker stop nginx1

[X-Fleet]
X-Conflicts=nginx@*.service
```

>[AZURE.NOTE]Атрибут `X-Conflicts` сообщает CoreOS, что на конкретном узле CoreOS может выполняться только один экземпляр этого контейнера. Более подробную информацию см. в статье [Модульные файлы].

Теперь запустите экземпляры модулей в кластере CoreOS. Вы увидите, что они работают на трех разных компьютерах:

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start nginx@{1,2,3}.service

unit nginx@3.service launched on 00c927e4.../100.79.62.16
unit nginx@1.service launched on 62f0f66e.../100.79.86.62
unit nginx@2.service launched on df85f2d1.../100.78.126.15

```
Для доступа к веб- серверу, работающему на одном из модулей, отправьте простой запрос к облачной службе, где размещен кластер CoreOS.

`curl http://coreos-cluster.cloudapp.net`

Вы увидите примерно такой текст, выводимый сервером nginx по умолчанию:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

Можно попробовать завершить работу одной или нескольких виртуальных машин кластера для проверки того, что веб-служба продолжает работать.

По завершении остановите и выгрузите модули.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop nginx@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload nginx@{1,2,3}.service

```

## Дальнейшие действия

* Попробуйте выполнить больше функций с трехузловым кластером CoreOS в Azure. Узнайте, как создавать более сложные кластеры и использовать Docker, а также создавать более интересные приложения. Для этого прочитайте [учебник Тима Парка (Tim Park) по CoreOS], [учебник Патрика Чейнзона (Patrick Chanezon) по CoreOS], документацию по [Docker] и [Обзор CoreOS].

* Чтобы приступить к работе с fleet и CoreOS в диспетчере ресурсов Azure, воспользуйтесь этим [шаблоном быстрого запуска](https://azure.microsoft.com/documentation/templates/coreos-with-fleet-multivm/).

* Дополнительную информацию об использовании среды с открытым кодом на виртуальных машинах Linux в Azure см. в статье [Linux и вычисления с открытым кодом в Azure].

<!--Link references-->
[Azure Command-Line Interface (Azure)]: ../xplat-cli-install.md
[CoreOS]: https://coreos.com/
[Обзор CoreOS]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[учебник Тима Парка (Tim Park) по CoreOS]: https://github.com/timfpark/coreos-azure
[учебник Патрика Чейнзона (Patrick Chanezon) по CoreOS]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Использование CoreOS в Azure]: virtual-machines-linux-coreos-how-to.md
[Настройка набора с балансировкой нагрузки]: ../load-balancer/load-balancer-internet-getstarted.md
[Запуск контейнеров с fleet]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[Модульные файлы]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[образ busybox для Docker Hub]: https://registry.hub.docker.com/_/busybox/
[образ nginx для Docker Hub]: https://hub.docker.com/_/nginx/
[Linux и вычисления с открытым кодом в Azure]: virtual-machines-linux-opensource.md

<!---HONumber=Nov15_HO4-->