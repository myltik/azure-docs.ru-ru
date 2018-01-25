---
title: "Как использовать соединитель служебной шины Azure в приложениях логики | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Подключитесь к служебной шине Azure для отправки и получения сообщений. Вы можете выполнять такие действия, как отправка в очередь, отправка в раздел, получение из очереди и получение из подписки."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 89bf0ffec759fca4af5f99af1b6a2dd8d641ff6f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-azure-service-bus-connector"></a>Начало работы с соединителем служебной шины Azure
Подключитесь к служебной шине Azure для отправки и получения сообщений. Вы можете выполнять такие действия, как отправка в очередь, отправка в раздел, получение из очереди и получение из подписки.

Чтобы использовать [соединитель](apis-list.md), сначала нужно создать приложение логики. Вы можете начать с [создания приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-service-bus"></a>Подключение к служебной шине
Чтобы обеспечить доступ приложения логики к какой-либо службе, сначала необходимо создать подключение к этой службе. Таким образом вы установите [соединение](connectors-overview.md) между приложением логики и другой службой.  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>Использование триггера служебной шины
Триггер — это событие, которое можно использовать для запуска рабочего процесса, определенного в приложении логики. [Дополнительные сведения о триггерах](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>Использование действия служебной шины
Действие — это операция, выполняемая рабочим процессом, определенным в приложении логики. [Дополнительные сведения о действиях](../logic-apps/logic-apps-overview.md#logic-app-concepts).

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/servicebus/). 

## <a name="next-steps"></a>Дополнительная информация
[Создание приложения логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

