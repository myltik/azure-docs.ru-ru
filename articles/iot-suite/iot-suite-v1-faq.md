---
title: Часто задаваемые вопросы по Azure IoT Suite | Документация Майкрософт
description: Часто задаваемые вопросы об IoT Suite
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 77b9fab68b66fe8998733a8f890acb9dd6b97899
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34702337"
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Часто задаваемые вопросы об IoT Suite

Дополнительные сведения см. также в статье [Часто задаваемые вопросы о предварительно настроенном решении для подключенной фабрики IoT Suite](../iot-accelerators/iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Где можно найти исходный код для предварительно настроенных решений?

Исходный код хранится в следующих репозиториях GitHub:
* [Предварительно настроенное решение для удаленного мониторинга][lnk-remote-monitoring-github]
* [Предварительно настроенное решение по диагностическому обслуживанию][lnk-predictive-maintenance-github]
* [Предварительно настроенное решение для подключенной фабрики](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>Как обновить до последней версии предварительно настроенное решение удаленного мониторинга, которое использует функции управления устройствами Центра Интернета вещей?

* При развертывании предварительно настроенного решения с сайта https://www.azureiotsuite.com/ всегда развертывается новый экземпляр последней его версии.
* При развертывании предварительно настроенного решения с помощью командной строки можно обновить существующее развертывание, добавив новый код. См. сведения о [развертывании в облаке][lnk-cloud-deployment] в [репозитории][lnk-remote-monitoring-github] GitHub.

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>Как добавить поддержку нового метода устройства в предварительно настроенное решение для удаленного мониторинга?

См. сведения в разделе [Добавление в симулятор поддержки для нового метода][lnk-add-method] статьи [Настройка предварительно настроенного решения][lnk-customize].

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>Почему виртуальное устройство игнорирует изменения требуемых свойств?
В предварительно настроенном решении для мониторинга код виртуального устройства использует только требуемые свойства **Desired.Config.TemperatureMeanValue** и **Desired.Config.TelemetryInterval**, чтобы обновить сообщаемые свойства. Все прочие запросы на изменение требуемых свойств игнорируются.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>Почему устройство не отображается в списке устройств на панели мониторинга решения?

Для возврата списка устройств на панели мониторинга решения используется запрос. В настоящее время запрос не может возвращать более 10 000 устройств. Попробуйте ограничить условие поиска для запроса.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>В чем разница между удалением группы ресурсов на портале Azure и нажатием кнопки "Удалить" в предварительно настроенном решении на сайте azureiotsuite.com?

* Если удалить предварительно настроенное решение на сайте [azureiotsuite.com][lnk-azureiotsuite], будут удалены все ресурсы, подготовленные при создании этого решения. Если вы добавляли в группу ресурсов дополнительные ресурсы, они также будут удалены. 
* Если удалить группу ресурсов на [портале Azure][lnk-azure-portal], будут удалены ресурсы в этой группе. Кроме того, на [портале Azure][lnk-azure-portal] вам нужно будет удалить приложение Azure Active Directory, связанное с предварительно настроенным решением.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Сколько экземпляров центров Интернета вещей можно подготовить в рамках одной подписки?

По умолчанию [в рамках одной подписки можно подготовить 10 экземпляров Центра Интернета вещей][link-azuresublimits]. Можно создать [запрос в службу поддержки Azure][link-azuresupportticket], чтобы увеличить этот лимит. Так как каждое предварительно настроенное решение подготавливает новый Центр Интернета вещей, в рамках одной подписки можно подготовить до 10 таких решений. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Сколько экземпляров Azure Cosmos DB можно подготовить в рамках одной подписки?

Пятьдесят. Вы можете направить [запрос в службу поддержки Azure][link-azuresupportticket], чтобы увеличить это количество, но по умолчанию в рамках одной подписки можно подготовить только 50 экземпляров Cosmos DB. 

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

> [!NOTE]
> Microsoft Azure для DreamSpark теперь называется Microsoft Imagine для учащихся.

Сейчас нельзя создавать предварительно настроенные решения с использованием учетной записи [Microsoft Azure для DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/). Но вы можете создать [бесплатную пробную учетную запись Azure](https://azure.microsoft.com/free/) всего за несколько минут. Это позволит вам создать предварительно настроенное решение.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Можно ли создать предварительно настроенное решение при наличии подписки поставщика облачных решений (CSP)?

В настоящее время создание предварительно настроенных решений с помощью подписки поставщика облачных решений (CSP) недоступно. Но вы можете создать [бесплатную пробную учетную запись Azure][lnk-30daytrial] всего за несколько минут. Это позволит вам создать предварительно настроенное решение.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Как удалить клиент Azure AD?

Ознакомьтесь с записью блога Эрика Голпа (Eric Golpe) [Walkthrough of Deleting an Azure AD Tenant][lnk-delete-aad-tennant] (Пошаговое руководство по удалению клиента Azure AD).

### <a name="next-steps"></a>Дополнительная информация

Вы также можете ознакомиться с другими функциями и возможностями предварительно настроенных решений IoT Suite.

* [Обзор предварительно настроенного решения прогнозируемого обслуживания][lnk-predictive-overview]
* [Начало работы с предварительно настроенным решением для подключенной фабрики](../iot-accelerators/iot-accelerators-connected-factory-overview.md)
* [Все аспекты безопасности Интернета вещей][lnk-security-groundup]

[lnk-predictive-overview]:../iot-accelerators/iot-accelerators-predictive-overview.md
[lnk-security-groundup]:../iot-accelerators/securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
[lnk-cloud-deployment]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
[lnk-add-method]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md#add-support-for-a-new-method-to-the-simulator
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-predictive-maintenance-github]: https://github.com/Azure/azure-iot-predictive-maintenance
