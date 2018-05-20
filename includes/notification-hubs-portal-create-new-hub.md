---
title: включение файла
description: включение файла
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c718fa972b9d6cba13a13edd3c7e8df29fa63207
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
1. Войдите на [портале Azure](https://portal.azure.com).

2. Последовательно выберите **Создать ресурс** > **Интернет и мобильные устройства** > **Концентратор уведомлений**.
   
      ![Портал Azure: создание центра уведомлений](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. В поле **Центр уведомлений** введите уникальное имя. Выберите **регион**, **подписку** и **группу ресурсов** (если она уже создана). 
   
      Если у вас нет пространства имен служебной шины, можно использовать имя по умолчанию. Оно создается на основе имени концентратора (если имя пространства имен доступно).
    
      Если у вас уже есть пространство имен служебной шины, в котором требуется создать концентратор, сделайте следующее:

    a. В области **Пространство имен** выберите ссылку **Выбрать существующее**. 
   
    Б. Нажмите кнопку **Создать**.
   
      ![Портал Azure: настройка свойств концентратора уведомлений](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. Последовательно выберите **Уведомления** (значок колокольчика) и **Go to resource** (Перейти к ресурсу). 

      ![Портал Azure — "Уведомления" -> Go to resource (Перейти к ресурсу)](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)    
5. Выберите **Политики доступа** в списке. Запишите две строки подключения, которые отобразятся. Они требуются для дальнейшей обработки push-уведомлений.

      >[!IMPORTANT]
      >**НЕ** используйте в приложении DefaultFullSharedAccessSignature. Этот параметр можно использовать только в серверной части.
      >
   
      ![Портал Azure: строки подключения к концентратору уведомлений](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

