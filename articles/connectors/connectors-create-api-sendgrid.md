---
title: SendGrid | Документация Майкрософт
description: Создание приложений логики с помощью службы приложений Azure. Поставщик подключений SendGrid позволяет отправлять электронную почту и управлять списками получателей.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: e87173b4a31a721ca3cd5af41d4a6d725c46c972
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-sendgrid-connector"></a>Начало работы с соединителем SendGrid
Поставщик подключений SendGrid позволяет отправлять электронную почту и управлять списками получателей.

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sendgrid"></a>Создание подключения к SendGrid
Для создания приложений логики с помощью SendGrid необходимо создать **подключение**, а затем указать данные для приведенных ниже свойств. 

| Свойство | Обязательно | ОПИСАНИЕ |
| --- | --- | --- |
| ApiKey |Yes |Укажите ключ API SendGrid |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 


Созданное подключение можно использовать для выполнения действий и прослушивания триггеров.

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/sendgrid/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).