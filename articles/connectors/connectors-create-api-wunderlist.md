---
title: "Соединитель Wunderlist в Azure Logic Apps | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как создать подключение к Wunderlist и с его помощью создавать рабочие процессы в приложениях логики."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 899110992cc52ca5edf1706320fd5570473de784
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-wunderlist-connector"></a>Начало работы с соединителем Wunderlist
Wunderlist — это приложение для ведения списка дел и управления задачами, помогающее справляться с делами.  Wunderlist позволяет легко составлять и вести списки дел, а также предоставлять к ним доступ, будь то отправка списка покупок, работа над проектом или планирование отпуска. Wunderlist мгновенно синхронизируется между телефоном, планшетом и компьютером, обеспечивая доступ к задачам из любой точки мира.

Для начала создайте приложение логики, как описано [здесь](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-wunderlist"></a>Создание подключения к Wunderlist
Для создания приложений логики с помощью Wunderlist необходимо создать **подключение**, а затем указать данные для приведенных ниже свойств.

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| Маркер |Да |Учетные данные Wunderlist |

Созданное подключение можно использовать для выполнения действий и прослушивания триггеров.

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/wunderlist/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).