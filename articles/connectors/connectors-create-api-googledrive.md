---
title: "Добавление соединителя Google Диска в приложения логики | Документация Майкрософт"
description: "Обзор соединителя Google-диска с параметрами интерфейса API REST"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2bcebc5-02d2-435b-b0da-ef53bc51c4b6
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia; ladocs
ms.openlocfilehash: c066a10b33e172eb5f16eede43ec407794000c90
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-google-drive-connector"></a>Начало работы с соединителем Google-диска
Подключитесь к Google-диску, чтобы создавать файлы, получать строки и выполнять множество других действий. С помощью Google-диска вы можете: 

* формировать бизнес-процессы на основе данных, получаемых в результате поиска; 
* использовать действия для поиска изображений, новостей и многого другого. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно найти видеоролик, а затем с помощью Twitter опубликовать его в веб-канале Twitter.

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-the-connection-to-google-drive"></a>Создание подключения к Google-диску
При добавлении соединителя в приложения логики эти приложения необходимо авторизовать для подключения к Google-диску.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

После создания подключения укажите свойства Google-диска, такие как путь к папке или имя файла. 

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/googledrive/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).
