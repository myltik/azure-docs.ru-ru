---
title: "Добавление соединителя Пользователи Office 365 в приложения логики | Документация Майкрософт"
description: "Обзор соединителя Office 365 Пользователи с параметрами интерфейса API REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2146481-9105-4f56-b4c2-7ae340cb922f
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 2e7827e32a03b6f6af46f5bc65f0ed74f3065f86
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-office-365-users-connector"></a>Начало работы с соединителем Office 365 Пользователи
Подключившись к Office 365 Пользователи, вы сможете получать профили, искать пользователей и выполнять многие другие действия. С помощью Office 365 Пользователи вы можете:

* формировать бизнес-процессы на основе данных, получаемых из Office 365 Пользователи; 
* использовать действия для получения непосредственных подчиненных, профиля пользователя для менеджера и т. д. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно получить список непосредственных подчиненных того или иного лица и внести соответствующие изменения в базу данных SQL Azure. 

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Создание подключения к Office 365 Пользователи
При добавлении этого соединителя в приложения логики необходимо войти в учетную запись Office 365 Пользователи и разрешить приложениям логики подключаться к вашей учетной записи.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

После создания подключения укажите свойства Office 365 Пользователи, такие как идентификатор пользователя. Эти свойства описаны далее в **справочнике по REST API** .

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/officeusers/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).