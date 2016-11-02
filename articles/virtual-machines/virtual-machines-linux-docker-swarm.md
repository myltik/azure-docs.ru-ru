<properties
   pageTitle="Приступая к использованию Docker с поддержкой Swarm в Azure"
   description="В этой статье описано создание группы виртуальных машин с расширением Docker для виртуальных машин и использование Swarm для создания кластера Docker."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="squillace"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="01/04/2016"
   ms.author="rasquill"/>

# Как использовать Docker со Swarm

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


В этом разделе описан простой способ использования [Docker](https://www.docker.com/) со [Swarm](https://github.com/docker/swarm) для создания в Azure кластера, управляемого Swarm. Мы создадим четыре виртуальные машины Azure, одна из которых будет выполнять роль диспетчера Swarm, а остальные три — роль части кластера узлов Docker. Выполнив все необходимые действия, вы сможете использовать Swarm для просмотра кластера, а затем — использовать в нем Docker. Кроме того, при вызовах CLI Azure в этом разделе используется режим управления службами (asm).

> [AZURE.NOTE] Для демонстрации одновременной и при этом независимой работы разных инструментов в этом разделе используется Docker со Swarm и интерфейс командной строки Azure, в котором *не* задействуется **docker-machine**. В команде **docker-machine** используются параметры **--swarm**, с помощью которых **ее** можно использовать, чтобы добавить узлы непосредственно в Swarm. С примером можно ознакомиться в документации по [docker-machine](https://github.com/docker/machine). Если у вас нет **docker-machine**, запущенной одновременно с виртуальными машинами Azure, см. статью [Использование docker-machine в Azure](virtual-machines-linux-docker-machine.md).

## Создание узлов Docker с Виртуальными машинами Azure

В этом разделе описано создание четырех виртуальных машин, но их число может быть любым. Выполните приведенный ниже код, указав вместо значения *&lt;password&gt;* выбранный пароль.

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

Выполнив этот код, вы сможете просмотреть свои виртуальные машины Azure, воспользовавшись командой **azure vm list**.

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## Установка Swarm на основной виртуальной машине Swarm

В этом разделе используется [модель установки контейнера, описанная в документации по системе Docker Swarm](https://github.com/docker/swarm#1---docker-image), но вы также можете использовать SSH-подключение к виртуальной машине **swarm-master**. В этой модели **Swarm** скачивается в качестве контейнера Docker под управлением Swarm. Далее мы выполним эту операцию *удаленно с ноутбука с помощью Docker*, чтобы подключиться к виртуальной машине **swarm-master** и выполнить команду создания идентификатора кластера **swarm create**. Идентификатор кластера определяет способ обнаружения участников группы Swarm системой **Swarm**. (Вы также можете клонировать репозиторий и самостоятельно выполнить его сборку. Так вы получите полный контроль над ним и сможете использовать функцию отладки.)

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

Идентификатор кластера указан в последней строке кода. Скопируйте его куда-нибудь, так как он вновь вам понадобится во время присоединения виртуальных машин узла к основной виртуальной машине Swarm, чтобы создать виртуальную систему Swarm. В этом примере используется идентификатор кластера **36731c17189fd8f450c395db8437befd**.

> [AZURE.NOTE] Следует уточнить, что для подключения к виртуальной машине **swarm-master** в Azure мы используем наш локальный экземпляр Docker. Также мы придерживаемся инструкций **swarm-master** по скачиванию, установке и запуску команды **create**, возвращающей идентификатор кластера, который затем можно применить для обнаружения.
<!-- -->
> Для проверки выполните команду `docker -H tcp://`*&lt;hostname&gt;* ` images`, чтобы вывести список процессов контейнера, выполняемых на виртуальной машине **swarm-master** и на другом узле, для сравнения. (Так как во время запуска предыдущей команды Swarm мы использовали параметр **--rm**, то после ее выполнения контейнер был удален. Из-за этого использование команды **docker ps -a** не даст результатов.)


        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $
<P />
> Если вы уже работали с **Docker**, то знаете, что в других узлах нет записей, так как пока еще не скачаны и не запущены нужные образы.

## Присоединение виртуальных машин узла к кластеру Docker

В каждом узле можно вывести список конечных точек с помощью интерфейса CLI Azure. Далее мы выведем такой список для узла Docker **swarm-node-1**, чтобы получить порт узла Docker.

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK


В результате использования **Docker** и параметра `-H` для указания клиента Docker на виртуальной машине узла этот узел будет присоединен к виртуальной сети Swarm, которая создана с помощью передачи идентификатора кластера и порта узла Docker (с использованием команды **--addr**):

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

Все выглядит прекрасно. Убедиться, что система **Swarm** запущена в узле **swarm-node-1**, можно с помощью следующей команды:

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

Повторите эти действия со всеми другими узлами в кластере. В нашем примере это узлы **swarm-node-2** и **swarm-node-3**.

## Начните управлять кластером Swarm,

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

и вы сможете вывести список узлов, имеющихся в кластере:

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

Перейдите к управлению в Swarm. Для вдохновения ознакомьтесь со статьей [https://github.com/docker/swarm/](https://github.com/docker/swarm/) и просмотрите [видео](https://www.youtube.com/watch?v=EC25ARhZ5bI).

<!-- links -->

[docker-machine-azure]: virtual-machines-linux-docker-machine.md
 

<!---HONumber=AcomDC_0629_2016-->