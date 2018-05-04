---
title: Часто задаваемые вопросы по Azure IoT Suite | Документация Майкрософт
description: Часто задаваемые вопросы об IoT Suite
services: iot-suite
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
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 49e94e771deb4582b922400d81e8388faf164f40
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Часто задаваемые вопросы об IoT Suite

См. также ответы на часто задаваемые вопросы по [подключенной фабрике](iot-suite-faq-cf.md) и [удаленному мониторингу](iot-suite-faq-rm-v2.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Где можно найти исходный код для предварительно настроенных решений?

Исходный код хранится в следующих репозиториях GitHub:

* [Remote Monitoring preconfigured solution with Azure IoT](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) (Предварительно настроенное решение для удаленного мониторинга с Центром Интернета вещей Azure)
* [Remote Monitoring preconfigured solution with Azure IoT](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) (Предварительно настроенное решение для удаленного мониторинга с Центром Интернета вещей Azure) (Java)
* [Microsoft Azure IoT Suite](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Предварительно настроенное решение для подключенной фабрики](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-preconfigured-solutions"></a>С помощью каких пакетов SDK можно разработать клиенты устройств для предварительно настроенных решений?

Ссылки на пакеты SDK для устройств Интернета вещей для других языков (C, .NET, Java, Node.js, Python) можно найти в репозиториях GitHub для [пакетов SDK Microsoft Azure для Интернета вещей](https://github.com/Azure/azure-iot-sdks).

Если вы используете устройство DevKit, ресурсы и примеры можно найти в репозитории GitHub для [пакета SDK для DevKit](https://github.com/Microsoft/devkit-sdk).

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>Доступна ли новая архитектура микрослужб для всех трех типов предварительно настроенных решений?

Сейчас архитектура микрослужб используется только в решении для удаленного мониторинга, так как в нем охватывается самый широкий сценарий.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Каковы преимущества новой архитектуры на базе микрослужб с открытым кодом в новом обновлении?

За последние два года облачная архитектура значительно улучшилась. Микрослужбы стали оптимальным вариантом, который обеспечивает масштабируемость и гибкость без снижения скорости разработки. В некоторых службах Майкрософт по умолчанию используется шаблон архитектуры, который обеспечивает высокую надежность и масштабируемость. Мы реализуем полученные знания на практике, чтобы предоставить своим клиентам преимущества.

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>Доступно ли новое предварительно настроенное решение в том же географическом регионе, что и имеющееся?

Да, новое решение для удаленного мониторинга доступно в том же географическом регионе.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>В чем разница между удалением группы ресурсов на портале Azure и нажатием кнопки "Удалить" в предварительно настроенном решении на сайте azureiotsuite.com?

* Если удалить предварительно настроенное решение на сайте [azureiotsuite.com](https://www.azureiotsuite.com/), будут удалены все ресурсы, подготовленные при его создании. Если вы добавляли в группу ресурсов дополнительные ресурсы, они также будут удалены.
* Если удалить группу ресурсов на [портале Azure](https://portal.azure.com), будут удалены ресурсы в этой группе. Вам также придется удалить приложение Azure Active Directory, связанное с предварительно настроенным решением.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>Можно ли продолжить использовать имеющиеся ресурсы в Azure IoT Suite?

Да. Любое имеющееся решение продолжает работать в подписке Azure, а исходный код остается доступным на GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Сколько экземпляров центров Интернета вещей можно подготовить в рамках одной подписки?

По умолчанию [в рамках одной подписки можно подготовить 10 экземпляров Центра Интернета вещей](../azure-subscription-service-limits.md#iot-hub-limits). Вы можете отправить [запрос в службу поддержки Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы увеличить этот лимит. Так как каждое предварительно настроенное решение подготавливает новый Центр Интернета вещей, в рамках одной подписки можно подготовить до 10 таких решений.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Сколько экземпляров Azure Cosmos DB можно подготовить в рамках одной подписки?

Пятьдесят. Вы можете отправить [запрос в службу поддержки Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы увеличить это количество, но по умолчанию в рамках одной подписки можно подготовить только 50 экземпляров Cosmos DB.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Сколько бесплатных API-интерфейсов Карт Bing можно подготовить в рамках одной подписки?

Два. В рамках подписки Azure можно создать только две карты Bing уровня 1 с внутренними транзакциями для корпоративных планов. Решение для удаленного мониторинга по умолчанию подготавливается с помощью плана уровня 1 с внутренними транзакциями. Иными словами, используя одну подписку, в которую не вносились изменения, вы можете подготовить не более двух решений для удаленного мониторинга.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Можно ли создать предварительно настроенное решение при наличии Microsoft Azure для DreamSpark?

> [!NOTE]
> Microsoft Azure для DreamSpark теперь называется Microsoft Imagine для учащихся.

Сейчас нельзя создавать предварительно настроенные решения с использованием учетной записи [Microsoft Azure для DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/). Но вы можете создать [бесплатную пробную учетную запись Azure](https://azure.microsoft.com/free/) всего за несколько минут. Это позволит вам создать предварительно настроенное решение.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Можно ли создать предварительно настроенное решение при наличии подписки поставщика облачных решений (CSP)?

В настоящее время создание предварительно настроенных решений с помощью подписки поставщика облачных решений (CSP) недоступно. Но вы можете создать [бесплатную пробную учетную запись Azure](https://azure.microsoft.com/free/) всего за несколько минут. Это позволит вам создать предварительно настроенное решение.

### <a name="how-do-i-delete-an-aad-tenant"></a>Как удалить клиент AAD?

Ознакомьтесь с записью блога Эрика Голпа (Eric Golpe) [Walkthrough of Deleting an Azure AD Tenant](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx) (Пошаговое руководство по удалению клиента Azure AD).

### <a name="next-steps"></a>Дополнительная информация

Вы также можете ознакомиться с другими функциями и возможностями предварительно настроенных решений IoT Suite.

* [Обзор возможностей предварительно настроенного решения удаленного мониторинга](iot-suite-remote-monitoring-explore.md)
* [Обзор предварительно настроенного решения прогнозируемого обслуживания](iot-suite-predictive-overview.md)
* [Начало работы с предварительно настроенным решением для подключенной фабрики](iot-suite-connected-factory-overview.md)
* [Комплексная защита в Интернете вещей](securing-iot-ground-up.md)