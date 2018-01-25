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
ms.openlocfilehash: 3657955ca4280fecd3a0fb1ea64b90e0a5c5c765
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-wunderlist-connector"></a>Начало работы с соединителем Wunderlist
Wunderlist — это приложение для ведения списка дел и управления задачами, помогающее справляться с делами.  Wunderlist позволяет легко составлять и вести списки дел, а также предоставлять к ним доступ, будь то отправка списка покупок, работа над проектом или планирование отпуска. Wunderlist мгновенно синхронизируется между телефоном, планшетом и компьютером, обеспечивая доступ к задачам из любой точки мира.

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