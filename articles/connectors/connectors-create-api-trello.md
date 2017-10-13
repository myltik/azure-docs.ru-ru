---
title: "Соединитель Trello в Azure Logic Apps | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Trello позволяет отслеживать все ваши проекты как на работе, так и дома.  Это простое, бесплатное, гибкое и наглядное средство организации данных и управления проектами.  Подключитесь к Trello для управления досками, списками и карточками."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 526a14710f24ee4a4b61a11873aa6caa0b47dc10
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-trello-connector"></a>Начало работы с соединителем Trello
Trello позволяет отслеживать все ваши проекты как на работе, так и дома.  Это простое, бесплатное, гибкое и наглядное средство организации данных и управления проектами.  Подключитесь к Trello для управления досками, списками и карточками.

Для начала создайте приложение логики, как описано [здесь](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-trello"></a>Создание подключения к Trello
Для создания приложения логики с Trello сначала нужно создать **подключение**, а затем указать данные для следующих свойств:

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| Маркер |Да |Укажите учетные данные Trello |

Созданное подключение можно использовать для выполнения действий и прослушивания триггеров.

> [!INCLUDE [Steps to create a connection to Trello](../../includes/connectors-create-api-trello.md)]
> 

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/trello/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).