---
title: "Создание первого приложения Azure Service Fabric | Документация Майкрософт"
description: "Создание и развертывание приложения Service Fabric с помощью Java"
services: service-fabric
documentationcenter: java
author: seanmck
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2cf98a0ef478a058c03122d3e027ef37e2404a09
ms.openlocfilehash: 8a7b100a531ea1dd5420451064fdfb1eb3f21782


---
# <a name="create-your-first-azure-service-fabric-application"></a>Создание первого приложения Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# для Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java для Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# для Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

Service Fabric предоставляет пакеты SDK для создания служб в среде Linux с помощью .NET Core и Java. В этом руководстве мы создадим приложение для Linux, а также службу с использованием Java.  В следующем видео Microsoft Virtual Academy также представлено пошаговое создание приложения Java под управлением Linux:  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-create-your-first-linux-application-with-java/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

> [!NOTE]
> Первоклассный встроенный язык программирования Java поддерживается только в предварительной версии Linux (планируется поддержка Windows). Тем не менее все приложения, включая приложения Java, можно запустить в качестве гостевых исполняемых файлов или же в контейнерах Windows или Linux. Дополнительные сведения см. в статьях [Развертывание гостевого исполняемого файла в Service Fabric](service-fabric-deploy-existing-app.md) и [Предварительная версия: развертывание контейнера в Service Fabric](service-fabric-deploy-container.md).
> 


## <a name="prerequisites"></a>Предварительные требования
Перед началом работы [настройте среду разработки Linux](service-fabric-get-started-linux.md). Если вы используете Mac OS X, вы можете [настроить универсальную среду Linux на виртуальной машине с помощью Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Создание приложения
Приложение Service Fabric может содержать одну или несколько служб, каждая из которых выполняет определенную роль в работе приложения. Пакет Service Fabric SDK для Linux включает генератор [Yeoman](http://yeoman.io/) , который упрощает создание первой службы и добавление последующих. Итак, с помощью Yeoman мы создадим приложение с одной службой.

1. В окне терминала введите ``yo azuresfjava``.
2. Присвойте имя приложению.
3. Выберите тип первой службы и присвойте ей имя. В этом примере мы выберем службу Reliable Actor.
   
   ![Генератор Service Fabric Yeoman для Java][sf-yeoman]

> [!NOTE]
> Дополнительные сведения о доступных возможностях см. в статье [Общие сведения о модели программирования Service Fabric](service-fabric-choose-framework.md).
> 

## <a name="build-the-application"></a>Создание приложения
Шаблоны Service Fabric для Yeoman включают скрипт сборки для [Gradle](https://gradle.org/), который можно использовать для создания приложения из терминала.

  ```bash
  cd myapp
  gradle
  ```

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

## <a name="start-the-test-client-and-perform-a-failover"></a>Запуск тестового клиента и отработка отказа
Проекты субъекта предполагают использование дополнительных средств. Например, для отправки сообщений им требуется другая служба или клиент. Шаблон субъекта включает простой тестовый скрипт, обеспечивающий взаимодействие со службой субъекта.

1. Запустите этот скрипт с помощью служебной программы для отслеживания, чтобы просмотреть выходные данные службы субъекта.
   
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. В Service Fabric Explorer найдите узел, в котором размещена первичная реплика службы субъекта. В нашем примере это узел 3.
   
    ![Поиск первичной реплики в Service Fabric Explorer][sfx-primary]

3. Щелкните найденный узел, а затем щелкните в меню "Действия" пункт **Отключить (перезапустить)** . Это действие перезапускает один из пяти узлов в локальном кластере и выполняет принудительную отработку отказа с переходом на одну из вторичных реплик, запущенных на другом узле. После этого обратите внимание на выходные данные тестового клиента: счетчик будет увеличиваться несмотря на отработку отказа.

## <a name="build-and-deploy-an-application-with-the-eclipse-neon-plugin"></a>Сборка и развертывание приложения с помощью подключаемого модуля Eclipse Neon

Если вы установили [подключаемый модуль Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-linux#install-the-java-sdk-and-eclipse-neon-plugin-optional) для Eclipse Neon, его можно использовать для создания, сборки и развертывания приложений Service Fabric на базе Java.  При установке Eclipse выберите **интегрированную среду разработки Eclipse для разработчиков Java**.

### <a name="create-the-application"></a>Создание приложения

Подключаемый модуль Service Fabric можно добавить в Eclipse.

1. В Eclipse выберите **File > Other > Service Fabric** (Файл > Другое > Service Fabric). Появится набор параметров, включающий субъекты и контейнеры.
   
    ![Шаблоны Service Fabric в Eclipse][sf-eclipse-templates]

2. В нашем примере используется служба без отслеживания состояния.

3. Вам будет предложено подтвердить использование перспективы Service Fabric для оптимизации Eclipse при использовании в проектах Service Fabric. Щелкните "Yes" (Да).

### <a name="deploy-the-application"></a>Развертывание приложения
Шаблоны Service Fabric включают набор задач Gradle для сборки и развертывания приложений, которые можно активировать с помощью Eclipse. 

1. Выберите **Run > Run Configurations** (Запуск > Конфигурации запуска).
2. Укажите среду: **локальная** или **облачная**. По умолчанию используется **локальная** среда. Для развертывания в удаленном кластере выберите **облачную** среду.
3. Убедитесь, что в профилях публикации указаны нужные сведения, изменив файл `local.json` или `cloud.json` соответствующим образом.
4. Щелкните **Выполнить**.

Приложение будет собрано и развернуто через несколько секунд. Его состояние можно отслеживать в Service Fabric Explorer.

## <a name="adding-more-services-to-an-existing-application"></a>Добавление дополнительных служб в существующее приложение

Чтобы добавить службу в приложение, созданное с использованием `yo`, сделайте следующее: 
1. Перейдите в корневой каталог существующего приложения.  Например, `cd ~/YeomanSamples/MyApplication`, если `MyApplication` является приложением, созданным с помощью Yeoman.
2. Запустите `yo azuresfjava:AddService`


## <a name="next-steps"></a>Дальнейшие действия
* [Общие сведения о надежных субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)
* [Использование интерфейса командной строки Azure для взаимодействия с кластером Service Fabric](service-fabric-azure-cli.md)
* [Устранение неполадок при развертывании](service-fabric-azure-cli.md#troubleshooting)
* [Сведения о вариантах поддержки Service Fabric](service-fabric-support.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png



<!--HONumber=Jan17_HO1-->


