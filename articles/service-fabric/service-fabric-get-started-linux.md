---
title: "Настройка среды разработки в Linux | Документация Майкрософт"
description: "Установите среду выполнения и пакет SDK, а затем создайте локальный кластер разработки в Linux. После этого вы сможете создавать приложения."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 5840932d79ef9fd99a94eb6ae4e587b0e616065e
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017


---
<a id="prepare-your-development-environment-on-linux" class="xliff"></a>

# Подготовка среды разработки в Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

 Чтобы развертывать и запускать [приложения Azure Service Fabric](service-fabric-application-model.md) на компьютере для разработки под управлением Linux, установите среду выполнения и стандартный пакет SDK. Вы также можете установить дополнительные пакеты SDK для Java и .NET Core.

<a id="prerequisites" class="xliff"></a>

## Предварительные требования

<a id="supported-operating-system-versions" class="xliff"></a>

### Поддерживаемые версии операционных систем
Для разработки поддерживаются следующие операционные системы:

* Ubuntu 16.04 (`Xenial Xerus`).

<a id="update-your-apt-sources" class="xliff"></a>

## Обновление списка источников APT
Прежде чем перейти к установке пакета SDK и связанного пакета среды выполнения с помощью команды apt-get, необходимо обновить список источников APT.

1. Откройте окно терминала.
2. Добавьте репозиторий Service Fabric в список источников.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Добавьте репозиторий `dotnet` в список источников.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Добавьте новый ключ GPG в набор ключей APT.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Добавьте официальный ключ GPG Docker в набор ключей APT.

    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Настройте репозиторий Docker.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Обновите списки пакетов, добавив в них новые репозитории.

    ```bash
    sudo apt-get update
    ```

<a id="install-and-set-up-the-sdk-for-containers-and-guest-executables" class="xliff"></a>

## Установка и настройка пакета SDK для контейнеров и гостевых исполняемых файлов

После обновления источников пакетов можно установить пакет SDK.

1. Установите пакет SDK для Service Fabric. Вам будет предложено подтвердить установку и принять условия лицензионного соглашения.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

    >   [!TIP]
    >   Следующие команды автоматизируют принятие лицензии для пакетов Service Fabric:
    >   ```bash
    >   echo "servicefabric servicefabric/accepted-eula-v1 select true" | debconf-set-selections
    >   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | debconf-set-selections
    >   ```
    
2. Выполните сценарий установки пакета SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

Установив общий пакет SDK, вы сможете создавать приложения с гостевыми исполняемыми файлами или службами контейнеров при помощи команды `yo azuresfguest` или `yo azuresfcontainer`. Вам может потребоваться задать переменную среды **$NODE_PATH**, в которой находятся модули узла. 


```bash
    export NODE_PATH=$NODE_PATH:$HOME/.node/lib/node_modules 
```

Если вы используете среду в качестве корневого каталога, может потребоваться задать переменную с помощью следующей команды:

```bash
    export NODE_PATH=$NODE_PATH:/root/.node/lib/node_modules 
```


> [!TIP]
> Вы можете добавить эти команды в файл ~/.bashrc, чтобы не задавать переменную среды при каждом входе.
>

<a id="setup-the-xplat-service-fabric-cli" class="xliff"></a>

## Настройка XPlat Service Fabric CLI
[XPlat CLI][azure-xplat-cli-github] поддерживает команды для взаимодействия с сущностями Service Fabric, включая кластеры и приложения. Интерфейс работает на базе Node.js, поэтому [убедитесь, что у вас установлена платформа Node][install-node], прежде чем перейти к дальнейшим действиям.

