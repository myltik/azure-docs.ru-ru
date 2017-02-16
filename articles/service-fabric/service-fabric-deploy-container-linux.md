---
title: "Service Fabric и развертывание контейнеров в Linux | Документация Майкрософт"
description: "Service Fabric и использование контейнеров Docker для развертывания приложений микрослужб. В этой статье представлены возможности, которые предоставляет Service Fabric для контейнеров, а также описано развертывание образа контейнера Docker в кластере."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2016
ms.author: mfussell
translationtype: Human Translation
ms.sourcegitcommit: f31c8ab3d2b4fd871c92ac0e7d04bc8d5ab86830
ms.openlocfilehash: 963ca79f83d9ae4b3c5a0e5da6c5fb7985e9ca77


---
# <a name="deploy-a-docker-container-to-service-fabric"></a>Развертывание контейнера Docker в Service Fabric
> [!div class="op_single_selector"]
> * [Развертывание контейнера Windows](service-fabric-deploy-container.md)
> * [Развертывание контейнера Docker](service-fabric-deploy-container-linux.md)
>
>

В этой статье подробно рассматривается создание контейнерных служб в контейнерах Docker в Linux.

В Service Fabric реализовано несколько способов использования контейнеров для создания приложений, состоящих из контейнерных микрослужб. Эти службы называются контейнерными.

которые включают следующие возможности:

* развертывание и активация образа контейнера;
* управление ресурсами;
* проверка подлинности репозитория;
* сопоставление порта контейнера с портом узла;
* межконтейнерное обнаружение и взаимодействие;
* возможность настройки и установки переменных среды.

## <a name="packaging-a-docker-container-with-yeoman"></a>Упаковка контейнера Docker с помощью yeoman
При упаковке контейнера в Linux у вас есть выбор — использовать шаблон yeoman или [создать пакет приложения вручную](service-fabric-deploy-container.md#manually).

Приложение Service Fabric может содержать один или несколько контейнеров, каждый из которых выполняет определенную роль в работе приложения. Пакет SDK Service Fabric для Linux включает в себя генератор [Yeoman](http://yeoman.io/), который упрощает создание приложения и добавление образа контейнера. Воспользуемся Yeoman, чтобы создать приложение с одним контейнером Docker — *SimpleContainerApp*. Позже можно будет добавить дополнительные службы, изменив созданные файлы манифестов.

## <a name="create-the-application"></a>Создание приложения
1. Введите в терминале **yo azuresfguest**.
2. В качестве платформы выберите **контейнер**.
3. Укажите имя приложения, например SimpleContainerApp.
4. Укажите URL-адрес образа контейнера из репозитория DockerHub. Параметр образа имеет вид [репозиторий]/[имя_образа].

![Генератор Service Fabric Yeoman для контейнеров][sf-yeoman]

## <a name="deploy-the-application"></a>Развертывание приложения
Созданное приложение можно развернуть в локальном кластере с помощью интерфейса командной строки Azure.

1. Подключитесь к удаленному кластеру Service Fabric.

    ```bash
    azure servicefabric cluster connect
    ```
2. С помощью скрипта установки (включен в шаблон) скопируйте пакет приложения в хранилище образов кластера, зарегистрируйте тип приложения и создайте экземпляр приложения.

    ```bash
    ./install.sh
    ```
3. Откройте в браузере Service Fabric Explorer и перейдите по адресу http://localhost:19080/Explorer (если используется Vagrant в Mac OS X, замените localhost частным IP-адресом ВМ).
4. Разверните узел приложения. Вы увидите одну запись для типа приложения и еще одну — для первого экземпляра этого типа.
5. Для удаления экземпляра приложения и отмены регистрации типа приложения используйте сценарий удаления, предоставленный в шаблоне.

    ```bash
    ./uninstall.sh
    ```

## <a name="adding-more-services-to-an-existing-application"></a>Добавление дополнительных служб в существующее приложение

Чтобы добавить службу контейнеров в приложение, созданное с использованием `yo`, выполните следующие действия: 
1. Перейдите в корневой каталог существующего приложения.  Например, `cd ~/YeomanSamples/MyApplication`, если `MyApplication` является приложением, созданным с помощью Yeoman.
2. Запустите `yo azuresfguest:AddService`



## <a name="next-steps"></a>Дальнейшие действия
* [Общие сведения о Service Fabric и контейнерах](service-fabric-containers-overview.md)
* [Использование интерфейса командной строки Azure для взаимодействия с кластером Service Fabric](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png



<!--HONumber=Dec16_HO1-->


