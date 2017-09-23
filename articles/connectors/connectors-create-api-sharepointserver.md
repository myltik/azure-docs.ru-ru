---
title: "Использование соединителя SharePoint Server в приложениях логики | Документация Майкрософт"
description: "Как приступить к использованию соединителя SharePoint Server в приложениях логики."
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 0f3274816e279a1aa57febaa2f8294914900799a
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017

---
# <a name="get-started-with-the-sharepoint-connector"></a>Приступая к работе с соединителем SharePoint
Подключение SharePoint позволяет работать со списками в SharePoint.

Для начала создайте приложение логики, как описано [здесь](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="create-a-connection-to-sharepoint"></a>Создание подключения к SharePoint
Чтобы использовать соединитель SharePoint, сначала нужно создать **подключение** , а затем указать данные для следующих свойств: 

| Свойство | Обязательно | Description (Описание) |
| --- | --- | --- |
| Маркер |Да |Указание учетных данных SharePoint |

Для подключения к **SharePoint** необходимо предоставить свое удостоверение (имя пользователя и пароль, учетные данные смарт-карты и т. п.). После прохождения проверки подлинности вы сможете использовать соединитель SharePoint в приложении логики. 

В конструкторе приложения логики выполните следующие действия, чтобы войти в SharePoint для создания подключения **connection** , которое будет использоваться в приложении логики.

1. В поле поиска введите SharePoint и дождитесь возвращения всех записей с текстом SharePoint в имени.   
   ![Настройка SharePoint][1]  
2. Выберите **SharePoint - When a file is created** (SharePoint — при создании файла).   
3. Выберите **Sign in to SharePoint** (Вход в SharePoint).   
   ![Настройка SharePoint][2]    
4. Укажите учетные данные SharePoint, чтобы войти и пройти аутентификацию в SharePoint.   
   ![Настройка SharePoint][3]     
5. После успешной аутентификации вы перейдете к своему приложению логики, чтобы завершить его, настроив параметры в диалоговом окне **При создании файла** SharePoint.          
   ![Настройка SharePoint][4]  
6. Затем можно будет добавить другие триггеры и действия, необходимые для завершения приложения логики.   
7. Сохраните результаты работы, выбрав **Сохранить** в строке меню вверху.  

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/sharepoint/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png

