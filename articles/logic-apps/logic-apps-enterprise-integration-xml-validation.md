---
title: "Обзор проверки данных XML и Пакета интеграции Enterprise | Документация Майкрософт"
description: "Узнайте, как функционирует проверка в пакете интеграции Enterprise и приложениях логики."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 967d06e3df21aa1c194cdc81fb7cd0bdf53e82e4


---
# <a name="enterprise-integration-with-xml-validation"></a>Интеграция Enterprise с проверкой данных XML
## <a name="overview"></a>Обзор
Часто в сценариях "бизнес-бизнес" партнерам по соглашению необходимо проверять допустимость сообщений, которыми они обмениваются между собой, перед началом обработки данных. В пакете интеграции Enterprise можно использовать соединитель проверки XML, чтобы проверять документы с помощью предопределенной схемы.  

## <a name="how-to-validate-a-document-with-the-xml-validation-connector"></a>Как проверить документ с помощью соединителя проверки XML
1. Создайте приложение логики и [свяжите его с учетной записью интеграции](../logic-apps/logic-apps-enterprise-integration-accounts.md "Узнайте, как связать учетную запись интеграции с приложением логики"), содержащей схему, которая будет использоваться для проверки данных XML.
2. Добавьте триггер **Request - When an HTTP request is received** (Запрос: при получении HTTP-запроса) в свое приложение логики.  
   ![](./media/logic-apps-enterprise-integration-xml/xml-1.png)    
3. Добавьте действие **Проверка XML**, щелкнув **Добавить действие**.  
4. Введите слово *xml* в поле поиска, чтобы отфильтровать все действия и оставить только те, которые вы хотите использовать. 
5. Выберите действие **Проверка XML**   .  
   ![](./media/logic-apps-enterprise-integration-xml/xml-2.png)   
6. Выберите текстовое поле **Содержимое**.  
   ![](./media/logic-apps-enterprise-integration-xml/xml-1-5.png)
7. Выберите тег body в качестве содержимого, которое следует проверять.   
   ![](./media/logic-apps-enterprise-integration-xml/xml-3.png)  
8. Выберите поле списка **Имя схемы** и выберите схему, которую вы хотите использовать для проверки входного *содержимого*, указанного выше.     
   ![](./media/logic-apps-enterprise-integration-xml/xml-4.png) 
9. Сохраните результаты своих действий.  
   ![](./media/logic-apps-enterprise-integration-xml/xml-5.png) 

На этом настройка соединителя проверки завершена. В случае реального приложения может потребоваться сохранить проверенные данные в бизнес-приложении, например Salesforce. Можно легко добавить действие для отправки выходных данных проверки в Salesforce. 

Теперь можно протестировать действие проверки, выполнив запрос к конечной точке HTTP.  

## <a name="next-steps"></a>Дальнейшие действия
[Обзор пакета интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise")   




<!--HONumber=Jan17_HO3-->


