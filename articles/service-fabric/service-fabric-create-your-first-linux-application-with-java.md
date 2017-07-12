---
title: "Создание приложения микрослужб Azure на платформе Linux с помощью Java | Документация Майкрософт"
description: "Создание и развертывание приложения Service Fabric с помощью Java"
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/02/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: e229602b4bfa72977c9b15e854d796ed09fa55d2
ms.contentlocale: ru-ru
ms.lasthandoff: 07/01/2017


---
<a id="create-your-first-service-fabric-java-application-on-linux" class="xliff"></a>

# Создание первого Java-приложения Service Fabric в Linux
> [!div class="op_single_selector"]
> * [C# для Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java для Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# для Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Используя это краткое руководство, вы создадите первое Java-приложение Azure Service Fabric в среде разработки Linux всего за несколько минут.  В результате вы получите простое приложение Java с одной службой, работающее в кластере локальной разработки.  

<a id="prerequisites" class="xliff"></a>

## Предварительные требования
Перед началом работы установите пакет SDK для Service Fabric, Azure CLI и настройте кластер разработки в своей [среде разработки Linux](service-fabric-get-started-linux.md). Если вы используете Mac OS X, можно [настроить среду разработки Linux на виртуальной машине с помощью Vagrant](service-fabric-get-started-mac.md).

Необходимо также настроить [Azure CLI 2.0](service-fabric-azure-cli-2-0.md) (рекомендуется) или [XPlat CLI](service-fabric-azure-cli.md) для развертывания приложения.

<a id="create-the-application" class="xliff"></a>

## Создание приложения
Приложение Service Fabric содержит одну или несколько служб, каждая из которых выполняет определенную роль в работе приложения. Пакет Service Fabric SDK для Linux включает генератор [Yeoman](http://yeoman.io/) , который упрощает создание первой службы и добавление последующих.  Кроме того, можно создать, собрать и развернуть Java-приложения Service Fabric с помощью подключаемого модуля для Eclipse. См. статью [Подключаемый модуль Service Fabric для разработки приложений Eclipse на Java](service-fabric-get-started-eclipse.md). В этом руководстве используйте Yeoman для создания приложения с одной службой, которая сохраняет и получает значение счетчика.

1. В окне терминала введите ``yo azuresfjava``.
2. Присвойте имя приложению. 
3. Выберите тип первой службы и присвойте ей имя. Для этого примера выберите службу Reliable Actor. Дополнительные сведения о других типах служб см. в статье [Общие сведения о модели программирования Service Fabric](service-fabric-choose-framework.md).
   ![Генератор Service Fabric Yeoman для Java][sf-yeoman]

<a id="build-the-application" class="xliff"></a>

## Создание приложения
Шаблоны Service Fabric для Yeoman включают скрипт сборки для [Gradle](https://gradle.org/), который можно использовать для создания приложения из терминала. Используйте следующую команду, чтобы собрать и упаковать приложение:

  ```bash
  cd myapp
  gradle
  ```

<a id="deploy-the-application" class="xliff"></a>

## Развертывание приложения
Созданное приложение можно развернуть в локальном кластере.

<a id="using-xplat-cli" class="xliff"></a>

### Использование кроссплатформенного интерфейса командной строки

1. Подключитесь к удаленному кластеру Service Fabric.

    ```bash
    azure servicefabric cluster connect
    ```

2. С помощью скрипта установки (включен в шаблон) скопируйте пакет приложения в хранилище образов кластера, зарегистрируйте тип приложения и создайте экземпляр приложения.

    ```bash
    ./install.sh
    ```

<a id="using-azure-cli-20" class="xliff"></a>

### Использование Azure CLI 2.0

Созданное приложение развертывается так же, как и любое другое приложение Service Fabric. Дополнительные сведения см. в документации по [управлению приложениями Service Fabric с помощью Azure CLI](service-fabric-application-lifecycle-azure-cli-2-0.md).

Параметры для этих команд можно найти в созданном манифесте в пакете приложения.

Когда приложение будет развернуто, откройте браузер и перейдите к [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) по адресу [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Затем разверните узел **Приложения**. Вы увидите одну запись для типа приложения и еще одну — для первого экземпляра этого типа.

<a id="start-the-test-client-and-perform-a-failover" class="xliff"></a>

## Запуск тестового клиента и отработка отказа
Проекты субъекта предполагают использование дополнительных средств. Например, для отправки сообщений им требуется другая служба или клиент. Шаблон субъекта включает простой тестовый скрипт, обеспечивающий взаимодействие со службой субъекта.

1. Запустите этот скрипт с помощью служебной программы для отслеживания, чтобы просмотреть выходные данные службы субъекта.  Сценарий теста вызывает для субъекта метод `setCountAsync()`, увеличивающий значение счетчика. Затем для субъекта вызывается метод `getCountAsync()`, чтобы получить новое значение счетчика и вывести его на консоль.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. В Service Fabric Explorer найдите узел, в котором размещена первичная реплика службы субъекта. В нашем примере это узел 3. Первичная реплика службы обрабатывает операции чтения и записи.  Затем изменения в состоянии службы реплицируются во вторичные реплики, выполняющиеся на узлах 0 и 1, как показано на снимке экрана ниже.

    ![Поиск первичной реплики в Service Fabric Explorer][sfx-primary]

3. В разделе **Узлы** щелкните найденный узел, а затем выберите в меню "Действия" пункт **Отключить (перезапустить)**. После этого узел, на котором запущена первичная реплика службы, перезапускается и выполняется принудительная отработка отказа с переходом на одну из вторичных реплик, запущенных на другом узле.  Вторичная реплика повышается до первичной, на другом узле создается еще одна вторичная реплика, и первичная реплика начинает выполнять операции чтения и записи. После перезапуска узла обратите внимание на выходные данные тестового клиента: счетчик будет увеличиваться несмотря на отработку отказа.

<a id="add-another-service-to-the-application" class="xliff"></a>

## Добавление в приложение еще одной службы
Чтобы добавить службу в существующее приложение при помощи `yo`, выполните инструкции ниже.
1. Перейдите в корневой каталог существующего приложения.  Например, `cd ~/YeomanSamples/MyApplication`, если `MyApplication` является приложением, созданным с помощью Yeoman.
2. Запустите `yo azuresfjava:AddService`
3. Создайте и разверните приложение, как в предыдущих шагах.

<a id="remove-the-application" class="xliff"></a>

## Удаление приложения
С помощью скрипта для удаления экземпляра приложения (включен в шаблон) отмените регистрацию пакета приложения и удалите его из хранилища образов кластера.

```bash
./uninstall.sh
```

В обозревателе Service Fabric вы увидите, что приложение и его тип больше не отображаются в узле **Приложения**.

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
* [Подключаемый модуль Service Fabric для разработки приложений Eclipse на Java](service-fabric-get-started-eclipse.md)
* [Общие сведения о надежных субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)
* [Использование интерфейса командной строки Azure для взаимодействия с кластером Service Fabric](service-fabric-azure-cli.md)
* [Устранение неполадок при развертывании](service-fabric-azure-cli.md#troubleshooting)
* [Сведения о вариантах поддержки Service Fabric](service-fabric-support.md)

<a id="related-articles" class="xliff"></a>

## Связанные статьи

* [Начало работы с Service Fabric и Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Начало работы с Service Fabric и XPlat CLI](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

