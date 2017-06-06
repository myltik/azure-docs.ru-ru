---
title: "Создание концентратора событий Azure | Документация Майкрософт"
description: "Узнайте, как создать пространство имен концентраторов событий Azure и концентратор событий с помощью портала Azure."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: ff99e327-c8db-4354-9040-9c60c51a2191
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 2ef14fccda5c38ebdbf9d64b4766b45503755af0
ms.contentlocale: ru-ru
ms.lasthandoff: 05/04/2017


---

# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Создание пространства имен концентраторов событий и концентратора событий с помощью портала Azure

## <a name="create-an-event-hubs-namespace"></a>Создание пространства имен концентраторов событий
1. Войдите на [портал Azure][Azure portal] и щелкните **Создать** в левой верхней части экрана.
1. Последовательно выберите **Интернет вещей** и **Концентраторы событий**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)
1. В колонке **Создание пространства имен** укажите имя пространства имен. Система немедленно проверяет, доступно ли оно.
   
    ![](./media/event-hubs-create/create-event-hub1.png)
1. Убедившись, что пространство имен доступно, выберите ценовую категорию: "Базовый" или "Стандартный". Также выберите подписку Azure, группу ресурсов и расположение для создания ресурса. 
1. Щелкните **Создать** , чтобы создать пространство имен.
1. В списке пространств имен концентраторов событий щелкните созданное пространство имен.      
   
    ![](./media/event-hubs-create/create-event-hub2.png)
1. В колонке пространства имен щелкните **Концентраторы событий**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

## <a name="create-an-event-hub"></a>Создание концентратора событий

1. Щелкните **Добавить концентратор событий**в верхней части колонки.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
1. Введите имя концентратора событий, а затем щелкните **Создать**.
   
    ![](./media/event-hubs-create/create-event-hub5.png)
1. В списке концентраторов событий щелкните имя только что созданного концентратора событий. 
    
    ![](./media/event-hubs-create/create-event-hub6.png)
1. В колонке пространства имен (не в конкретной колонке концентратора событий) щелкните **Политики общего доступа**, а затем щелкните **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)
1. Нажмите кнопку копирования, чтобы скопировать строку подключения **RootManageSharedAccessKey** в буфер обмена. Сохраните эту строку. Она вам понадобится позже.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

Теперь концентратор событий создан, и у вас есть строки подключения, необходимые для отправки и приема событий.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о концентраторах событий см. в следующих статьях:

* [Обзор концентраторов событий](event-hubs-what-is-event-hubs.md)
* [Общие сведения об API концентраторов событий](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/
