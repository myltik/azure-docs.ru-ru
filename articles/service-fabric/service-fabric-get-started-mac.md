---
title: "Настройка среды разработки Mac OS X для работы с Azure Service Fabric | Документация Майкрософт"
description: "Установите среду выполнения, пакет SDK и инструменты и создайте локальный кластер разработки. По завершении установки вы сможете собирать приложения на базе Mac OS X."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: saysa
ms.openlocfilehash: 4ac26c02e1893097c858380c07f520e6570fd3db
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2018
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Настройка среды разработки для Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Вы можете собирать приложения Azure Service Fabric на базе Mac OS X, которые работают в кластерах Linux. Из этого документа вы узнаете, как настроить систему Mac для разработки приложений.

## <a name="prerequisites"></a>предварительным требованиям
Платформа Azure Service Fabric изначально не предназначена для работы в Mac OS X. Чтобы запустить локальный кластер Service Fabric, предоставляется предварительно настроенный образ контейнера Docker. Перед началом работы вам потребуются:

* Не менее 4 ГБ ОЗУ.
* Последняя версия [Docker](https://www.docker.com/).

>[!TIP]
>
>Чтобы установить Docker на компьютере Mac, выполните инструкции, приведенные в [документации Docker](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install). Затем [проверьте правильность установки](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Создание локального контейнера и настройка Service Fabric
Чтобы настроить локальный контейнер Docker и запустить его в кластере Service Fabric, выполните следующие действия:

1. Извлеките универсальный образ контейнера Service Fabric из репозитория центра Docker. По умолчанию вы получите образ с последней версией Service Fabric. Сведения о конкретной версии см. на странице [центра Docker](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

    ```bash
    docker pull microsoft/service-fabric-onebox
    ```

2. Обновите конфигурацию управляющей программы Docker в узле с помощью следующих параметров и перезапустите управляющую программу Docker: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Вы можете обновить эти параметры непосредственно в файле daemon.json, который находится в папке установки Docker.
    
    >[!NOTE]
    >
    >На другом компьютере daemon.json может находиться в другом расположении. Например, ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >
    >Рекомендуем непосредственно изменить параметры настройки управляющей программы Docker. Нажмите **значок Docker**, а затем последовательно выберите **Параметры** > **Управляющая программа** > **Дополнительно**.
    >

3. Запустите универсальный экземпляр контейнера Service Fabric и используйте образ, извлеченный на первом шаге:

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox microsoft/service-fabric-onebox
    ```
    >[!TIP]
    >Укажите имя экземпляра контейнера, которое будет более удобным для чтения. 
    >
    >Если приложение ожидает передачи данных через определенные порты, их необходимо указать с помощью дополнительных тегов `-p`. Например, если приложение ожидает передачи данных через порт 8080, добавьте следующий тег `-p`:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

4. Войдите в контейнер Docker в интерактивном режиме SSH:

    ```bash
    docker exec -it sfonebox bash
    ```

5. Запустите скрипт установки, чтобы получить необходимые зависимости, а затем запустите контейнер в кластере.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. По завершении шага 5 перейдите по адресу `http://localhost:19080` с компьютера Mac. Откроется обозреватель Service Fabric.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Настройка интерфейса командной строки Service Fabric (sfctl) на компьютере Mac

Чтобы установить интерфейс командной строки Service Fabric (`sfctl`) на компьютере Mac, см. инструкции в [этом разделе](service-fabric-cli.md#cli-mac).
Команды интерфейса командной строки поддерживают взаимодействие с сущностями Service Fabric, включая кластеры, приложения и службы.

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Создание приложения на компьютере Mac с помощью Yeoman

Service Fabric предоставляет средства формирования шаблонов, которые позволяют создать приложение Service Fabric из терминала с помощью генератора шаблонов Yeoman. Чтобы установить генератор шаблонов Yeoman для Service Fabric на компьютере, выполните следующие действия:

1. Установите Node.js и диспетчер пакетов Node (NPM) на компьютере Mac. Это можно сделать с помощью [HomeBrew](https://brew.sh/), как показано ниже:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Установите на компьютере генератор шаблонов [Yeoman](http://yeoman.io/) из NPM.

    ```bash
    npm install -g yo
    ```
3. Установите генератор Yeoman, следуя инструкциям из [документации](service-fabric-get-started-linux.md) по началу работы. Чтобы создавать приложения Service Fabric с помощью Yeoman, сделайте следующее:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Чтобы создать приложение Service Fabric Java на компьютере Mac, установите JDK 1.8 и Gradle на хост-компьютере. Это можно сделать с помощью [HomeBrew](https://brew.sh/), как показано ниже: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Развертывание приложения на компьютере Mac из терминала

Когда вы создадите и соберете приложение Service Fabric, можно развернуть его с помощью [интерфейса командной строки Service Fabric](service-fabric-cli.md#cli-mac).

1. Подключитесь к кластеру Service Fabric, в котором выполняется экземпляр контейнера, на компьютере Mac.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Перейдите к каталогу проекта и запустите скрипт установки.

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Настройка разработки .NET Core 2.0

Установите [.NET Core SDK 2.0 для Mac](https://www.microsoft.com/net/core#macos), чтобы начать [создавать приложения Service Fabric на C#](service-fabric-create-your-first-linux-application-with-csharp.md). Сейчас пакеты для приложений .NET Core 2.0 Service Fabric доступны на NuGet.org в режиме предварительной версии.

## <a name="install-the-service-fabric-plug-in-for-eclipse-neon-on-your-mac"></a>Установка подключаемого модуля Service Fabric для Eclipse Neon на компьютере Mac

Azure Service Fabric предоставляет подключаемый модуль для Eclipse Neon для интегрированной среды разработки Java. Он упрощает процесс создания и развертывания служб Java. Чтобы установить или обновить подключаемый модуль Service Fabric для Eclipse до последней версии, выполните [эти действия](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon). Другие шаги в [документации Service Fabric для Eclipse](service-fabric-get-started-eclipse.md) также применимы: сборка приложения, добавление службы в приложение, удаление приложения и т. д.

На последнем шаге создается экземпляр контейнера с тем же путем, что и для узла. Для подключаемого модуля требуется, чтобы экземпляр этого типа работал в контейнере Docker на компьютере Mac. Например: 

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox microsoft/service-fabric-onebox
```

Атрибуты определяются следующим образом:
* `/Users/sayantan/work/workspaces/mySFWorkspace` — полный путь к рабочей области на компьютере Mac.
* `/tmp/mySFWorkspace` — это путь внутри контейнера. С ним следует сопоставить рабочую область.

>[!NOTE]
> 
>Если вы используете другие путь и имя рабочей области, обновите эти значения в команде `docker run`.
> 
>Если вы запускаете контейнер с именем, отличающимся от `sfonebox`, обновите его в файле testclient.sh в приложении Java субъекта Service Fabric.
>

## <a name="next-steps"></a>Дополнительная информация
<!-- Links -->
* [Создание первого приложения Azure Service Fabric](service-fabric-create-your-first-linux-application-with-java.md)
* [Создание и развертывание первого приложения Service Fabric Java в Linux с использованием подключаемого модуля Service Fabric для Eclipse](service-fabric-get-started-eclipse.md).
* [Создание кластера Service Fabric в Azure с помощью портала Azure](service-fabric-cluster-creation-via-portal.md)
* [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).
* [Моделирование приложения в Service Fabric](service-fabric-application-model.md)
* [Manage an Azure Service Fabric application by using Azure Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md) (Управление приложением Azure Service Fabric с помощью интерфейса командной строки Azure Service Fabric)
* [Подготовка среды разработки Linux в Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
