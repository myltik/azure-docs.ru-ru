---
title: "Управление метаданными артефактов учетной записи интеграции в Azure Logic Apps | Документация Майкрософт"
description: "Узнайте, как добавлять и извлекать метаданные артефактов учетных записей интеграции для Azure Logic Apps."
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
ms.custom: H1Hack27Feb2017
ms.date: 11/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: ca8537a7e35e44efafb9b0a9d6ddefe069067475
ms.openlocfilehash: de55bf7d9e68146745c90b96f280c8a71e27b2f9
ms.lasthandoff: 03/02/2017

---

# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>Управление метаданными артефактов в учетных записях интеграции для приложений логики

Вы можете определять пользовательские метаданные для артефактов в учетных записях интеграции и извлекать эти метаданные в среде выполнения приложений логики. Например, можно указать метаданные для артефактов (партнеры, соглашения, схемы, сопоставления и др.) и сохранить все метаданные с помощью пар "ключ — значение". В настоящее время артефакты не могут создавать метаданные через пользовательский интерфейс, но вы можете использовать для этого интерфейсы REST API. Чтобы добавить метаданные при создании или выборе партнера, соглашения или схемы на портале Azure, выберите **Редактирование в качестве JSON**. Чтобы извлечь метаданные артефактов в приложениях логики, можно использовать функцию "Поиск артефакта учетной записи интеграции".

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Добавление метаданных в артефакты учетных записей интеграции

1. Создайте [учетную запись интеграции](logic-apps-enterprise-integration-create-integration-account.md).

2. Добавьте в учетную запись интеграции артефакт, такой как [партнер](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner), [соглашение](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements) или [схема](logic-apps-enterprise-integration-schemas.md).

3.    Выберите артефакт, затем выберите **Редактирование в качестве JSON** и введите сведения о метаданных.

    ![Ввод метаданных](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Извлечение метаданных из артефактов для приложений логики

1. Создайте [приложение логики](logic-apps-create-a-logic-app.md).

2. Затем [свяжите приложение логики с учетной записью интеграции](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app). 

3. В конструкторе приложений логики добавьте в свое приложение логики триггер (*Запрос* или *HTTP*).

4.    Выберите **Следующий шаг** > **Добавить действие**. Выполните поиск по слову *интеграции*, чтобы найти и выбрать **Учетная запись интеграции > Поиск артефакта учетной записи интеграции**.

    ![Выбор функции "Поиск артефакта учетной записи интеграции"](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Выберите **Типа артефакта** и укажите **Имя артефакта**.

    ![Выбор типа артефакта и указание имени артефакта](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Пример: извлечение метаданных партнера

Метаданные партнера содержат такие сведения о `routingUrl`:

![Поиск в метаданных партнера сведений о routingURL](media/logic-apps-enterprise-integration-metadata/image6.png)

1. В приложении логики добавьте триггер, действие **Учетная запись интеграции > Поиск артефакта учетной записи интеграции** для партнера, а затем — **HTTP**.

    ![Добавление в приложение логики триггера, поиска артефакта и HTTP](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Чтобы получить универсальный код ресурса (URI), перейдите в представление кода своего приложения логики. Определение приложения логики должно выглядеть примерно как в этом примере:

    ![Поиск](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>Дальнейшие действия
* [Узнайте о соглашениях и Пакете интеграции Enterprise](logic-apps-enterprise-integration-agreements.md "Узнайте о соглашениях и Пакете интеграции Enterprise")  

