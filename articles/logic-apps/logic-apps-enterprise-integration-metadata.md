---
title: "Метаданные учетной записи интеграции Azure Logic Apps | Документация Майкрософт"
description: "Обзор метаданных учетной записи интеграции."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: e5d567800d00b41ec0782216b442f437a2500928
ms.openlocfilehash: 41edd713d85790793341e100f77300f999ac8c73


---
# <a name="azure-logic-apps-integration-account-metadata"></a>Метаданные учетной записи интеграции Azure Logic Apps

## <a name="overview"></a>Обзор

Партнеры, соглашения, схемы и карты, добавляемые в учетную запись интеграции, содержат метаданные с парами "ключ-значение". Можно определить пользовательские метаданные, которые могут быть получены во время выполнения.  Сейчас артефакты не поддерживают возможность создания метаданных в пользовательском интерфейсе. Для их создания можно использовать REST API.  Для партнеров, соглашений и схем доступно действие **EDIT as JSON** (Изменить как JSON) и возможно добавление ключей в метаданные.  В приложении логики для получения сведений о метаданных можно использовать функцию **Integration Account Artifact LookUp**.

## <a name="how-to-store-metadata"></a>Как хранить метаданные

1. Создайте [учетную запись интеграции](logic-apps-enterprise-integration-create-integration-account.md).   

2. Добавьте в нее [партнера](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner), [соглашение](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements) или [схему](logic-apps-enterprise-integration-schemas.md).

3. Выберите партнера, соглашение или схему. Выберите **Edit as JSON** (Изменить как JSON) и введите сведения о метаданных.    
![Ввод метаданных](media/logic-apps-enterprise-integration-metadata/image1.png)  

## <a name="call-integration-account-artifact-lookup-from-a-logic-app"></a>Вызовите из приложения логики функцию **Integration Account Artifact LookUp**.

1. Создайте [приложение логики](logic-apps-create-a-logic-app.md).

2. [Свяжите](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app) это приложение логики с учетной записью интеграции.    

3. Прежде чем искать компонент **Integration Account Artifact LookUp**, создайте триггер, например, с использованием *Request* или *HTTP*.  Введите в поле поиска **integration**, чтобы найти компонент **Integration Account Artifact LookUp**.  
![Поиск](media/logic-apps-enterprise-integration-metadata/image2.png)

3. Выберите **Integration Account Artifact LookUp**.  

4. Выберите **типа артефакта** и укажите **имя артефакта**.  
![Поиск](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="an-example-to-retrieve-partner-metadata"></a>Пример для получения метаданных партнера

1. Метаданные партнера содержат сведения о URL-адресе маршрутизации.    
![Поиск](media/logic-apps-enterprise-integration-metadata/image6.png)

2. В приложении логики настройте **Integration Account Artifact LookUp** и **HTTP** .  
![Поиск](media/logic-apps-enterprise-integration-metadata/image4.png)

3. Код для получения универсального кода ресурса (URI) должен выглядеть следующим образом.    
![Поиск](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>Дальнейшие действия
* [Узнайте о соглашениях и Пакете интеграции Enterprise](logic-apps-enterprise-integration-agreements.md "Узнайте о соглашениях и Пакете интеграции Enterprise")  



<!--HONumber=Feb17_HO1-->


