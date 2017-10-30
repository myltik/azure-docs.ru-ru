---
title: "Географическое аварийное восстановление в служебной шине Azure | Документация Майкрософт"
description: "Способы использования географических регионов для отработки отказа и аварийного восстановления в служебной шине Azure"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: sethm
ms.openlocfilehash: 2c509b56282ace92e536dc85f1a28f83a4701940
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/12/2017
---
# <a name="azure-service-bus-geo-disaster-recovery-preview"></a>Географическое аварийное восстановление в служебной шине Azure (предварительная версия)

При простое региональных центров обработки данных крайне важно, чтобы обработка данных продолжала работать в другом регионе или центре обработки данных. Таким образом *географическое аварийное восстановление* и *георепликация* являются важными функциями для любого предприятия. Служебная шина Azure поддерживает географическое аварийное восстановления и георепликацию на уровне пространства имен. 

Предварительная версия географического аварийного восстановления в данный момент доступна только в двух регионах (**северо-центральный регион США** и **юго-центральный регион США**).

## <a name="outages-and-disasters"></a>Сбои и аварийные ситуации

В статье [Рекомендации по изолированию приложений от простоев и аварий служебной шины](service-bus-outages-disasters.md) описаны различия между "сбоями" и "авариями", которые необходимо отметить. *Сбой* — это временная недоступность служебной шины Azure. Он может повлиять на некоторые компоненты службы, такие как хранилище сообщений, или даже весь центр обработки данных. Однако после устранения проблемы служебная шина снова становится доступной. Как правило, простой не приводит к утрате сообщений или других данных. Примером такого сбоя может быть сбой питания в центре обработки данных.

