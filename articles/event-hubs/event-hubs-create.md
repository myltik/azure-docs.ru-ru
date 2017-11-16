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
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: e444c4505d4744c95e08c4ef0d33566356785c81
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2017
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Создание пространства имен концентраторов событий и концентратора событий с помощью портала Azure

## <a name="create-an-event-hubs-namespace"></a>Создание пространства имен концентраторов событий
1. Войдите на [портал Azure][Azure portal] и щелкните **Создать ресурс** в левой верхней части экрана.
1. Последовательно выберите **Интернет вещей** и **Концентраторы событий**.
   
    ![](./media/event-hubs-create/create-event-hub9.png)
1. В разделе **создания пространства имен** укажите имя пространства имен. Система немедленно проверяет, доступно ли оно.
   
    ![](./media/event-hubs-create/create-event-hub1.png)
1. Убедившись, что пространство имен доступно, выберите ценовую категорию: "Базовый" или "Стандартный". Также выберите подписку Azure, группу ресурсов и расположение для создания ресурса. 
1. Щелкните **Создать** , чтобы создать пространство имен. Полная подготовка ресурсов для системы может занять несколько минут.
2. На портале в списке пространств имен щелкните имя только что созданного пространства имен.
2. В колонке **Политики общего доступа** щелкните **RootManageSharedAccessKey**.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

3. Нажмите кнопку копирования, чтобы скопировать строку подключения **RootManageSharedAccessKey** в буфер обмена. Сохраните эту строку подключения во временном расположении папке, например в Блокноте, для последующего использования.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>Создание концентратора событий

1. В списке пространств имен концентраторов событий щелкните созданное пространство имен.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. В колонке пространства имен щелкните **Концентраторы событий**.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

1. Щелкните **Добавить концентратор событий**в верхней части колонки.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
1. Введите имя концентратора событий, а затем щелкните **Создать**.
   
    ![](./media/event-hubs-create/create-event-hub5.png)

Теперь концентратор событий создан, и у вас есть строки подключения, необходимые для отправки и приема событий.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о концентраторах событий см. в следующих статьях:

* [Обзор концентраторов событий](event-hubs-what-is-event-hubs.md)
* [Общие сведения об API концентраторов событий](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/