<properties 
	pageTitle="Начало работы с Fleet на CoreOS в Azure" 
	description="Предоставляет простые примеры использования Fleet и Docker на виртуальной машине CoreOS Linux в Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"
	editor="madhana"/>

<tags 
	ms.service="virtual-machines" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.tgt_pltfrm="vm-linux" 
	ms.workload="infrastructure-services" 
	ms.date="03/17/2015" 
	ms.author="danlep"/>


<!--The next line, with one pound sign at the beginning, is the page title-->
# Начало работы с Fleet на CoreOS в Azure

В этой статье даются два кратких примера использования [fleet](https://github.com/coreos/fleet) и [Docker](https://www.docker.com/) для запуска приложений на кластере виртуальных машин[CoreOS].

Для использования этих примеров сначала необходимо установить трехузловой кластер CoreOS в соответствии со статьей [Использование CoreOS в Azure]. Сделав это, вы поймете самые основные элементы развертывания CoreOS и получите рабочий кластер и клиентский компьютер. В этих примерах мы используем одно и то же имя кластера. Кроме того, в этих примерах предполагается, что для запуска команд **fleet** используется локальный узел Linux.




## <a id='simple'>Пример 1. Hello World с Docker</a>

Вот простое приложение «Hello World», которое запускается в одном контейнере Docker. В этом случае используется [образ busybox для Docker Hub].

На клиентском компьютере Linux используйте удобный для вас текстовый редактор для создания следующего модульного файла **systemd** и назовите его `helloworld.service`. \(Более подробную информацию о синтаксисе см. в статье [Модульные файлы].\)

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


## <a id='highavail'>Пример 2. Высокодоступный сервер Apache</a>

Одним из преимуществ использования CoreOS, Docker и **fleet** являются простота запуска высокодоступных служб. В этом примере будет развернута служба, состоящая из трех идентичных контейнеров, в которых будет запущен веб-сервер Apache. Контейнеры будут работать на трех виртуальных машинах в кластере. Этот пример подобен примеру [Запуск контейнеров с fleet] и использует [образ CoreOS Apache для Docker Hub].

>[AZURE.NOTE]Чтобы запустить высокодоступный сервер Apache, необходимо настроить конечную точку HTTP с балансировкой нагрузки на виртуальных машинах \(общедоступный порт 80, приватный порт 80\). Это можно сделать после создания кластера CoreOS с помощью портала управления Azure или команды **azure vm endpoint**. Более подробную информацию см. в разделе [Настройка набора с балансировкой нагрузки].

На клиентском компьютере используйте удобный для вас текстовый редактор для создания шаблонного модульного файла **systemd** с именем apache@.service. Этот шаблон будет использоваться для запуска трех разных экземпляров с именем apache@1.service, apache@2.service и apache@3.service:

```
[Unit]
Description=High Availability Apache
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill apache1
ExecStartPre=-/usr/bin/docker rm apache1
ExecStartPre=/usr/bin/docker pull coreos/apache
ExecStart=/usr/bin/docker run -rm --name apache1 -p 80:80 coreos/apache /usr/sbin/apache2ctl -D FOREGROUND
ExecStop=/usr/bin/docker stop apache1

[X-Fleet]
X-Conflicts=apache@*.service
```

>[AZURE.NOTE]Атрибут `X-Conflicts` сообщает CoreOS, что на конкретном узле CoreOS может выполняться только один экземпляр этого контейнера. Более подробную информацию см. в статье [Модульные файлы].

Теперь запустите экземпляры модулей в кластере CoreOS. Вы увидите, что они работают на трех разных компьютерах:

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start apache@{1,2,3}.service

unit apache@3.service launched on 00c927e4.../100.79.62.16
unit apache@1.service launched on 62f0f66e.../100.79.86.62
unit apache@2.service launched on df85f2d1.../100.78.126.15

```
Для доступа к серверу Apache, работающему на одном из модулей, отправьте простой запрос к облачной службе, где размещен кластер CoreOS.

`curl http://coreos-cluster.cloudapp.net`

Вы увидите примерно такой текст, выводимый сервером Apache по умолчанию:

```
\<htm\l>\<body\>\<h1\>It works!\</h1\>
\<p\>This is the default web page for this server.\</p\>
\<p\>The web server software is running but no content has been added, yet.\</p\>
\</body\>\</html\>
```

Можно попробовать завершить работу одной или нескольких виртуальных машин кластера для проверки того, что служба Apache продолжает работать.

По завершении остановите и выгрузите модули.

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop apache@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload apache@{1,2,3}.service

```

## Дальнейшие действия

Попробуйте выполнить больше функций с трехузловым кластером CoreOS в Azure. Узнайте, как создавать более сложные кластеры и использовать Docker, а также создавать более интересные приложения. Для этого прочитайте [учебник Тима Парка \(Tim Park\) по CoreOS], [учебник Патрика Чейнзона \(Patrick Chanezon\) по CoreOS], документацию по [Docker] и [Обзор CoreOS].

Более подробную информацию об использовании среды с открытым исходным кодом на виртуальных машинах Linux в Azure см. также в разделе [Linux и вычисления с открытым кодом в Azure].

<!--Link references-->
[Azure Cross-Platform Interface (xplat-cli)]: xplat-cli.md
[CoreOS]: https://coreos.com/
[Обзор CoreOS]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[учебник Тима Парка \(Tim Park\) по CoreOS]: https://github.com/timfpark/coreos-azure
[учебник Патрика Чейнзона \(Patrick Chanezon\) по CoreOS]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Использование CoreOS в Azure]: virtual-machines-linux-coreos-how-to.md
[Настройка набора с балансировкой нагрузки]: http://msdn.microsoft.com/library/azure/dn655055.aspx
[Запуск контейнеров с fleet]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[Модульные файлы]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[образ busybox для Docker Hub]: https://registry.hub.docker.com/_/busybox/
[образ CoreOS Apache для Docker Hub]: https://registry.hub.docker.com/u/coreos/apache/
[Linux и вычисления с открытым кодом в Azure]: virtual-machines-linux-opensource.md
<!--HONumber=54-->