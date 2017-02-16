---
title: "Часто задаваемые вопросы по Azure IoT Suite | Документация Майкрософт"
description: "Часто задаваемые вопросы об IoT Suite"
services: 
suite: iot-suite
documentationcenter: 
author: aguilaaj
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: araguila
translationtype: Human Translation
ms.sourcegitcommit: 69d4439a20fec8caaabdb43444cabd81f7d1b7c8
ms.openlocfilehash: 6245c2a0f73ff63e3bfb5fe16112f89ed0efdf30


---
# <a name="frequently-asked-questions-for-iot-suite"></a>Часто задаваемые вопросы об IoT Suite
### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>В чем разница между удалением группы ресурсов на портале Azure и нажатием кнопки "Удалить" в предварительно настроенном решении на сайте azureiotsuite.com?
* Если удалить предварительно настроенное решение на сайте [azureiotsuite.com][lnk-azureiotsuite], будут удалены все ресурсы, подготовленные при создании этого решения. Если вы добавляли в группу ресурсов дополнительные ресурсы, они также будут удалены. 
* Если удалить группу ресурсов на [портале Azure][lnk-azure-portal], будут удалены ресурсы в этой группе. Вам также придется удалить приложение Azure Active Directory, связанное с предварительно настроенным решением, на[ классическом портале Azure][lnk-classic-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Сколько экземпляров центров IoT можно подготовить в рамках одной подписки?
Десять. Вы можете направить [запрос в службу поддержки Azure][link-azuresupportticket], чтобы увеличить это количество, но по умолчанию в рамках одной подписки можно подготовить только 10 экземпляров Центра Интернета вещей, как описано в разделе об [ограничениях подписки Azure][link-azuresublimits]. Так как каждое предварительно настроенное решение подготавливает новый Центр Интернета вещей, в рамках одной подписки можно подготовить до 10 таких решений. 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>Сколько экземпляров DocumentDB можно подготовить в рамках одной подписки?
Пятьдесят. Вы можете направить [запрос в службу поддержки Azure][link-azuresupportticket], чтобы увеличить это количество, но по умолчанию в рамках одной подписки можно подготовить только 50 экземпляров DocumentDB. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Сколько бесплатных API-интерфейсов Карт Bing можно подготовить в рамках одной подписки?
Два. В рамках подписки Azure можно создать только две карты Bing уровня 1 с внутренними транзакциями для корпоративных планов. Решение для удаленного мониторинга по умолчанию подготавливается с помощью плана уровня 1 с внутренними транзакциями. Иными словами, используя одну подписку, в которую не вносились изменения, вы можете подготовить не более двух решений для удаленного мониторинга.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>У меня есть развернутое решение для удаленного мониторинга со статической картой. Как мне добавить интерактивную карту Bing?
1. Получите на [портале Azure][lnk-azure-portal] ключ QueryKey API для Карт Bing для предприятий: 
   
   1. На [портале Azure][lnk-azure-portal] перейдите в группу ресурсов с учетной записью API для Карт Bing для предприятий.
   2. Щелкните **Все параметры** и **Управление ключами**. 
   3. Вы увидите два ключа: **MasterKey** и **QueryKey**. Скопируйте значение ключа **QueryKey**.
      
      > [!NOTE]
      > У вас нет учетной записи Bing Maps API for Enterprise? Создайте ее на [портале Azure][lnk-azure-portal], щелкнув "+ Создать". Затем найдите API для Карт Bing для предприятий и следуйте подсказкам по созданию.
      > 
      > 
2. Разверните последний фрагмент кода из архива [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].
3. Запустите развертывание (локально или в облаке) в соответствии с руководством по развертыванию, которое находится в папке /docs/ репозитория. 
4. Запустив развертывание (локально или в облаке), найдите в корневой папке файл *.user.config, созданный во время развертывания. Откройте этот файл в текстовом редакторе. 
5. Измените следующую строку, чтобы включить скопированное из ключа **QueryKey** значение: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Можно ли создать предварительно настроенное решение при наличии Microsoft Azure для DreamSpark?
Сейчас нельзя создавать предварительно настроенные решения с использованием учетной записи [Microsoft Azure для DreamSpark][lnk-dreamspark]. Но вы можете создать [бесплатную пробную учетную запись Azure][lnk-30daytrial] всего за несколько минут. Это позволит вам создать предварительно настроенное решение.

### <a name="how-do-i-delete-an-aad-tenant"></a>Как удалить клиент AAD?
Ознакомьтесь с записью блога Эрика Голпа (Eric Golpe) [Walkthrough of Deleting an Azure AD Tenant][lnk-delete-aad-tennant] (Пошаговое руководство по удалению клиента Azure AD).

### <a name="next-steps"></a>Дальнейшие действия
Вы также можете ознакомиться с другими функциями и возможностями предварительно настроенных решений IoT Suite.

* [Обзор предварительно настроенного решения прогнозируемого обслуживания][lnk-predictive-overview]
* [Все аспекты безопасности Интернета вещей][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx



<!--HONumber=Jan17_HO1-->