1. Клонируйте репозиторий с GitHub на компьютер для разработки.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Перейдите в клонированный репозиторий и установите зависимости интерфейса командной строки с помощью диспетчера пакетов Node (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Создайте символическую ссылку из папки `bin/azure` клонированного репозитория в `/usr/bin/azure`.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Наконец, включите автозавершение команд Service Fabric.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

<a id="setup-azure-cli-20" class="xliff"></a>

### Настройка Azure CLI 2.0

В качестве альтернативы XPlat CLI теперь доступен командный модуль Service Fabric, включенный в Azure CLI.

Дополнительные сведения об установке Azure CLI 2.0 (в т. ч. с помощью команд Service Fabric) см. в [документации по началу работы](service-fabric-azure-cli-2-0.md).

<a id="set-up-a-local-cluster" class="xliff"></a>

## Настройка локального кластера
Если установка выполнена успешно, можно запустить локальный кластер.

1. Выполните сценарий настройки кластера.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Откройте веб-браузер и перейдите по адресу http://localhost:19080/Explorer. Если кластер запущен, вы увидите панель мониторинга Service Fabric Explorer.

    ![Обозреватель Service Fabric Explorer в Linux][sfx-linux]

На этом этапе можно развертывать готовые пакеты приложений Service Fabric или новые приложения на базе гостевых контейнеров или гостевых исполняемых файлов. Чтобы создать службы с помощью пакетов SDK для Java или .NET Core, выполните этапы установки, указанные в следующих разделах.


> [!NOTE]
> Автономные кластеры не поддерживаются в системе Linux. В предварительной версии поддерживаются только универсальные кластеры, а также кластеры Azure из нескольких компьютеров под управлением Linux.
>

<a id="install-the-java-sdk-optional-if-you-wish-to-use-the-java-programming-models" class="xliff"></a>

## Установка пакета SDK для Java (необязательно, если вы хотите использовать модели программирования Java)
Пакеты SDK для Java предоставляют библиотеки и шаблоны для создания служб Service Fabric на языке Java.

1. Установите пакет SDK для Java.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Выполните сценарий установки пакета SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

<a id="install-the-eclipse-neon-plugin-optional" class="xliff"></a>

## Установка подключаемого модуля Eclipse Neon (необязательно)

Подключаемый модуль Eclipse для Service Fabric можно установить из **интегрированной среды разработки Eclipse для разработчиков Java**. Eclipse можно использовать для создания приложений с гостевыми исполняемыми файлами и контейнерами Service Fabric в дополнение к Java-приложениям Service Fabric.

> [!NOTE]
> Пакет SDK для Java является необходимым условием для использования подключаемого модуля Eclipse, даже если вы используете его только с гостевыми исполняемыми файлами и контейнерами.
>

1. Откройте Eclipse и убедитесь, что у вас установлена последняя версия Eclipse **Neon** и Buildship (версия 1.0.17 или более поздняя). Вы можете проверить версии установленных компонентов, щелкнув **Help > Installation Details** (Справка > Сведения об установке). Чтобы обновить Buildship, воспользуйтесь инструкциями [здесь][buildship-update].
2. Чтобы установить подключаемый модуль Service Fabric, щелкните **Help > Install New Software** (Справка > Установка нового программного обеспечения).
3. В текстовом поле Work with (Работать с) введите http://dl.microsoft.com/eclipse.
4. Нажмите Добавить.

    ![Подключаемый модуль Eclipse][sf-eclipse-plugin]

5. Выберите подключаемый модуль Service Fabric и нажмите кнопку **Далее**.
6. Выполните необходимые шаги установки и примите условия лицензионного соглашения.

Если подключаемый модуль Eclipse Service Fabric уже установлен, убедитесь, что вы используете последнюю версию. Это можно проверить, выбрав ``Help => Installation Details`` и выполнив поиск по запросу "Service Fabric" в списке установленных подключаемых модулей. Выберите обновление, если доступна более новая версия.

Дополнительные сведения см. в статье [Начало работы с подключаемым модулем Eclipse для разработки приложений Service Fabric на Java](service-fabric-get-started-eclipse.md).


<a id="install-the-net-core-sdk-optional-if-you-wish-to-use-the-net-core-programming-models" class="xliff"></a>

## Установка пакета SDK для .NET Core (необязательно, если вы хотите использовать модели программирования .NET Core)
Пакет SDK для .NET Core предоставляет библиотеки и шаблоны для создания служб Service Fabric с помощью .NET Core.

1. Установите пакет SDK для .NET Core.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Выполните сценарий установки пакета SDK.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

<a id="updating-the-sdk-and-runtime" class="xliff"></a>

## Обновление пакета SDK и среды выполнения

Для обновления до последней версии пакета SDK и среды выполнения выполните следующие команды (отмените выбор ненужных пакетов SDK):

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
```


> [!NOTE]
> Обновление вышеуказанных пакетов может привести к остановке локального кластера разработки. Перезапустите локальный кластер после обновления, следуя инструкциям на этой странице.

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
* [Создание первого приложения Azure Service Fabric](service-fabric-create-your-first-linux-application-with-java.md)
* [Getting started with Eclipse Plugin for Service Fabric Java application development](service-fabric-get-started-eclipse.md) (Начало работы с подключаемым модулем Eclipse для разработки приложения Service Fabric на Java)
* [Создание первого приложения Azure Service Fabric](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Настройка среды разработки для Mac OS X](service-fabric-get-started-mac.md)
* [Use the XPlat CLI to manage your Service Fabric applications](service-fabric-azure-cli.md) (Использование XPlat CLI для управления приложениями Service Fabric)
* [Различия между Service Fabric для Linux (предварительная версия) и Windows (общедоступная версия)](service-fabric-linux-windows-differences.md)

<a id="related-articles" class="xliff"></a>

## Связанные статьи

* [Начало работы с Service Fabric и Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Начало работы с Service Fabric и XPlat CLI](service-fabric-azure-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

