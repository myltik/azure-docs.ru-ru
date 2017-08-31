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
ms.date: 8/23/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 4f51030446d2d2a5a11018b1fce7d7e9193f3dfc
ms.contentlocale: ru-ru
ms.lasthandoff: 08/24/2017

---
# <a name="prepare-your-development-environment-on-linux"></a>Подготовка среды разработки в Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Чтобы развертывать и запускать [приложения Azure Service Fabric](service-fabric-application-model.md) на компьютере для разработки под управлением Linux, установите среду выполнения и стандартный пакет SDK. Вы также можете установить дополнительные пакеты SDK для Java и .NET Core.

## <a name="prerequisites"></a>Предварительные требования

Для разработки поддерживаются следующие операционные системы:

* Ubuntu 16.04 (`Xenial Xerus`).

## <a name="update-your-apt-sources"></a>Обновление списка источников APT
Прежде чем перейти к установке пакета SDK и связанного пакета среды выполнения с помощью средства командной строки apt-get, необходимо обновить список источников APT.

1. Откройте окно терминала.
2. Добавьте репозиторий Service Fabric в список источников.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Добавьте репозиторий `dotnet` в список источников.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Добавьте новый ключ Gnu Privacy Guard (GnuPG или GPG) в набор ключей APT.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Добавьте официальный ключ Docker GPG в набор ключей APT.

    ```bash
    sudo apt-get install curl
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

## <a name="install-and-set-up-the-sdk-for-local-cluster-setup"></a>Установка и настройка пакета SDK для настройки локального кластера

После обновления источников пакетов можно установить пакет SDK. Установите пакет SDK для Service Fabric, подтвердите установку и примите условия лицензионного соглашения.

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   Следующие команды автоматизируют принятие лицензии для пакетов Service Fabric:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-v1 select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | sudo debconf-set-selections
>   ```

