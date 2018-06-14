---
title: Имя издателя и ключ издателя в службах BizTalk | Документация Майкрософт
description: Узнайте, как извлечь имя и ключ издателя для Service Bus или управления доступом (ACS) в службах BizTalk. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 067fe356-d1aa-420f-b2f2-1a418686470a
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 18eac72d75680ab12c4a0bea9dfc5ac8a5fce566
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2017
ms.locfileid: "24103435"
---
# <a name="biztalk-services-issuer-name-and-issuer-key"></a>Службы BizTalk: имя и ключ издателя

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

В службах BizTalk в Azure используются имя и ключ издателя Service Bus, а также имя и ключ издателя службы Access Control. В частности:

| Задача | Используемые имя и ключ издателя |
| --- | --- |
| Развертывание приложения из Visual Studio |Имя и ключ издателя для службы Access Control |
| Настройка портала служб BizTalk в Azure |Имя и ключ издателя для службы Access Control |
| Создание бизнес-ретрансляций со службами адаптера BizTalk в Visual Studio |Имя и ключ издателя шины обслуживания |

В этом разделе перечислены шаги для извлечения имени и ключа издателя. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Имя и ключ издателя для службы Access Control
Имя и ключ издателя для службы Access Control используются в следующих областях:

* Приложение службы BizTalk в Azure, созданное в среде Visual Studio: для успешного развертывания в Azure приложения службы BizTalk, созданного в среде Visual Studio, необходимо ввести имя и ключ издателя для службы Access Control. 
* Портал служб BizTalk Azure. При открытии портала служб BizTalk во время создания службы BizTalk имя и ключ издателя для службы контроля доступа автоматически регистрируются для развертываний с теми же значениями службы контроля доступа.

### <a name="get-the-access-control-issuer-name-and-issuer-key"></a>Имя и ключ издателя для службы контроля доступа

Чтобы использовать ACS для аутентификации и получить значения имени и ключа издателя, сделайте следующее:

1. Установите [командлеты Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Добавьте учетную запись Azure: `Add-AzureAccount`.
3. Получите имя подписки: `get-azuresubscription`.
4. Выберите свою подписку: `select-azuresubscription <name of your subscription>`. 
5. Создайте пространство имен: `new-azuresbnamespace <name for the service bus> "Location" -CreateACSNamespace $true -NamespaceType Messaging`.

    Пример:    `new-azuresbnamespace biztalksbnamespace "South Central US" -CreateACSNamespace $true -NamespaceType Messaging`
      
5. При создании пространства имен ACS (занимает несколько минут) значения имени и ключа издателя указываются в строке подключения: 

    ```
    Name                  : biztalksbnamespace
    Region                : South Central US
    DefaultKey            : abcdefghijklmnopqrstuvwxyz
    Status                : Active
    CreatedAt             : 10/18/2016 9:36:30 PM
    AcsManagementEndpoint : https://biztalksbnamespace-sb.accesscontrol.windows.net/
    ServiceBusEndpoint    : https://biztalksbnamespace.servicebus.windows.net/
    ConnectionString      : Endpoint=sb://biztalksbnamespace.servicebus.windows.net/;SharedSecretIssuer=owner;SharedSecretValue=abcdefghijklmnopqrstuvwxyz
    NamespaceType         : Messaging
    ```

Итог:  
имя издателя — SharedSecretIssuer;  
ключ издателя — SharedSecretKey.

Дополнительные сведения о командлете [New-AzureSBNamespace](https://msdn.microsoft.com/library/dn495165.aspx). 

## <a name="service-bus-issuer-name-and-issuer-key"></a>Имя и ключ издателя шины обслуживания
Имя и ключ издателя шины обслуживания используются службами адаптера BizTalk. В проекте служб BizTalk в Visual Studio можно использовать службы адаптера BizTalk для подключения к локальной бизнес-системе. Для подключения создайте бизнес-ретранслятор и введите сведения о бизнес-системе. При этом также вводится имя и ключ издателя шины обслуживания.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Чтобы извлечь имя и ключ издателя Service Bus
1. Войдите на [портале Azure](http://portal.azure.com).
2. Выполните поиск по фразе **Service Bus** и выберите свое пространство имен. 
3. Откройте вкладку свойств **Политики общего доступа**, выберите политику и просмотрите **строку подключения** для этих значений ключей и имени.  

## <a name="next"></a>Далее
Дополнительная информация о службах BizTalk в Azure

* [Установка пакета SDK для служб BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Руководства по службам BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Как приступить к работе с пакетом SDK для служб BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Службы BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>См. также
* [Инструкция по использованию службы управления ACS для настройки удостоверений службы](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
* [Службы BizTalk. Диаграмма выпусков Developer, Basic, Standard и Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Создание служб BizTalk с помощью портала Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [Службы BizTalk. Диаграмма состояния подготовки](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [Службы BizTalk: вкладки «Панель мониторинга», «Монитор» и «Масштаб»](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Службы BizTalk: резервное копирование и восстановление](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Службы BizTalk: регулирование](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>

