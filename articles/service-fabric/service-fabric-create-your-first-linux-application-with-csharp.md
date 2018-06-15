---
title: Создание приложения микрослужб Azure на платформе Linux с помощью C# | Документация Майкрософт
description: Создание и развертывание приложения Service Fabric с помощью C#
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: subramar
ms.openlocfilehash: ce894f775614c5be7413a004d2237f6a5cf00926
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642416"
---
# <a name="create-your-first-azure-service-fabric-application"></a>Создание первого приложения Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# для Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Использование Java в Linux (предварительная версия)](service-fabric-create-your-first-linux-application-with-java.md)
> * [Использование C# в Linux (предварительная версия)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric предоставляет пакеты SDK для создания служб в среде Linux с помощью .NET Core и Java. В этом руководстве показано, как создать приложение для Linux, а также службу C# с помощью .NET Core 2.0.

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы [настройте среду разработки Linux](service-fabric-get-started-linux.md). Если вы используете Mac OS X, вы можете [настроить универсальную среду Linux на виртуальной машине с помощью Vagrant](service-fabric-get-started-mac.md).

Также необходимо установить [интерфейс командной строки Service Fabric](service-fabric-cli.md)

### <a name="install-and-set-up-the-generators-for-c"></a>Установка и настройка генераторов для C#
Service Fabric предоставляет средства формирования шаблонов для создания приложений Service Fabric из терминала с помощью генератора шаблонов Yeoman. Сделайте следующее, чтобы настроить генераторы шаблона Yeoman Service Fabric для C#:

1. Установите Node.js и NPM на компьютере.

   Ubuntu
   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash –
   sudo apt-get install -y nodejs 
   ```

   Red Hat Enterprise Linux 7.4 (поддержка предварительной версии Service Fabric)
   ```bash
   sudo yum install nodejs
   sudo yum install npm
   ```

2. Установите на компьютере генератор шаблонов [Yeoman](http://yeoman.io/) из NPM.

  ```bash
  sudo npm install -g yo
  ```
3. Установите генератор приложений C# Service Fabric Yeoman из NPM.

  ```bash
  sudo npm install -g generator-azuresfcsharp
  ```

## <a name="create-the-application"></a>Создание приложения
Приложение Service Fabric может содержать одну или несколько служб, каждая из которых выполняет определенную роль в работе приложения. Установленный генератор [Yeoman](http://yeoman.io/) Service Fabric для C# упрощает создание первой службы и добавление последующих. Итак, с помощью Yeoman мы создадим приложение с одной службой.

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

1. Подключитесь к удаленному кластеру Service Fabric.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. С помощью скрипта установки (включен в шаблон) скопируйте пакет приложения в хранилище образов кластера, зарегистрируйте тип приложения и создайте экземпляр приложения.

    ```bash
    ./install.sh
    ```

Созданное приложение развертывается так же, как и любое другое приложение Service Fabric. Дополнительные сведения см. в документации по [управлению приложениями Service Fabric с помощью интерфейса командной строки Service Fabric](service-fabric-application-lifecycle-sfctl.md).

Параметры для этих команд можно найти в созданном манифесте в пакете приложения.

Когда приложение будет развернуто, откройте браузер и перейдите к [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) по адресу [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Затем разверните узел **Приложения**. Вы увидите одну запись для типа приложения и еще одну — для первого экземпляра этого типа.

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

## <a name="next-steps"></a>Дополнительная информация

* [Azure Service Fabric command line](service-fabric-cli.md) (Интерфейс командной строки Azure Service Fabric)
* [Сведения о вариантах поддержки Service Fabric](service-fabric-support.md)
* [Командная строка Azure Service Fabric](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
