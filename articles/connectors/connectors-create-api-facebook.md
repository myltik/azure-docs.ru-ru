---
title: "Добавление соединителя Facebook в приложения логики | Документация Майкрософт"
description: "Обзор соединителя Facebook с параметрами интерфейса API REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 27a9297c5d4cfb52f254ceb12e37423403e7f41b
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-facebook-connector"></a>Начало работы с соединителем Facebook
Подключение к Facebook позволяет оставлять публикации в хронике, получать канал страниц и выполнять другие действия. С помощью Facebook можно:

* формировать бизнес-процессы на основе данных, получаемых из Facebook; 
* использовать триггер при получении новой публикации;
* использовать действия, оставляющие публикации в хронике, получающие канал страниц и выполняющие другие действия. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно настроить трансляцию всех публикаций, которые появляются в хронике, в Twitter. 

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Создание подключения к Facebook
При добавлении соединителя в приложения логики эти приложения необходимо авторизовать для подключения к Facebook.

1. Вход в учетную запись Facebook
2. Выберите **Авторизовать**и разрешите приложениям логики подключаться к Facebook и использовать его. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 


## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/facebook/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).