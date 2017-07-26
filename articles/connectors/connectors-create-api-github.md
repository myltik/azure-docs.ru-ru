---
title: "Соединитель GitHub в Azure Logic Apps | Документация Майкрософт"
description: "Создание приложений логики с помощью службы приложений Azure. GitHub — это веб-служба размещения репозиториев Git. Она предоставляет все возможности распределенного управления редакциями и исходным кодом (SCM) Git, а также собственные функции."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: d4614b0ceff0ec0d36dbb1a136551f985f2fc1a1
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-github-connector"></a>Начало работы с соединителем GitHub
GitHub — это веб-служба размещения репозиториев Git. Она предоставляет все возможности распределенного управления редакциями и исходным кодом (SCM) Git, а также собственные функции.

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-github"></a>Создание подключения к GitHub
Для создания приложений логики с помощью GitHub необходимо создать **подключение**, а затем указать данные для следующих свойств. 

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| Маркер |Да |Укажите учетные данные GitHub |

Созданное подключение можно использовать для выполнения действий и прослушивания триггеров, описанных в этой статье. 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/github/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).
