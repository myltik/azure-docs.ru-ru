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
ms.sourcegitcommit: 4faf01ca10f263a5ecc18f51659b5004bf4f7c36
ms.openlocfilehash: 47a081e04c30f6d2c6c5e92cf82085e2d56478cf


---
# <a name="azure-logic-apps-integration-account-metadata"></a>Метаданные учетной записи интеграции Azure Logic Apps 

## <a name="overview"></a>Обзор

Партнеры, соглашения, схемы и карты, добавляемые в учетную запись интеграции, содержат метаданные с парами "ключ-значение". Можно определить пользовательские метаданные, которые могут быть получены во время выполнения.  Сейчас артефакты не поддерживают возможность создания метаданных в пользовательском интерфейсе. Для их создания можно использовать REST API.  Для партнеров, соглашений и схем доступно действие **EDIT as JSON** (Изменить как JSON) и возможно добавление ключей в метаданные.  В приложении логики для получения сведений о метаданных можно использовать функцию **Integration Account Artifact LookUp**.

## <a name="how-to-store-metadata"></a>Как хранить метаданные 

1. Создайте [учетную запись интеграции](app-service-logic-enterprise-integration-create-integration-account.md).   

2. Добавьте в нее [партнера](app-service-logic-enterprise-integration-partners.md#how-to-create-a-partner), [соглашение](app-service-logic-enterprise-integration-agreements.md#how-to-create-agreements) или [схему](app-service-logic-enterprise-integration-schemas.md).

3. Выберите партнера, соглашение или схему. Выберите **Edit as JSON** (Изменить как JSON) и введите сведения о метаданных.    
![Ввод метаданных](./media/app-service-logic-enterprise-integration-metadata/image1.png)  

## <a name="call-integration-account-artifact-lookup-from-a-logic-app"></a>Вызовите из приложения логики функцию **Integration Account Artifact LookUp**.

1. Создайте [приложение логики](app-service-logic-create-a-logic-app.md).

2. [Свяжите](app-service-logic-enterprise-integration-create-integration-account.md#how-to-link-an-integration-account-to-a-logic-app) это приложение логики с учетной записью интеграции.    

3. Прежде чем искать компонент **Integration Account Artifact LookUp**, создайте триггер, например, с использованием *Request* или *HTTP*.  Введите в поле поиска **integration**, чтобы найти компонент **Integration Account Artifact LookUp** 
![Поиск](./media/app-service-logic-enterprise-integration-metadata/image2.png). 

3. Выберите **Integration Account Artifact LookUp**.  

4. Выберите **типа артефакта** и укажите **имя артефакта**.  
![Поиск](./media/app-service-logic-enterprise-integration-metadata/image3.png)

## <a name="an-example-to-retrieve-partner-metadata"></a>Пример для получения метаданных партнера 

1. Метаданные партнера содержат сведения о URL-адресе маршрутизации.    
![Поиск](./media/app-service-logic-enterprise-integration-metadata/image6.png)

2. В приложении логики настройте **Integration Account Artifact LookUp** и **HTTP** .  
![Поиск](./media/app-service-logic-enterprise-integration-metadata/image4.png)

3. Код для получения универсального кода ресурса (URI) должен выглядеть следующим образом.    
![Поиск](./media/app-service-logic-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>Дальнейшие действия
* [Узнайте о соглашениях и Пакете интеграции Enterprise](app-service-logic-enterprise-integration-agreements.md "Узнайте о соглашениях и Пакете интеграции Enterprise")  


<!--HONumber=Nov16_HO4-->


