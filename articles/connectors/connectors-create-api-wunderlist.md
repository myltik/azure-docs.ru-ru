---
title: Соединитель Wunderlist в Azure Logic Apps | Документация Майкрософт
description: Из этой статьи вы узнаете, как создать подключение к Wunderlist и с его помощью создавать рабочие процессы в приложениях логики.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 4d1ae30724faa59dcdeffd21be9c67d280d574f6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-wunderlist-connector"></a>Начало работы с соединителем Wunderlist
Wunderlist — это диспетчер задач и списка дел, которые помогает пользователям выполнять их задачи.  Wunderlist позволяет легко составлять и вести списки дел, а также предоставлять к ним доступ. Это может быть список покупок, работа над проектом или планирование отпуска. Wunderlist мгновенно синхронизируется между телефоном, планшетом и компьютером, обеспечивая доступ к задачам из любой точки мира.

Для начала создайте приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-wunderlist"></a>Создание подключения к Wunderlist
Для создания приложений логики с помощью Wunderlist необходимо создать **подключение**, а затем указать данные для приведенных ниже свойств.

| Свойство | Обязательно | ОПИСАНИЕ |
| --- | --- | --- |
| по маркеру |Yes |Учетные данные Wunderlist |

Созданное подключение можно использовать для выполнения действий и прослушивания триггеров.

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/wunderlist/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).