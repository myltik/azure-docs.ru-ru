---
title: "Создание приложения микрослужб Azure на платформе Linux с помощью C# | Документация Майкрософт"
description: "Создание и развертывание приложения Service Fabric с помощью C#"
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/27/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 4baf144cc28eeff0ab8f8b60e837f8a2bad903af
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017

---
# <a name="create-your-first-azure-service-fabric-application"></a>Создание первого приложения Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# для Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java для Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# для Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric предоставляет пакеты SDK для создания служб в среде Linux с помощью .NET Core и Java. В этом руководстве показано, как создать приложение для Linux, а также службу с использованием C# (.NET Core).

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы [настройте среду разработки Linux](service-fabric-get-started-linux.md). Если вы используете Mac OS X, вы можете [настроить универсальную среду Linux на виртуальной машине с помощью Vagrant](service-fabric-get-started-mac.md).

Необходимо также настроить [Azure CLI 2.0](service-fabric-azure-cli-2-0.md) (рекомендуется) или [XPlat CLI](service-fabric-azure-cli.md) для развертывания приложения.

## <a name="create-the-application"></a>Создание приложения
Приложение Service Fabric может содержать одну или несколько служб, каждая из которых выполняет определенную роль в работе приложения. Пакет Service Fabric SDK для Linux включает генератор [Yeoman](http://yeoman.io/) , который упрощает создание первой службы и добавление последующих. Итак, с помощью Yeoman мы создадим приложение с одной службой.

1. В терминале введите следующую команду для формирования шаблонов: `yo azuresfcsharp`
2. Присвойте имя приложению.
3. Выберите тип первой службы и присвойте ей имя. В этом примере мы выберем службу Reliable Actor.

   ![Генератор Service Fabric Yeoman для C#][sf-yeoman]

> [!NOTE]
> Дополнительные сведения о доступных возможностях см. в статье [Общие сведения о модели программирования Service Fabric](service-fabric-choose-framework.md).
>
>

## <a name="build-the-application"></a>Создание приложения
Шаблоны Service Fabric для Yeoman включают скрипт сборки, с помощью которого можно создать приложение из терминала (после перемещения в папку приложения).

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>Развертывание приложения

Созданное приложение можно развернуть в локальном кластере.

### <a name="using-xplat-cli"></a>Использование кроссплатформенного интерфейса командной строки

1. Подключитесь к удаленному кластеру Service Fabric.

    ```bash
    azure servicefabric cluster connect
    ```

2. С помощью скрипта установки (включен в шаблон) скопируйте пакет приложения в хранилище образов кластера, зарегистрируйте тип приложения и создайте экземпляр приложения.

    ```bash
    ./install.sh
    ```

### <a name="using-azure-cli-20"></a>Использование Azure CLI 2.0

Созданное приложение развертывается так же, как и любое другое приложение Service Fabric. Дополнительные сведения см. в документации по [управлению приложениями Service Fabric с помощью Azure CLI](service-fabric-application-lifecycle-azure-cli-2-0.md).

Параметры для этих команд можно найти в созданном манифесте в пакете приложения.

Когда приложение будет развернуто, откройте браузер и перейдите к [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) по адресу [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Затем разверните узел **Приложения**. Вы увидите одну запись для типа приложения и еще одну — для первого экземпляра этого типа.

## <a name="start-the-test-client-and-perform-a-failover"></a>Запуск тестового клиента и отработка отказа
Проекты субъекта предполагают использование дополнительных средств. Например, для отправки сообщений им требуется другая служба или клиент. Шаблон субъекта включает простой тестовый скрипт, обеспечивающий взаимодействие со службой субъекта.

1. Запустите этот скрипт с помощью служебной программы для отслеживания, чтобы просмотреть выходные данные службы субъекта.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. В Service Fabric Explorer найдите узел, в котором размещена первичная реплика службы субъекта. В нашем примере это узел 3.

    ![Поиск первичной реплики в Service Fabric Explorer][sfx-primary]
3. Щелкните найденный узел, а затем щелкните в меню "Действия" пункт **Отключить (перезапустить)** . Будет перезапущен один узел в локальном кластере и выполнена принудительная отработка отказа с переходом на вторичную реплику, запущенную на другом узле. После этого обратите внимание на выходные данные тестового клиента: счетчик будет увеличиваться несмотря на отработку отказа.

## <a name="adding-more-services-to-an-existing-application"></a>Добавление дополнительных служб в существующее приложение

Чтобы добавить службу в приложение, созданное с использованием `yo`, сделайте следующее: 
1. Перейдите в корневой каталог существующего приложения.  Например, `cd ~/YeomanSamples/MyApplication`, если `MyApplication` является приложением, созданным с помощью Yeoman.
2. Запустите `yo azuresfcsharp:AddService`

## <a name="migrating-from-projectjson-to-csproj"></a>Миграция из project.json в CSPROJ-файл
1. Выполнив команду dotnet migrate в корневом каталоге проекта, вы конвертируете все файлы project.json в формат CSPROJ.
2. Обновите ссылки на проект в CSPROJ-файлах в соответствии с таковыми в файлах проекта.
3. Обновите имена файлов проекта в соответствии с CSPROJ-файлами в build.sh.

## <a name="next-steps"></a>Дальнейшие действия
* [Общие сведения о надежных субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)
* [Использование интерфейса командной строки Azure для взаимодействия с кластером Service Fabric](service-fabric-azure-cli.md)
* [Сведения о вариантах поддержки Service Fabric](service-fabric-support.md)

## <a name="related-articles"></a>Связанные статьи

* [Начало работы с Service Fabric и Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Начало работы с Service Fabric и XPlat CLI](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png

