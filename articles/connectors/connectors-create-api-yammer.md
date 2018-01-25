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
ms.openlocfilehash: 37f72d829fc50a0f967f08e068c553f5026f35eb
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-yammer-connector"></a>Начало работы с соединителем Yammer
Вы можете подключаться к Yammer для доступа к беседам в корпоративной сети. С помощью Yammer вы можете:

* формировать бизнес-процессы на основе данных, получаемых из Yammer; 
* использовать триггеры при поступлении нового сообщения в группе или веб-канале, на который вы подписаны;
* использовать действия для публикации сообщений, получения всех сообщений и т. д. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, при появлении нового сообщения вы можете отправлять сообщения электронной почты с помощью Office 365.

Для начала создайте приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-yammer"></a>Создание подключения к Yammer
Чтобы использовать соединитель Yammer, сначала нужно создать **подключение**, а затем указать данные для приведенных ниже свойств. 

| Свойство | Обязательно | ОПИСАНИЕ |
| --- | --- | --- |
| по маркеру |Yes |Указание учетных данных Yammer |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/yammer/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).