---
title: "Использование соединителя Office 365 Видео в приложениях логики | Документация Майкрософт"
description: "Начало использования соединителя Office 365 Видео в приложениях логики службы приложений Microsoft Azure"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 738e5aa7-2523-4116-8b65-211b9063852d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: f0e3613d4a3fd5478787c0365eb7a0bcde886c81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-office365-video-connector"></a>Начало работы с соединителем Office 365 Видео
Подключившись к Office 365 Видео, вы сможете получать информацию об Office 365 Видео, список видео и многое другое. С помощью Office 365 Видео вы можете:

* формировать бизнес-процессы на основе данных, получаемых из Office 365 Видео; 
* использовать действия для проверки состояния видеопортала, получения списка всех видео, имеющихся в канале, и т. д. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно воспользоваться соединителем Поиска Bing для поиска видео в Office 365, а затем соединителем Office 365 Видео для получения сведений об этом видео. Если видео соответствует вашим требованиям, его можно опубликовать в Facebook. 

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-office365-video-connector"></a>Создание подключения к соединителю Office 365 Видео
При добавлении этого соединителя в приложения логики необходимо войти в учетную запись Office 365 Видео и разрешить приложениям логики подключаться к вашей учетной записи.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

После создания подключения укажите свойства Office 365 Видео, такие как имя клиента или идентификатор канала. 


## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/office365videoconnector/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).