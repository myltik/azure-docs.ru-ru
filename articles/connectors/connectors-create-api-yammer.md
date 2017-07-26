---
title: "Добавление соединителя Yammer в Azure Logic Apps | Документация Майкрософт"
description: "Обзор соединителя Yammer с параметрами API REST"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: c7a213343b4fb2b5a89a5052a459061b404a431c
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-yammer-connector"></a>Начало работы с соединителем Yammer
Вы можете подключаться к Yammer для доступа к беседам в корпоративной сети. С помощью Yammer вы можете:

* формировать бизнес-процессы на основе данных, получаемых из Yammer; 
* использовать триггеры при поступлении нового сообщения в группе или веб-канале, на который вы подписаны;
* использовать действия для публикации сообщений, получения всех сообщений и т. д. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, при появлении нового сообщения вы можете отправлять сообщения электронной почты с помощью Office 365.

Для начала создайте приложение логики, как описано [здесь](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-yammer"></a>Создание подключения к Yammer
Чтобы использовать соединитель Yammer, сначала нужно создать **подключение**, а затем указать данные для приведенных ниже свойств. 

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| Маркер |Да |Указание учетных данных Yammer |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/yammer/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).
