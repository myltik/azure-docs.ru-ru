---
title: Добавление соединителя Twilio в Azure Logic Apps | Документация Майкрософт
description: Обзор соединителя Twilio с параметрами API REST
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 2bb7961c850fc8a35f3e114d9497941b188cf51b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-twilio-connector"></a>Начало работы с соединителем Twilio
Подключитесь к Twilio для отправки и получения глобальных SMS-, MMS- и IP-сообщений. С помощью Twilio вы можете:

* формировать бизнес-процессы на основе данных, получаемых из Twilio; 
* использовать действия для получения сообщений, вывода списка сообщений и выполнения многих других задач. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, новое полученное сообщение Twilio можно использовать в рабочем процессе служебной шины. 

Для начала создайте приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-twilio"></a>Создание подключения к Twilio
При добавлении этого соединителя в приложения логики введите указанные ниже значения Twilio:

| Свойство | Обязательно | ОПИСАНИЕ |
| --- | --- | --- |
| Идентификатор учетной записи |Yes |Введите идентификатор своей учетной записи Twilio |
| Маркер доступа |Yes |Введите свой маркер доступа Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Если у вас нет маркера доступа Twilio, см. статью об [удостоверениях пользователей и маркерах доступа](https://www.twilio.com/docs/api/chat/guides/identity).

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/twilio/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).