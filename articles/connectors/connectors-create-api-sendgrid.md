---
title: SendGrid | Документация Майкрософт
description: Создание приложений логики с помощью службы приложений Azure. Поставщик подключений SendGrid позволяет отправлять электронную почту и управлять списками получателей.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
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
ms.openlocfilehash: 0b34a76ecaf4997cbf66c3d026cd770aa8aa080d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295845"
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