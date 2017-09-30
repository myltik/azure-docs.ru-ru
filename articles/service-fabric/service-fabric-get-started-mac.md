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
ms.date: 08/21/2017
ms.author: saysa
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 28424d139499b797b09664f73657a7f73361e3bc
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

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
Платформа Service Fabric изначально не предназначена для работы в OS X. Чтобы запустить локальный кластер Service Fabric, мы предоставляем предварительно настроенную виртуальную машину Ubuntu с использованием программ Vagrant и VirtualBox. Перед началом работы вам потребуются:

* [Vagrant (1.8.4 или более поздней версии)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

>[!NOTE]
> Необходимо использовать взаимно поддерживаемые версии Vagrant и VirtualBox. Vagrant может работать с ошибками в неподдерживаемой версии VirtualBox.
>

## <a name="create-the-local-vm"></a>Создание локальной виртуальной машины Linux
Чтобы создать локальную виртуальную машину, содержащую кластер Service Fabric с пятью узлами, сделайте следующее:

1. Клонируйте репозиторий `Vagrantfile`.

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
    На этом шаге отключается файл `Vagrantfile`, содержащий конфигурацию виртуальной машины и расположение, из которого загружается виртуальная машина.  Файл указывает на образ Ubuntu. 

2. Перейдите к локальному клону репозитория.

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Необязательно.) Измените параметры по умолчанию для виртуальной машины.

    По умолчанию локальные виртуальные машины имеют следующую конфигурацию:

   * 3 ГБ выделенной памяти;
   * частная сеть хоста с IP 192.168.50.50, обеспечивающая транзит трафика с узла Mac.

     Вы можете изменить любой из этих параметров или добавить к виртуальной машине другую конфигурацию в файле `Vagrantfile`. Полный список параметров см. в [документации по Vagrant](http://www.vagrantup.com/docs).
4. Создание виртуальной машины

    ```bash
    vagrant up
    ```


5. Вход на виртуальную машину и установка пакета SDK для Service Fabric

    ```bash
    vagrant ssh
    ```
   
   Установите пакет SDK, как описано [здесь](service-fabric-get-started-linux.md).  Следующий скрипт позволяет быстро установить общий пакет SDK для Service Fabric и среду выполнения Service Fabric с помощью CLI (sfctl). Для работы со скриптом необходимо прочитать и принять условия использования лицензии на устанавливаемое программное обеспечение.

    ```bash
    sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
    ```

5.  Запуск кластера Service Fabric

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    >[!TIP]
    > Если загрузка виртуальной машины занимает много времени, ее можно загрузить с помощью wget или curl или через браузер, перейдя по ссылке, указанной в файле `Vagrantfile` **config.vm.box_url**. После локальной загрузки измените файл `Vagrantfile`, чтобы он указывал локальный путь, по которому вы загрузили образ. Например, если вы загрузили образ в папку /home/users/test/azureservicefabric.tp8.box, задайте этот путь в **config.vm.box_url**.
    >

5. Проверьте правильность установки кластера. Для этого перейдите в Service Fabric Explorer по адресу http://192.168.50.50:19080/Explorer (при условии, что вы сохранили IP-адрес частной сети по умолчанию).

    ![Вид Service Fabric Explorer с узла Mac][sfx-mac]


## <a name="create-application-on-mac-using-yeoman"></a>Создание приложения на компьютере Mac с помощью Yeoman
Service Fabric предоставляет средства формирования шаблонов, которые позволяют создать приложение Service Fabric из терминала с помощью генератора шаблонов Yeoman. Чтобы установить генератор шаблонов Yeoman для Service Fabric на компьютере, сделайте следующее.

1. Вам потребуется установить Node.js и NPM на компьютере Mac. В противном случае вы можете установить Node.js и NPM с помощью Homebrew, выполнив указанные ниже действия. Чтобы просмотреть, какие версии Node.js и NPM установлены на компьютере Mac, воспользуйтесь параметром ``-v``.

  ```bash
  brew install node
  node -v
  npm -v
  ```
2. Установите на компьютере генератор шаблонов [Yeoman](http://yeoman.io/) из NPM.

  ```bash
  npm install -g yo
  ```
3. Установите необходимый генератор Yeoman, следуя инструкциям из [документации](service-fabric-get-started-linux.md) по началу работы. Чтобы создать приложения Service Fabric с помощью Yeoman, сделайте следующее:

  ```bash
  npm install -g generator-azuresfjava       # for Service Fabric Java Applications
  npm install -g generator-azuresfguest      # for Service Fabric Guest executables
  npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
  ```
4. Чтобы создать приложение Service Fabric Java на компьютере Mac, вам потребуется установить JDK 1.8 и Gradle.

## <a name="set-up-net-core-20-development"></a>Настройка разработки .NET Core 2.0

Установите [.NET Core SDK 2.0 для Mac](https://www.microsoft.com/net/core#macos), чтобы начать [создавать приложения Service Fabric на C#](service-fabric-create-your-first-linux-application-with-csharp.md). Пакеты для приложений .NET Core 2.0 Service Fabric доступны на NuGet.org в режиме предварительной версии.


## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Установка подключаемого модуля Service Fabric для Eclipse Neon

Платформа Service Fabric предоставляет подключаемый модуль для **среды разработки Java Eclipse Neon**, который упрощает процесс создания, сборки и развертывания служб Java. Вы можете использовать инструкции по установке или обновлению подключаемого модуля Eclipse Service Fabric, указанные в этой общей [документации](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon).

>[!TIP]
> По умолчанию мы поддерживаем IP-адрес по умолчанию, указанный в ``Vagrantfile`` в файле ``Local.json`` созданного приложения. Если изменить IP-адрес и развернуть Vagrant, вам также потребуется обновить соответствующий IP-адрес в файле ``Local.json`` своего приложения.

## <a name="next-steps"></a>Дальнейшие действия
<!-- Links -->
* [Создание первого приложения Azure Service Fabric](service-fabric-create-your-first-linux-application-with-java.md)
* [Getting started with Eclipse Plugin for Service Fabric Java application development](service-fabric-get-started-eclipse.md) (Начало работы с подключаемым модулем Eclipse для разработки приложения Service Fabric на Java)
* [Создание кластера Service Fabric в Azure с помощью портала Azure](service-fabric-cluster-creation-via-portal.md)
* [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Моделирование приложения в Service Fabric](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

