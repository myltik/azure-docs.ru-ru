---
title: Использование расширения виртуальных машин Docker для Linux на Azure
description: Описание механизма Docker и расширений Виртуальных машин Azure, а также программного создания виртуальных машин в Azure как узлов Docker с помощью интерфейса CLI Azure.
services: virtual-machines-linux
documentationcenter: ''
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: eaff75e3-d929-4931-a4a1-8c377a8e7302
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: rasquill
ms.openlocfilehash: 91f7ea54afce0e94953d4bb01bbb1b33f167fe22
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Использование расширения виртуальных машин Docker в интерфейсе командной строки Azure (CLI Azure)
> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов. Дополнительные сведения об использовании расширения виртуальной машины Docker с моделью Resource Manager см. [здесь](../dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

В этой статье показано, как создать виртуальную машину с расширением виртуальных машин Docker в режиме управления службами (asm) в интерфейсе командной строки Azure на любой платформе. [Docker](https://www.docker.com/) — один из самых популярных подходов к виртуализации, использующий [контейнеры Linux](http://en.wikipedia.org/wiki/LXC) вместо виртуальных машин как способ изоляции данных и вычислений при использовании общих ресурсов. Можно использовать расширение виртуальной машины Docker и [агент Linux для Azure](../../extensions/agent-linux.md) для создания виртуальной машины Docker, в которой можно разместить любое количество контейнеров для приложений в Azure. Обзорное обсуждение контейнеров и их преимуществ см. на [доске по Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>Использование расширения виртуальных машин Docker в Azure
Чтобы использовать расширения виртуальных машин Docker в Azure, необходимо установить версию [интерфейса командной строки Azure](https://github.com/Azure/azure-sdk-tools-xplat) (Azure CLI) более позднюю, чем 0.8.6 (на момент написания статьи текущей версией является 0.10.0). Вы можете установить CLI Azure на компьютерах под управлением Mac, Linux и Windows.

Процесс использования Docker в Azure прост.

* Установите CLI Azure и его зависимости на компьютере, с которого вы хотите управлять Azure (в случае Windows это будет дистрибутив Linux, запущенный в виде виртуальной машины).
* Используйте команды Docker в CLI Azure, чтобы создать узел виртуальных машин Docker в Azure.
* Используйте команды локального Docker для управления своим контейнерами на узле виртуальных машин Docker в Azure.

### <a name="install-the-azure-command-line-interface-azure-cli"></a>Установка интерфейса командной строки Azure (Azure CLI)
Сведения об установке и настройке интерфейса командной строки Azure см. в статье [Установка Azure CLI](../../../cli-install-nodejs.md). Чтобы подтвердить установку, введите `azure` в командной строке, и через некоторое время отобразится рисунок ASCII интерфейса командной строки Azure, в котором перечислены основные доступные вам команды. Если установка прошла без ошибок, вы можете ввести `azure help vm` и увидеть в списке команд docker.

> [!NOTE]
> В Docker имеются инструменты для Windows, [Docker Machine](https://docs.docker.com/installation/windows/), позволяющие также автоматизировать создание клиента Docker, который можно использовать для работы с виртуальными машинами Azure в качестве узлов Docker.
> 
> 

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Подключение CLI Azure к учетной записи Azure
Чтобы вы смогли использовать CLI Azure, свяжите учетные данные учетной записи Azure с CLI Azure для вашей платформы. В разделе [Подключение к подписке Azure](/cli/azure/authenticate-azure-cli) описано, как скачать и импортировать **PUBLISHSETTINGS** -файл либо связать интерфейс командной строки Azure с идентификатором организации.

> [!NOTE]
> Есть некоторые различия в действиях при использовании первого или второго способа аутентификации, так что не забудьте прочитать вышеуказанный документ, чтобы понять различие в функциональности.
> 
> 

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Установка Docker и использование расширения виртуальных машин Docker для Azure
Следуйте [инструкции по установке Docker](https://docs.docker.com/installation/#installation) для того, чтобы установить Docker локально на вашем компьютере.

Для использования Docker на виртуальной машине Azure в образе Linux, который используется для нее, необходимо установить [агент виртуальных машин Linux для Azure](../../extensions/agent-linux.md). В настоящее время это обеспечивается только двумя типами образов:

* образ Ubuntu из коллекции образов Azure или
* пользовательский образ Linux, созданный вами, с установленным и настроенным агентом виртуальных машин Linux для Azure. Дополнительные сведения о создании собственной виртуальной машины с агентом Azure см. в [руководстве пользователя агента Linux для Azure](../../extensions/agent-linux.md).

### <a name="using-the-azure-image-gallery"></a>Использование коллекции образов Azure
В сеансе Bash или сеансе терминала используйте команду

`azure vm image list | grep Ubuntu-14_04`

в интерфейсе командной строки Azure для поиска последнего образа Ubuntu в коллекции виртуальных машин, выберите одно из имен образов, например `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`, и используйте указанную ниже команду для создания виртуальной машины с помощью этого образа.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

где:

* *&lt;vm-cloudservice name&gt;* — имя виртуальной машины, которая станет главным компьютером для контейнеров Docker в Azure;
* *&lt;имени пользователя&gt;* is the имени пользователя of the default root user of the VM
* *&lt;password&gt;* — пароль для *имени пользователя* учетной записи, который отвечает требованиям к сложности, предъявляемым для Azure.

> [!NOTE]
> В настоящий момент пароль должен включать не менее 8 символов, содержать как минимум один символ в нижнем и в верхнем регистре, цифру и один из следующих специальных символов: `!@#$%^&+=`. Точка в конце предыдущего выражения НЕ является специальным символом.
> 
> 

Если команда прошла успешно, вы увидите нечто вроде следующего, в зависимости от конкретных аргументов и параметров, используемых вами:

![](media/cli-use-docker/dockercreateresults.png)

> [!NOTE]
> Создание виртуальной машины может занять несколько минут, но после ее подготовки (значение состояния — `ReadyRole`) будет запущена управляющая программа Docker (служба Docker), и вы сможете подключиться к узлу контейнера Docker.
> 
> 

Для проверки виртуальной машины Docker, только что созданной в Azure, введите

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

где *&lt;vm-name-you-used&gt;* — имя виртуальной машины, которое было использовано при вызове `azure vm docker create`. Вы должны увидеть вывод, похожий на пример ниже, который указывает, что виртуальная машина узла Docker в Azure работает и ожидает ваших команд. 

Теперь можно попытаться подключиться с помощью клиента Docker, чтобы получить информацию (в некоторых экземплярах клиента Docker, например в ОС Mac, может потребоваться использовать `sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Чтобы убедиться, что все работает, можно проверить виртуальную машину для расширения Docker:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Проверка подлинности виртуальной машины узла Docker
Помимо создания виртуальной машины Docker, команда `azure vm docker create` также автоматически создает необходимые сертификаты, чтобы разрешить подключение клиентского компьютера Docker к узлу контейнера Azure с помощью HTTPS. Сертификаты хранятся как на клиентских компьютерах, так и на хост-компьютерах, в зависимости от обстоятельств. При последующих попытках существующие сертификаты используются повторно и совместно с новым узлом.

По умолчанию сертификаты помещаются в `~/.docker`, а Docker настраивается для запуска с использованием порта **2376**. Если вы хотите использовать другой порт или каталог, то вы можете использовать один из следующих параметров командной строки `azure vm docker create` для настройки виртуальной машины, в которой размещены контейнеры Docker, на использование другого порта или других сертификатов для подключения клиентов:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Управляющая программа Docker на узле настроена на прослушивание и проверку подлинности клиентских подключений на указанном порту с использованием сертификатов, созданных с помощью команды `azure vm docker create` . Клиентские компьютеры должны иметь эти сертификаты, чтобы получить доступ к узлу Docker.

> [!NOTE]
> Сетевой узел, работающий без этих сертификатов, будет уязвим для всех, кто может подключиться к компьютеру. Перед тем как изменить конфигурацию по умолчанию, убедитесь, что вы понимаете все риски, которым могут быть подвержены компьютеры и приложения.
> 
> 

## <a name="next-steps"></a>Дополнительная информация
* Переходите к [руководству пользователя Docker] и использованию виртуальной машины Docker. Для создания виртуальной машины с поддержкой Docker на новом портале см. раздел [Использование расширения виртуальных машин Docker на классическом портале Azure].
* Расширение виртуальной машины Docker для Azure также поддерживает Docker Compose, использующий декларативный файл YAML, позволяющий использовать смоделированное разработчиком приложение в любой среде и обеспечить согласованное развертывание. Ознакомьтесь с разделом [Приступая к работе с решениями Docker и Compose для определения и запуска многоконтейнерного приложения на виртуальной машине Azure].  

<!--Anchors-->
[Subheading 1]:#subheading-1
[Subheading 2]:#subheading-2
[Subheading 3]:#subheading-3
[Next steps]:#next-steps

[How to use the Docker VM Extension with Azure]:#How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]:#Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]:#Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]:../../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]:../../../app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md
[Использование расширения виртуальных машин Docker на классическом портале Azure]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[руководству пользователя Docker]:https://docs.docker.com/userguide/

[Приступая к работе с решениями Docker и Compose для определения и запуска многоконтейнерного приложения на виртуальной машине Azure]:../docker-compose-quickstart.md
