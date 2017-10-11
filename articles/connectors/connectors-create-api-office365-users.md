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
ms.openlocfilehash: 330f733440932a769eb0fe6031cd0d947a820080
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-office-365-users-connector"></a>Начало работы с соединителем Office 365 Пользователи
Подключившись к Office 365 Пользователи, вы сможете получать профили, искать пользователей и выполнять многие другие действия. С помощью Office 365 Пользователи вы можете:

* формировать бизнес-процессы на основе данных, получаемых из Office 365 Пользователи; 
* использовать действия для получения непосредственных подчиненных, профиля пользователя для менеджера и т. д. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно получить список непосредственных подчиненных того или иного лица и внести соответствующие изменения в базу данных SQL Azure. 

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/logic-apps-create-a-logic-app.md).

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