*Авария* определяется как полная потеря [единицы масштабирования](service-bus-architecture.md#service-bus-scale-units) или центра обработки данных служебной шины. Центр обработки данных может снова стать доступным, но может и не стать или быть отключенным в течение нескольких часов или дней. Авария обычно вызывает потерю некоторых или всех сообщений или других данных. Примеры аварий — пожар, наводнение или землетрясение.

Функция географического аварийного восстановления служебной шины Azure — это решение для аварийного восстановления. Основные понятия и рабочий процесс, описанные в этой статье, относятся к сценариям восстановления после аварий, но не временных сбоев.  

## <a name="basic-concepts-and-terms"></a>Основные понятия и термины

Функция аварийного восстановления реализует аварийное восстановление метаданных и основывается на первичном и вторичном пространстве имен для аварийного восстановления. Обратите внимание, что функция географического аварийного восстановления доступна только для [пространства имен уровня Premium](service-bus-premium-messaging.md). Вам не нужно изменять какие-либо строки подключения, так как соединение выполняется через псевдоним.

В этом руководстве используются термины, представленные ниже.

-  *Псевдоним*: основная строка подключения.

-  *Первичное или вторичное пространство имен*: описание пространств имен, соответствующих псевдониму. Первичное (активное) принимает сообщения, вторичное (пассивное) не получает сообщений. Метаданные между ними синхронизированы, поэтому они могут беспрепятственно принимать сообщения без каких-либо изменений кода приложения.

-  *Метаданные*: представление объектов в служебной шине Azure. В настоящее время поддерживаются только метаданные.

-  *Отработка отказа*: процесс активации дополнительного пространства имен. Необходимо извлечь сообщения из предыдущего первичного пространства имен, как только оно снова станет доступно, а затем удалить пространство имен. Чтобы выполнить другую отработку отказа, добавьте новое вторичное пространство имен в связывание.

## <a name="failover-workflow"></a>Рабочий процесс отработки отказа

Следующий раздел представляет собой обзор всего процесса настройки начальной отработки отказа и последующих действий.

![1][]

Сначала настройте первичное и вторичное пространство имен, а затем создайте связывание. Это связывание предоставит псевдоним, который можно использовать для подключения. Так как используется псевдоним, нет необходимости изменять строки подключения. Только новые пространства имен могут быть добавлены к вашему связыванию. Наконец, вы должны добавить логику триггера (например, бизнес-логику, которая обнаруживает, что пространство имен недоступно, и инициирует отработку отказа). Доступность пространства имен можно проверить с помощью возможности [обзора сообщений](message-browsing.md) служебной шины.

После настройки мониторинга и аварийного восстановления можно взглянуть на процесс отработки отказа. Если триггер инициирует отработку отказа или вы запускаете отработку отказа вручную, необходимы два шага:

1. В случае другого сбоя необходимо иметь возможность повторной отработки отказа. Поэтому настройте второе пассивное пространство имен и обновите связывание. 
2. Извлеките сообщения из прежнего первичного пространства имен, как только новое пространство имен станет доступным. После этого повторно используйте или удалите старое основное пространство имен.

![2][]

## <a name="set-up-disaster-recovery"></a>Настройка аварийного восстановления

В этом разделе описывается создание собственного кода географического аварийного восстановления в служебной шине. Для этого требуются два пространства имен в независимых расположениях, например, юг США и северо-центральный регион США. В следующем примере используется Visual Studio 2017.

1.  Создайте проект **консольного приложения (.Net Framework)** в Visual Studio и присвойте ему имя, например, **SBGeoDR**.

2.  Установите следующие пакеты NuGet:
    1.  Microsoft.IdentityModel.Clients.ActiveDirectory;
    2.  Microsoft.Azure.Management.ServiceBus.

3. Убедитесь, что установлен пакет Newtonsoft.Json NuGet версии 10.0.3.

3.  Добавьте следующие инструкции `using` в код.

    ```csharp
    using System.Threading;
    using Microsoft.Azure.Management.ServiceBus;
    using Microsoft.Azure.Management.ServiceBus.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

4. Измените метод `main()`, чтобы добавить два пространства имен уровня Рremium:

    ```csharp
    // 1. Create primary namespace (optional).

    var namespaceParams = new SBNamespace()
    {
        Location = "South Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    var namespace1 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, namespaceParams);

    // 2. Create secondary namespace (optional if you already have an empty namespace available).

    var namespaceParams2 = new SBNamespace()
    {
        Location = "North Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    // If you re-run this program while namespaces are still paired this operation will fail with a bad request.
    // This is because we block all updates on secondary namespaces once it is paired.

    var namespace2 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRSecondaryNS, namespaceParams2);
    ```

5. Включите связывание между двумя пространствами имен и получите псевдоним, который вы позже используете для подключения к своим сущностям:

    ```csharp
    // 3. Pair the namespaces to enable DR.

    ArmDisasterRecovery drStatus = client.DisasterRecoveryConfigs.CreateOrUpdate(
        resourceGroupName,
        geoDRPrimaryNS,
        alias,
        new ArmDisasterRecovery { PartnerNamespace = geoDRSecondaryNS });

    // A similar loop can be used to check if other operations (Failover, BreakPairing, Delete) 
    // mentioned below have been successful.
    while (drStatus.ProvisioningState != ProvisioningStateDR.Succeeded)
    {
        Console.WriteLine("Waiting for DR to be set up. Current state: " +
        drStatus.ProvisioningState);
        drStatus = client.DisasterRecoveryConfigs.Get(
        resourceGroupName,
        geoDRPrimaryNS,
        alias);

        Thread.CurrentThread.Join(TimeSpan.FromSeconds(30));
    }
    ```

Вы успешно установили два связанных пространства имен. Теперь можно создать сущности для наблюдения за синхронизацией метаданных. Если вы хотите выполнить отработку отказа сразу же после этого, подождите пока синхронизируются метаданные. Вы можете добавить небольшой период ожидания, например:

```csharp
client.Topics.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", new SBTopic());
client.Subscriptions.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", "myTopic-Sub1", new SBSubscription());

// sleeping to allow metadata to sync across primary and secondary
Thread.Sleep(1000 * 60);
```

На этом этапе вы можете добавлять сущности на портале или с помощью Azure Resource Manager и посмотреть, как они синхронизируются. Если вы не планируете выполнять отработку отказа вручную, вы должны создать приложение для управления основным пространством имен и инициирования отработки отказа, если оно станет недоступным. 

## <a name="initiate-a-failover"></a>Запуск отработки отказа

Ниже показано, как инициировать отработку отказа:

```csharp
// Note that this failover operation is always run against the secondary namespace 
// (because primary might be down at time of failover).

client.DisasterRecoveryConfigs.FailOver(resourceGroupName, geoDRSecondaryNS, alias);
```

После запуска отработки отказа добавьте новое пассивное пространство имен и восстановите связывание. В предыдущем разделе показан код для создания связывания. Кроме того, необходимо удалить сообщения из старого основного пространства имен после завершения отработки отказа. Примеры для получения сообщений из очереди см. в статье [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md).

## <a name="how-to-disable-geo-disaster-recovery"></a>Отключение географического аварийного восстановления

Ниже показано, как отключить связывание пространств имен:

```csharp
client.DisasterRecoveryConfigs.BreakPairing(resourceGroupName, geoDRPrimaryNS, alias);
```

Следующий код удаляет созданный псевдоним:

```csharp
// Delete the DR config (alias).
// Note that this operation must run against the namespace to which the alias is currently pointing.
// If you break the pairing and want to delete the namespaces afterwards, you must delete the alias first.

client.DisasterRecoveryConfigs.Delete(resourceGroupName, geoDRPrimaryNS, alias);
```

## <a name="steps-after-a-failover-failback"></a>Действия после отработки отказа (восстановление размещения)

После отработки отказа выполните следующие действия:

1.  Создайте пассивное вторичное пространство имен. Код показан в предыдущем разделе.
2.  Удалите оставшиеся сообщения из очереди.

## <a name="alias-connection-string-and-test-code"></a>Строка подключения псевдонима и тестирование кода

Если вы хотите протестировать процесс отработки отказа, напишите пример приложения, которое передает сообщения в основное пространство имен с использованием псевдонима. Для этого убедитесь, что получаете строку подключения псевдонима из активного пространства имен. В текущем выпуске предварительной версии нету интерфейса для непосредственного получения строки подключения. В следующем примере код подключается до и после отработки отказа:

```csharp
var accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRPrimaryNS, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;

if(aliasPrimaryConnectionString == null)
{
    accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRSecondaryNS, "RootManageSharedAccessKey");
    aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
    aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
}
```

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь со справочными материалами по [REST API для географического аварийного восстановления](/rest/api/servicebus/disasterrecoveryconfigs).
- Запустите пример географического аварийного восстановления [на GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Ознакомьтесь с примером [географического аварийного восстановления, отправляющим сообщения псевдониму](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Дополнительную информацию об обмене сообщениями через служебную шину см. в следующих статьях:

* [Базовая информация о служебной шине](service-bus-fundamentals-hybrid-solutions.md)
* [Очереди, разделы и подписки служебной шины](service-bus-queues-topics-subscriptions.md)
* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Как использовать разделы и подписки служебной шины](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Azure Service Bus REST API](/rest/api/servicebus/) (REST API служебной шины Azure) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
