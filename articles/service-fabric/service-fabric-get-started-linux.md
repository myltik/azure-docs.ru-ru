<properties
   pageTitle="Настройка среды разработки в Linux | Microsoft Azure"
   description="Установите среду выполнения и пакет SDK, а затем создайте локальный кластер разработки в Linux. После этого вы сможете создавать приложения."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="seanmck"/>

# Подготовка среды разработки в Linux


> [AZURE.SELECTOR]
-[ Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

 Чтобы развертывать и запускать [приложения Azure Service Fabric](service-fabric-application-model.md) на компьютере для разработки под управлением Linux, установите среду выполнения и стандартный пакет SDK. Вы также можете установить дополнительные пакеты SDK для Java и .NET Core.

## Предварительные требования
### Поддерживаемые версии операционных систем
Для разработки поддерживаются следующие операционные системы:

- Ubuntu 16.04 (Xenial Xerus).

## Обновление списка источников APT

Прежде чем перейти к установке пакета SDK и связанного пакета среды выполнения с помощью команды apt-get, необходимо обновить список источников APT.

1. Откройте окно терминала.
2. Добавьте репозиторий Service Fabric в список источников.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Добавьте новый ключ GPG в набор ключей APT.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```

4. Обновите списки пакетов, добавив в них новые репозитории.

    ```bash
    sudo apt-get update
    ```

## Установка и настройка пакета SDK

После обновления источников пакетов можно установить пакет SDK.

1. Установите пакет SDK для Service Fabric. Вам будет предложено подтвердить установку и принять условия лицензионного соглашения.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

2. Выполните сценарий установки пакета SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## Настройка кроссплатформенного интерфейса командной строки Azure

[Кроссплатформенный интерфейс командной строки Azure][azure-xplat-cli-github] содержит команды для взаимодействия с сущностями Service Fabric, включая кластеры и приложения. Интерфейс работает на базе Node.js, поэтому [убедитесь, что у вас установлена платформа Node][install-node], прежде чем перейти к дальнейшим действиям.

1. Клонируйте репозиторий с GitHub на компьютер для разработки.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Перейдите в клонированный репозиторий и установите зависимости интерфейса командной строки с помощью диспетчера пакетов Node (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Создайте символьную ссылку на папку /usr/bin/azure в папке клонированного репозитория bin/azure, чтобы добавить ее в путь и получить доступ к командам из любого каталога.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Наконец, включите автозавершение команд Service Fabric.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## Настройка локального кластера

Если установка выполнена успешно, можно запустить локальный кластер.

1. Выполните сценарий настройки кластера.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Откройте веб-браузер и перейдите по адресу http://localhost:19080/Explorer. Если кластер запущен, вы увидите панель мониторинга Service Fabric Explorer.

    ![Обозреватель Service Fabric Explorer в Linux][sfx-linux]

На этом этапе можно развертывать готовые пакеты приложений Service Fabric или новые приложения на базе гостевых контейнеров или гостевых исполняемых файлов. Чтобы создать новые службы с помощью пакетов SDK для Java или .NET Core, выполните следующие дополнительные этапы установки.

## Установка пакета SDK для Java и подключаемого модуля Eclipse Neon (необязательно)

Пакеты SDK для Java предоставляют библиотеки и шаблоны для создания служб Service Fabric на языке Java.

1. Установите пакет SDK для Java.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Выполните сценарий установки пакета SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

Подключаемый модуль Eclipse для Service Fabric можно установить из интегрированной среды разработки Eclipse Neon.

1. Откройте Eclipse и убедитесь, что у вас установлена версия Buildship 1.0.17 или более поздняя. Вы можете проверить версии установленных компонентов, щелкнув **Help > Installation Details** (Справка > Сведения об установке). Чтобы обновить Buildship, воспользуйтесь [инструкциями][buildship-update].

2. Чтобы установить подключаемый модуль Service Fabric, щелкните **Help > Install New Software** (Справка > Установка нового программного обеспечения).

3. В поле Work with (Работа с) введите http://dl.windowsazure.com/eclipse/servicefabric.

4. Нажмите Добавить.

    ![Подключаемый модуль Eclipse][sf-eclipse-plugin]

5. Выберите подключаемый модуль Service Fabric и нажмите кнопку Next (Далее).

6. Выполните необходимые шаги установки и примите условия лицензионного соглашения.

## Установка пакета SDK для .NET Core (необязательно)

Пакет SDK для .NET Core предоставляет библиотеки и шаблоны для создания служб Service Fabric на базе кроссплатформенной среды .NET Core.

1. Установите пакет SDK для .NET Core.

    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```

2. Выполните сценарий установки пакета SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## Дальнейшие действия

- [Create your first Azure Service Fabric application](service-fabric-create-your-first-linux-application-with-java.md) (Создание первого приложения Azure Service Fabric)

- [Настройка среды разработки для Mac OS X](service-fabric-get-started-mac.md)


<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

<!---HONumber=AcomDC_0928_2016-->