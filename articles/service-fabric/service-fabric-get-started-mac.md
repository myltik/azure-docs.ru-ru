---
title: "Настройка среды разработки для Mac OS X | Документация Майкрософт"
description: "Установите среду выполнения, пакет SDK и инструменты и создайте локальный кластер разработки. После завершения установки вы сможете создавать приложения на базе Mac OS X."
services: service-fabric
documentationcenter: java
author: saysa
manager: raunakp
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: e5d14eb0a656d67030f4c0d3d510aec0e9cafae7
ms.lasthandoff: 03/29/2017


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
    На этом шаге отключается файл `Vagrantfile`, содержащий конфигурацию виртуальной машины и расположение, из которого загружается виртуальная машина.

   
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

   На этом этапе требуется скачать предварительно настроенный образ виртуальной машины, загрузить его на локальном устройстве, а затем установить в нем локальный кластер Service Fabric. Это займет несколько минут. Если установка выполнена успешно, в окне вывода появится сообщение о начале запуска кластера.

    ![Начало установки кластера после подготовки виртуальной машины][cluster-setup-script]

>[!TIP]
> Если загрузка виртуальной машины занимает много времени, ее можно загрузить с помощью wget или curl или через браузер, перейдя по ссылке, указанной в файле `Vagrantfile` **config.vm.box_url**. После локальной загрузки измените файл `Vagrantfile`, чтобы он указывал локальный путь, по которому вы загрузили образ. Например, если вы загрузили образ в папку /home/users/test/azureservicefabric.tp8.box, задайте этот путь в **config.vm.box_url**.
>

5. Проверьте правильность установки кластера. Для этого перейдите в Service Fabric Explorer по адресу http://192.168.50.50:19080/Explorer (при условии, что вы сохранили IP-адрес частной сети по умолчанию).

    ![Вид Service Fabric Explorer с узла Mac][sfx-mac]

## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Установка подключаемого модуля Service Fabric для Eclipse Neon

Платформа Service Fabric предоставляет подключаемый модуль для **среды разработки Java Eclipse Neon**, который упрощает процесс создания, сборки и развертывания служб Java. Вы можете использовать инструкции по установке или обновлению подключаемого модуля Eclipse Service Fabric, указанные в этой общей [документации](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon).

## <a name="using-service-fabric-eclipse-plugin-on-mac"></a>Использование подключаемого модуля Eclipse Service Fabric в Mac

Убедитесь, что вы выполнили инструкции, описанные в [документации по подключаемому модулю Eclipse Service Fabric](service-fabric-get-started-eclipse.md). Инструкции по созданию, сборке и развертыванию приложения Service Fabric на Java, использующего гостевой контейнер Vagrant на узле Mac, практически такие же, как в общей документации, за исключением некоторых моментов.

* Так как для приложения Service Fabric на Java потребуются библиотеки Service Fabric, проект Eclipse необходимо создать в общем пути. По умолчанию содержимое пути на узле, где содержится ``Vagrantfile``, используется совместно с путем ``/vagrant`` на гостевом компьютере.
* При наличии ``Vagrantfile`` в пути, например ``~/home/john/allprojects/``, необходимо создать проект ``MyActor`` Service Fabric в расположении ``~/home/john/allprojects/MyActor``. В таком случае путь к рабочей области Eclipse будет иметь следующий вид: ``~/home/john/allprojects``.

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

