---
title: "Обзор учетных записей интеграции и Пакета интеграции Enterprise | Документация Майкрософт"
description: "Узнайте все об учетных записях интеграции, пакете интеграции Enterprise и приложениях логики."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: ce2f01045195b9881bc1b3c337c453d10cd39540


---
# <a name="overview-of-integration-accounts"></a>Обзор учетных записей интеграции
## <a name="what-is-an-integration-account"></a>Что такое учетная запись интеграции?
Учетная запись интеграции — это учетная запись Azure, позволяющая приложениям интеграции Enterprise управлять артефактами, включая схемы, карты, сертификаты, партнеры и соглашения. Любому создаваемому вами приложению интеграции потребуется учетная запись интеграции, например, для доступа к схеме, карте или сертификату.

## <a name="create-an-integration-account"></a>Создание учетной записи интеграции
1. Щелкните **Обзор** .  
   ![](./media/logic-apps-enterprise-integration-accounts/account-1.png)  
2. Введите **integration** в поле фильтра поиска и выберите **Учетные записи интеграции** из списка результатов.     
   ![](./media/logic-apps-enterprise-integration-accounts/account-2.png)  
3. В меню в верхней части страницы нажмите кнопку *Добавить*.      
   ![](./media/logic-apps-enterprise-integration-accounts/account-3.png)  
4. В соответствующих полях введите **имя**, выберите **подписку**, которую необходимо использовать, создайте **группу ресурсов** или выберите имеющуюся, выберите **расположение**, в котором будет размещена учетная запись интеграции, выберите **ценовую категорию**, а затем нажмите кнопку **Создать**.   
   
   На этом этапе в выбранном расположении будет подготовлена учетная запись интеграции. На это потребуется не больше 1 минуты.    
   ![](./media/logic-apps-enterprise-integration-accounts/account-4.png)  
5. Обновите страницу. Вы увидите новую учетную запись интеграции в списке. Поздравляем!  
   ![](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

## <a name="how-to-link-an-integration-account-to-a-logic-app"></a>Как связать учетную запись интеграции с приложением логики
Чтобы предоставить приложению логики доступ к картам, схемам, соглашениям и другим артефактам, которые находятся в вашей учетной записи интеграции, необходимо сначала связать ее с этим приложением логики.

### <a name="here-are-the-steps-to-link-an-integration-account-to-a-logic-app"></a>Ниже приведены инструкции о том, как связать учетную запись интеграции с приложением логики.
#### <a name="prerequisites"></a>Предварительные требования
* Учетная запись интеграции.
* Приложение логики.

> [!NOTE]
> Прежде чем начать, убедитесь, что учетная запись интеграции и приложение логики находятся в **одном расположении Azure** .
> 
> 

1. Щелкните ссылку **Параметры** в меню приложения логики.  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)   
2. В колонке "Параметры" выберите **Учетная запись интеграции**.  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)   
3. Выберите учетную запись интеграции, которую необходимо связать с приложением логики, из раскрывающегося списка **Выберите учетную запись интеграции**.  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)   
4. Сохраните результаты своих действий.  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-4.png)   
5. Вы увидите уведомление о том, что учетная запись интеграции связана с вашим приложением логики, а все артефакты в вашей учетной записи интеграции доступны для этого приложения логики.  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)   

Теперь, когда учетная запись интеграции связана с приложением логики, можно перейти в приложение логики и использовать для создания приложений с функциями "бизнес-бизнес" такие соединители "бизнес-бизнес", как проверка XML, кодирование и декодирование неструктурированных файлов и преобразование.  

## <a name="how-to-delete-an-integration-account"></a>Как удалить учетную запись интеграции?
1. Щелкните **Обзор**.  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Введите **integration** в поле фильтра поиска и выберите **Учетные записи интеграции** из списка результатов.     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Выберите **учетную запись интеграции**, которую нужно удалить.  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Щелкните ссылку **Удалить**, которая находится в меню.   
   ![](./media/logic-apps-enterprise-integration-accounts/delete.png)  
5. Подтвердите свой выбор.    

## <a name="how-to-move-an-integration-account"></a>Как переместить учетную запись интеграции?
Можно легко переместить учетную запись интеграции в новую подписку и новую группу ресурсов. Если необходимо переместить учетную запись интеграции, выполните следующие действия.

> [!IMPORTANT]
> После перемещения учетной записи интеграции потребуется обновить все сценарии, чтобы в них использовались новые идентификаторы ресурсов.
> 
> 

1. Щелкните **Обзор**.  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Введите **integration** в поле фильтра поиска и выберите **Учетные записи интеграции** из списка результатов.     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Выберите **учетную запись интеграции**, которую нужно удалить.  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Щелкните ссылку **Переместить**, которая находится в меню.   
   ![](./media/logic-apps-enterprise-integration-accounts/move.png)  
5. Подтвердите свой выбор.    

## <a name="next-steps"></a>Дальнейшие действия
* [Узнайте о соглашениях и Пакете интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-agreements.md "Узнайте о соглашениях и Пакете интеграции Enterprise")  




<!--HONumber=Jan17_HO3-->


