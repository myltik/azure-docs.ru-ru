---
title: "Подключение к GitHub при помощи Azure Logic Apps | Документация Майкрософт"
description: "Автоматизация рабочих процессов для GitHub при помощи Azure Logic Apps"
services: logic-apps
documentationcenter: 
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
ms.date: 03/02/2018
ms.author: mandia; ladocs
ms.openlocfilehash: cd7cd3babbfb7efc5917d3a7ec5b9d10112ba791
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2018
---
# <a name="connect-to-github"></a>Подключение к GitHub

GitHub — это веб-служба, где размещен репозиторий Git. Она обеспечивает все возможности распределенного управления версиями и исходным кодом (SCM) в Git, а также другие функции.

Чтобы приступить к работе с соединителем GitHub, [сначала создайте приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Создание подключения к GitHub

Чтобы использовать в приложении логики соединитель GitHub, сначала создайте *подключение*, а затем введите сведения для следующих свойств: 

| Свойство | Обязательно | ОПИСАНИЕ | 
| -------- | -------- | ----------- | 
| по маркеру | Yes | Укажите свои учетные данные GitHub. |

Создав подключение, вы можете выполнять действия и ожидать передачи данных от триггеров, описанных в этой статье.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Сведения о соединителях

Сведения о триггерах, действиях, определенных в Swagger, и всех ограничениях см. в статье о [соединителях](/connectors/github/).

## <a name="find-more-connectors"></a>Другие соединители

* Просмотрите [список соединителей](apis-list.md).