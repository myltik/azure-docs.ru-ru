---
title: "Как использовать соединитель Salesforce в приложениях логики | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Соединитель Salesforce предоставляет API для работы с объектами Salesforce."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/05/2016
ms.author: mandia; ladocs
ms.openlocfilehash: c2e2efd356382df9404f5c4ed54f24758b2cd22b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-salesforce-connector"></a>Начало работы с соединителем Salesforce
Соединитель Salesforce предоставляет API для работы с объектами Salesforce.

Чтобы использовать [соединитель](apis-list.md), сначала нужно создать приложение логики. Вы можете начать с [создания приложения логики](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-salesforce-connector"></a>Подключение к соединителю Salesforce
Чтобы обеспечить доступ приложения логики к какой-либо службе, сначала необходимо создать *подключение* к этой службе. Таким образом вы установите [соединение](connectors-overview.md) между приложением логики и другой службой.  

### <a name="create-a-connection-to-salesforce-connector"></a>Создание подключения к соединителю Salesforce
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>Использование триггера соединителя Salesforce
Триггер — это событие, которое можно использовать для запуска рабочего процесса, определенного в приложении логики. [Дополнительные сведения о триггерах](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>Добавить условие
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>Использование действия соединителя Salesforce
Действие — это операция, выполняемая рабочим процессом, определенным в приложении логики. [Дополнительные сведения о действиях](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/salesforce/). 

## <a name="next-steps"></a>Дальнейшие действия
[Создайте приложение логики](../logic-apps/logic-apps-create-a-logic-app.md)

