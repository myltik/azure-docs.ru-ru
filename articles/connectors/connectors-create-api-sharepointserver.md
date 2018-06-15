---
title: Использование соединителя SharePoint Server в приложениях логики | Документация Майкрософт
description: Как приступить к использованию соединителя SharePoint Server в приложениях логики.
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: c153b4987e37a5d97b95d4f1249de1ed92e851b3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295950"
---
# <a name="get-started-with-the-sharepoint-connector"></a>Приступая к работе с соединителем SharePoint
Подключение SharePoint позволяет работать со списками в SharePoint.

Для начала создайте приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sharepoint"></a>Создание подключения к SharePoint
Чтобы использовать соединитель SharePoint, сначала нужно создать **подключение**, а затем указать данные для следующих свойств: 

| Свойство | Обязательно | ОПИСАНИЕ |
| --- | --- | --- |
| по маркеру |Yes |Указание учетных данных SharePoint |

Для подключения к **SharePoint** необходимо предоставить свое удостоверение (имя пользователя и пароль, учетные данные смарт-карты и т. п.). После успешной аутентификации вы сможете использовать соединитель SharePoint в приложении логики. 

Чтобы войти в систему для создания **подключения**, которое будет использоваться в вашем приложении логики, в конструкторе приложения логики сделайте следующее:

1. В поле поиска введите SharePoint и дождитесь возвращения всех записей с текстом SharePoint в имени.   
   ![Настройка SharePoint][1]  
2. Выберите **SharePoint - When a file is created** (SharePoint — при создании файла).   
3. Выберите **Sign in to SharePoint** (Вход в SharePoint).   
   ![Настройка SharePoint][2]    
4. Укажите учетные данные SharePoint, чтобы войти и пройти аутентификацию в SharePoint.   
   ![Настройка SharePoint][3]     
5. После успешной аутентификации вы перенаправляетесь к своему приложению логики, чтобы завершить его настройку, задав параметры в диалоговом окне SharePoint **When a file is created** (При создании файла).          
   ![Настройка SharePoint][4]  
6. Затем можно будет добавить другие триггеры и действия, необходимые для завершения приложения логики.   
7. Сохраните результаты работы, выбрав **Сохранить** в строке меню (в верхней части окна).

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/sharepoint/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
