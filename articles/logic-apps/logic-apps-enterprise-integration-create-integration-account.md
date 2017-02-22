---
title: "Создание, привязка, удаление или перемещение учетной записи интеграции в Azure Logic Apps | Документация Майкрософт"
description: "Узнайте, как создать учетную запись интеграции и связать ее с приложениями логики."
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: dca762854f22721de76f69cbc28c0377d563fe28
ms.openlocfilehash: e26271a1291fc76e3ea8f93a7aa9ab8c811ff604


---

# <a name="what-is-an-integration-account"></a>Что такое учетная запись интеграции?
Учетная запись интеграции позволяет приложениям интеграции Enterprise управлять артефактами, включая схемы, карты, сертификаты, партнеры и соглашения. Любое создаваемое приложение интеграции использует учетную запись интеграции для доступа к схемам, картам, сертификатам и т. д.

## <a name="create-an-integration-account"></a>Создание учетной записи интеграции
1. Щелкните **Обзор**:   
   ![](./media/logic-apps-enterprise-integration-accounts/account-1.png)  
2. Введите **интеграции** в поле фильтра поиска и выберите **Учетные записи интеграции** в списке результатов:     
   ![](./media/logic-apps-enterprise-integration-accounts/account-2.png)  
3. В меню в верхней части страницы нажмите кнопку *Добавить*:      
   ![](./media/logic-apps-enterprise-integration-accounts/account-3.png)  
4. В соответствующих полях введите **имя**, выберите **подписку**, которую необходимо использовать, создайте **группу ресурсов** или выберите имеющуюся, выберите **расположение**, в котором будет размещена учетная запись интеграции, выберите **ценовую категорию**, а затем нажмите кнопку **Создать**.   
   
   На этом этапе в выбранном расположении будет подготовлена учетная запись интеграции. На это потребуется не больше 1 минуты.    
   ![](./media/logic-apps-enterprise-integration-accounts/account-4.png)  
5. Обновите страницу. Вы увидите новую учетную запись интеграции в списке:  
   ![](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

Теперь свяжите только что созданную учетную запись интеграции с приложением логики. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Привязка учетной записи интеграции к приложению логики
Чтобы предоставить приложению логики доступ к картам, схемам, соглашениям и другим артефактам в учетной записи интеграции, свяжите ее с приложением логики.

#### <a name="prereqs"></a>Предварительные требования
* Учетная запись интеграции.
* Приложение логики

> [!NOTE] 
> Прежде чем начать, убедитесь, что учетная запись интеграции и приложение логики находятся в **одном расположении Azure**.

1. Щелкните ссылку **Параметры** в меню приложения логики:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)   
2. В колонке "Параметры" выберите **Учетная запись интеграции**:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)   
3. Выберите учетную запись интеграции, которую необходимо связать с приложением логики, из раскрывающегося списка **Выберите учетную запись интеграции**:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)   
4. Сохраните результаты своих действий:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-4.png)   
5. Вы увидите уведомление о том, что учетная запись интеграции связана с вашим приложением логики, а все артефакты в вашей учетной записи интеграции доступны для этого приложения логики:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)   

Теперь, когда ваша учетная запись интеграции связана с приложением логики, вы можете использовать соединители "бизнес — бизнес" (B2B), доступные в приложениях логики. Некоторые распространенные соединители B2B включают в себя проверку XML, кодирование и декодирование неструктурированных файлов.  

## <a name="how-to-delete-an-integration-account"></a>Как удалить учетную запись интеграции?
1. Щелкните **Обзор**:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Введите **интеграции** в поле фильтра поиска и выберите **Учетные записи интеграции** в списке результатов:     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Выберите **учетную запись интеграции**, которую нужно удалить:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Щелкните ссылку **Удалить**, которая находится в меню:   
   ![](./media/logic-apps-enterprise-integration-accounts/delete.png)  
5. Подтвердите свой выбор.    

## <a name="how-to-move-an-integration-account"></a>Как переместить учетную запись интеграции?
Можно легко переместить учетную запись интеграции в новую подписку и новую группу ресурсов. Если необходимо переместить учетную запись интеграции, выполните следующие действия.

> [!IMPORTANT]
> После перемещения учетной записи интеграции необходимо обновить все сценарии, чтобы в них использовались новые идентификаторы ресурсов.

1. Щелкните **Обзор**:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Введите **интеграции** в поле фильтра поиска и выберите **Учетные записи интеграции** в списке результатов:     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Выберите **учетную запись интеграции**, которую нужно удалить:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Щелкните ссылку **Переместить**, которая находится в меню:   
   ![](./media/logic-apps-enterprise-integration-accounts/move.png)  
5. Подтвердите свой выбор.    

## <a name="next-steps"></a>Дальнейшие действия
* [Узнайте о соглашениях и Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-agreements.md "Узнайте о соглашениях и Пакете интеграции Enterprise")  




<!--HONumber=Jan17_HO5-->


