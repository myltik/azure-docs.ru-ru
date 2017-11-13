---
title: "Настройка среды разработки Mac OS X для работы с Azure Service Fabric | Документация Майкрософт"
description: "Установите среду выполнения, пакет SDK и инструменты и создайте локальный кластер разработки. После завершения установки вы сможете создавать приложения на базе Mac OS X."
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
ms.date: 10/31/2017
ms.author: saysa
ms.openlocfilehash: cf67c377cd5c17f7b540fb23af48a333a9cddf69
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Настройка среды разработки для Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Вы можете создать приложения Service Fabric на базе Mac OS X, работающие в кластерах Linux. Из этой статьи вы узнаете, как настроить систему Mac для разработки приложений.

## <a name="prerequisites"></a>Предварительные требования
Платформа Service Fabric изначально не предназначена для работы в OS X. Чтобы запустить локальный кластер Service Fabric, мы предоставляем предварительно настроенный образ контейнера Docker. Перед началом работы вам потребуются:

* не менее 4 ГБ ОЗУ;
* последняя версия [Docker](https://www.docker.com/);
* доступ к [образу](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/) универсального контейнера Docker для Service Fabric.

>[!TIP]
> * Чтобы установить Docker на компьютере Mac, можно выполнить инструкции, приведенные в официальной [документации](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install) Docker. 
> * По завершении проверьте правильность установки, выполнив инструкции, приведенные [здесь](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Создание локального контейнера и настройка Service Fabric
Чтобы настроить локальный контейнер Docker и запустить его в кластере Service Fabric, выполните следующие действия:

1. Извлеките образ из репозитория Docker Hub:

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Запустите экземпляр универсального контейнера Service Fabric, используя образ:

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    >Вы можете указать имя для экземпляра контейнера, которое будет более удобным для чтения. 

3. Войдите в контейнер Docker в интерактивном режиме SSH:

    ```bash
    docker exec -it sfonebox bash
    ```

4. Запустите скрипт установки, который позволит получить необходимые зависимости, а после этого запустите контейнер в кластере.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

5. После успешного завершения шага 4 вы можете перейти по адресу ``http://localhoist:19080`` на компьютере Mac и просмотреть Service Fabric Explorer.


## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Настройка интерфейса командной строки Service Fabric (sfctl) на компьютере Mac

Чтобы установить интерфейс командной строки Service Fabric (`sfctl`) на компьютере Mac, см. инструкции в [этом разделе](service-fabric-cli.md#cli-mac).
Команды интерфейса командной строки используются для взаимодействия с сущностями Service Fabric, включая кластеры, приложения и службы.

## <a name="create-application-on-your-mac-using-yeoman"></a>Создание приложения на компьютере Mac с помощью Yeoman

Service Fabric предоставляет средства формирования шаблонов, которые позволяют создать приложение Service Fabric из терминала с помощью генератора шаблонов Yeoman. Чтобы установить генератор шаблонов Yeoman для Service Fabric на компьютере, сделайте следующее:

1. Вам потребуется установить Node.js и NPM на компьютере Mac. Либо вы можете установить Node.js и NPM с помощью Homebrew, выполнив следующий шаг. Чтобы просмотреть, какие версии Node.js и NPM установлены на компьютере Mac, воспользуйтесь параметром ``-v``.

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Установите на компьютере генератор шаблонов [Yeoman](http://yeoman.io/) из NPM.

    ```bash
    npm install -g yo
    ```
3. Установите необходимый генератор Yeoman, следуя инструкциям из [документации](service-fabric-get-started-linux.md) по началу работы. Чтобы создавать приложения Service Fabric с помощью Yeoman, сделайте следующее:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Чтобы создать приложение Service Fabric Java на компьютере Mac, вам потребуется установить JDK 1.8 и Gradle на хост-компьютере. Если они еще не установлены, вы можете сделать это с помощью [Homebrew](https://brew.sh/). 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-application-on-your-mac-from-terminal"></a>Развертывание приложения на компьютере Mac из терминала

Создав приложение Service Fabric и выполнив его сборку, вы можете развернуть его с помощью [интерфейса командной строки Service Fabric](service-fabric-cli.md#cli-mac), выполнив следующие действия:

1. Подключитесь к кластеру Service Fabric, в котором выполняется экземпляр контейнера, на компьютере Mac.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Перейдите в каталог проекта и запустите скрипт установки.

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Настройка разработки .NET Core 2.0

Установите [.NET Core SDK 2.0 для Mac](https://www.microsoft.com/net/core#macos), чтобы начать [создавать приложения Service Fabric на C#](service-fabric-create-your-first-linux-application-with-csharp.md). Пакеты для приложений .NET Core 2.0 Service Fabric доступны на NuGet.org в режиме предварительной версии.

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-on-your-mac"></a>Установка подключаемого модуля Service Fabric для Eclipse Neon на компьютере Mac

Платформа Service Fabric предоставляет подключаемый модуль для **среды разработки Java Eclipse Neon**, который упрощает процесс создания, сборки и развертывания служб Java. Вы можете использовать инструкции по установке или обновлению подключаемого модуля Service Fabric Eclipse до последней версии, указанные в этой общей [документации](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon).

Все остальные инструкции из [документации по Service Fabric Eclipse](service-fabric-get-started-eclipse.md), касающиеся создания приложения, добавления службы в приложение, установки и удаления приложения, также можно использовать в этом руководстве.

Помимо выполнения указанных выше инструкций, для работы подключаемого модуля Service Fabric Eclipse с контейнером Docker на компьютере Mac следует создать экземпляр контейнера с путем, предоставленным узлу, следующим образом:
```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```
В этом примере ``/Users/sayantan/work/workspaces/mySFWorkspace`` — полный путь к рабочей области на компьютере Mac, а ``/tmp/mySFWorkspace`` — путь внутри контейнера, по которому будет выполняться сопоставление.

> [!NOTE]
>1. Если путь или имя рабочей области отличаются, обновите их соответствующим образом в приведенной выше команде ``docker run``.
>2. Если вы запускаете контейнер с именем, отличающимся от ``sfonebox``, обновите его соответствующим образом в файле ``testclient.sh`` в приложении Java субъекта Service Fabric.

## <a name="next-steps"></a>Дальнейшие действия
<!-- Links -->
* [Создание первого приложения Azure Service Fabric](service-fabric-create-your-first-linux-application-with-java.md)
* [Getting started with Eclipse Plugin for Service Fabric Java application development](service-fabric-get-started-eclipse.md) (Начало работы с подключаемым модулем Eclipse для разработки приложения Service Fabric на Java)
* [Создание кластера Service Fabric в Azure с помощью портала Azure](service-fabric-cluster-creation-via-portal.md)
* [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Моделирование приложения в Service Fabric](service-fabric-application-model.md)
* [Manage an Azure Service Fabric application by using Azure Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md) (Управление приложением Azure Service Fabric с помощью интерфейса командной строки Azure Service Fabric)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