## <a name="set-up-a-local-cluster"></a>Настройка локального кластера
  Если установка выполнена успешно, можно запустить локальный кластер.

  1. Выполните сценарий настройки кластера.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. Откройте веб-браузер и перейдите к [Service Fabric Explorer](http://localhost:19080/Explorer). Если кластер запущен, вы увидите панель мониторинга Service Fabric Explorer.

      ![Обозреватель Service Fabric Explorer в Linux][sfx-linux]

  На этом этапе можно развертывать готовые пакеты приложений Service Fabric или новые приложения на базе гостевых контейнеров или гостевых исполняемых файлов. Чтобы создать службы с помощью пакетов SDK для Java или .NET Core, выполните этапы установки, указанные в следующих разделах.


  > [!NOTE]
  > Автономные кластеры не поддерживаются в Linux. В предварительной версии поддерживаются только универсальные кластеры, а также кластеры Azure c несколькими компьютерами под управлением Linux.
  >

## <a name="set-up-the-service-fabric-cli"></a>Настройка интерфейса командной строки Service Fabric

[Интерфейс командной строки Service Fabric](service-fabric-cli.md) поддерживает команды для взаимодействия с сущностями Service Fabric, включая кластеры и приложения. Интерфейс основан на Python, поэтому убедитесь, что Python и PIP установлены, прежде чем выполнить следующую команду:

```bash
pip install sfctl
```

## <a name="install-and-set-up-the-generators-for-containers-and-guest-executables"></a>Установка и настройка генераторов для контейнеров и гостевых исполняемых файлов
Service Fabric предоставляет средства формирования шаблонов, которые позволяют создавать приложения Service Fabric из терминала с помощью генератора шаблонов Yeoman. Чтобы установить генератор шаблонов Yeoman для Service Fabric на компьютере, сделайте следующее.

1. Установите Node.js и NPM на компьютере.

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. Установите на компьютере генератор шаблонов [Yeoman](http://yeoman.io/) из NPM.

  ```bash
  sudo npm install -g yo
  ```
3. Установите генератор контейнеров Yeo для Service Fabric и генератор гостевых исполняемых файлов из NPM.

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

Установив эти генераторы, вы сможете создавать приложения с гостевыми исполняемыми файлами или службами контейнеров при помощи команды `yo azuresfguest` или `yo azuresfcontainer` соответственно.

## <a name="install-the-necessary-java-artifacts-optional-if-you-want-to-use-the-java-programming-models"></a>Установка необходимых артефактов Java (необязательно, если вы планируете использовать модели программирования Java)

Для создания служб Service Fabric с помощью Java установите пакет JDK 1.8, а также средство Gradle, которое используется для выполнения задач сборки. Указанный ниже фрагмент кода устанавливает Open JDK 1.8 и средство Gradle. Библиотеки Java для Service Fabric извлекаются из Maven.

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>Установка подключаемого модуля Eclipse Neon (необязательно)

Подключаемый модуль Eclipse для Service Fabric можно установить из **интегрированной среды разработки Eclipse для разработчиков Java**. Eclipse можно использовать для создания приложений с гостевыми исполняемыми файлами и контейнерами Service Fabric в дополнение к Java-приложениям Service Fabric.

1. Откройте Eclipse и убедитесь, что у вас установлена последняя версия Eclipse Neon и Buildship (версия 1.0.17 или более поздняя). Вы можете проверить версии установленных компонентов, щелкнув **Help** > **Installation Details** (Справка > Сведения об установке). Чтобы обновить Buildship, воспользуйтесь инструкциями на странице [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship. Подключаемые модули Eclipse для Gradle).

2. Чтобы установить подключаемый модуль Service Fabric, выберите **Help** > **Install New Software** (Справка > Установка нового программного обеспечения).

3. В поле **Work with** (Работать с) введите **http://dl.microsoft.com/eclipse**.

4. Щелкните **Добавить**.

    ![Страница с доступным программным обеспечением][sf-eclipse-plugin]

5. Выберите подключаемый модуль **Service Fabric** и нажмите кнопку **Next** (Далее).

6. Выполните шаги установки и примите условия лицензионного соглашения.

Если подключаемый модуль Eclipse Service Fabric уже установлен, убедитесь, что вы используете последнюю версию. Это можно проверить, выбрав **Help** > **Installation Details** (Справка > Сведения об установке) и выполнив поиск по запросу Service Fabric в списке установленных подключаемых модулей. Выберите **обновление**, если доступна более новая версия.

Дополнительные сведения см. в статье [Подключаемый модуль Service Fabric для разработки приложений Eclipse на Java](service-fabric-get-started-eclipse.md).


## <a name="install-the-net-core-sdk-optional-if-you-want-to-use-the-net-core-programming-models"></a>Установка пакета SDK для .NET Core (необязательно, если вы хотите использовать модели программирования .NET Core)
Пакет SDK для .NET Core предоставляет библиотеки и шаблоны для создания служб Service Fabric с помощью .NET Core. Установите пакет SDK для .NET Core, выполнив следующую команду.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

## <a name="update-the-sdk-and-runtime"></a>Обновление пакета SDK и среды выполнения

Для обновления до последней версии пакета SDK и среды выполнения выполните следующие команды (отмените выбор ненужных пакетов SDK):

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp
```
Чтобы обновить двоичные файлы пакета SDK для Java из Maven, необходимо изменить сведения о версии соответствующего двоичного файла в файле ``build.gradle``, чтобы они указывали на последнюю версию. Чтобы узнать, где необходимо обновить версию, просмотрите любой файл ``build.gradle`` в примерах по началу работы с Service Fabric [здесь](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> Обновление пакетов может привести к остановке локального кластера разработки. Перезапустите локальный кластер после обновления, следуя инструкциям на этой странице.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание первого Java-приложения Service Fabric в Linux](service-fabric-create-your-first-linux-application-with-java.md)
* [Подключаемый модуль Service Fabric для разработки приложений Eclipse на Java](service-fabric-get-started-eclipse.md)
* [Создание первого приложения Azure Service Fabric](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Настройка среды разработки для Mac OS X](service-fabric-get-started-mac.md)
* [Manage an Azure Service Fabric application by using Azure Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md) (Управление приложением Azure Service Fabric с помощью интерфейса командной строки Azure Service Fabric)
* [Различия между Service Fabric для Linux (предварительная версия) и Windows (общедоступная версия)](service-fabric-linux-windows-differences.md)
* [Azure Service Fabric command line](service-fabric-cli.md) (Интерфейс командной строки Azure Service Fabric)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

