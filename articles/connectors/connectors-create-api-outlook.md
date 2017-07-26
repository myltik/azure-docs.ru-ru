---
title: "Соединитель Outlook.com в Azure Logic Apps | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Соединитель Outlook.com позволяет управлять электронной почтой, календарями и контактами. С его помощью можно выполнять различные действия, такие как отправка сообщения электронной почты, планирование встреч, добавление контактов и т. д."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: bde1629504c97cf6706b42219570ffa6243073dd
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-outlookcom-connector"></a>Начало работы с соединителем Outlook.com
Соединитель Outlook.com позволяет управлять электронной почтой, календарями и контактами. С его помощью можно выполнять различные действия, такие как отправка сообщения электронной почты, планирование встреч, добавление контактов и т. д.

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-outlookcom"></a>Создание подключения к Outlook.com
Для создания приложений логики с помощью Outlook.com необходимо создать **подключение**, а затем указать данные для приведенных ниже свойств.

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| Маркер |Да |Укажите учетные данные Outlook.com |

Созданное подключение можно использовать для выполнения действий и прослушивания триггеров, описанных в этой статье.

> [!INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)]
>

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/outlook/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).
