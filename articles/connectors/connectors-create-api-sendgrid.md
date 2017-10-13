---
title: "SendGrid | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. Поставщик подключений SendGrid позволяет отправлять электронную почту и управлять списками получателей."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 9ff0591741899d65b8274fb14ab3f3c8db9abe36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-sendgrid-connector"></a>Начало работы с соединителем SendGrid
Поставщик подключений SendGrid позволяет отправлять электронную почту и управлять списками получателей.

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-sendgrid"></a>Создание подключения к SendGrid
Для создания приложений логики с помощью SendGrid необходимо создать **подключение**, а затем указать данные для приведенных ниже свойств. 

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| ApiKey |Да |Укажите ключ API SendGrid |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 


Созданное подключение можно использовать для выполнения действий и прослушивания триггеров.

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/sendgrid/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).