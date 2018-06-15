---
title: Использование соединителя Slack в Azure Logic Apps | Документация Майкрософт
description: Из этой статьи вы узнаете, как в приложении логики подключиться к Slack.
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 88b134a90ac385ad957d76f420fe85dc2dbbf751
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296229"
---
# <a name="get-started-with-the-slack-connector"></a>Начало работы с соединителем Slack
Slack — это средство для организации взаимодействия между группами пользователей, которое объединяет все сообщения в одном месте, поддерживает возможности мгновенного поиска и доступно в любом месте. 

Для начала создайте приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-slack"></a>Создание подключения к Slack
Чтобы использовать соединитель Slack, сначала нужно создать **подключение** , а затем указать данные для приведенных ниже свойств. 

| Свойство | Обязательно | ОПИСАНИЕ |
| --- | --- | --- |
| по маркеру |Yes |Указание учетных данных Slack |

Войдите в Slack и настройте в приложении логики **подключение** к Slack, выполнив следующие действия:

1. Выберите **Повторение**
2. Выберите **частоту** и введите **интервал**.
3. Выберите **Добавить действие**.  
   ![Настройка Slack][1]  
4. В поле поиска введите "Slack" и дождитесь возвращения всех записей с "Slack" в имени.
5. Выберите **Slack — опубликовать сообщение**
6. Выберите **Вход в Slack**.  
   ![Настройка Slack][2]
7. Укажите учетные данные Slack, чтобы войти и авторизовать приложение.    
   ![Настройка Slack][3]  
8. Вы будете перенаправлены на страницу входа своей организации. **Разрешите** Slack взаимодействие с вашим приложением логики.      
   ![Настройка Slack][5] 
9. После успешной аутентификации вы перейдете к своему приложению логики, чтобы завершить его, настроив параметры в разделе **Slack —получение всех сообщений**. Добавьте другие необходимые вам триггеры и действия.  
   ![Настройка Slack][6]
10. Сохраните результаты работы, выбрав **Сохранить** в строке меню (в верхней части окна).

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/slack/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
