---
title: Управление метаданными артефактов учетной записи интеграции в Azure Logic Apps | Документация Майкрософт
description: Узнайте, как добавлять и извлекать метаданные артефактов учетных записей интеграции для Azure Logic Apps.
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/23/2018
ms.author: LADocs; padmavc
ms.openlocfilehash: 3e7ef6aef9bc1062ae0f76adfbaf086961fcaa94
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298371"
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>Управление метаданными артефактов в учетных записях интеграции для приложений логики

Вы можете определять пользовательские метаданные для артефактов в учетных записях интеграции и извлекать эти метаданные в среде выполнения приложений логики. Например, можно указать метаданные для артефактов (партнеры, соглашения, схемы, сопоставления и др.) и сохранить все метаданные с помощью пар "ключ — значение". 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Добавление метаданных в артефакты учетных записей интеграции

1. На портале Azure создайте [учетную запись интеграции](logic-apps-enterprise-integration-create-integration-account.md).

2. Добавьте в учетную запись интеграции артефакт, такой как [партнер](logic-apps-enterprise-integration-partners.md), [соглашение](logic-apps-enterprise-integration-agreements.md) или [схема](logic-apps-enterprise-integration-schemas.md).

3. Выберите артефакт, затем **Редактирование** и введите сведения о метаданных.

   ![Ввод метаданных](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Извлечение метаданных из артефактов для приложений логики

1. На [портале Azure](quickstart-create-first-logic-app-workflow.md) создайте приложение логики.

2. Затем [свяжите приложение логики с учетной записью интеграции](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

3. В конструкторе приложений логики добавьте в свое приложение логики триггер (**Запрос** или **HTTP**).

4. В разделе триггера выберите **Новый шаг** > **Добавить действие**. Выполните поиск по запросу "учетная запись интеграции", чтобы найти и выбрать действие **Учетная запись интеграции > Поиск артефакта учетной записи интеграции**.

   ![Выбор функции "Поиск артефакта учетной записи интеграции"](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Выберите **Типа артефакта** и укажите **Имя артефакта**. Например: 

   ![Выбор типа артефакта и указание имени артефакта](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Пример: извлечение метаданных партнера

Предположим, метаданные этого партнера содержат сведения о `routingUrl`:

![Поиск в метаданных партнера сведений о routingURL](media/logic-apps-enterprise-integration-metadata/image6.png)

1. В приложении логики добавьте триггер, действие **Учетная запись интеграции > Поиск артефакта учетной записи интеграции** для партнера, а затем — действие **HTTP**.

   ![Добавление в приложение логики триггера, поиска артефакта и действия HTTP](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Чтобы получить URI, на панели инструментов конструктора приложений логики выберите **Представление кода** для приложения логики. Определение приложения логики должно выглядеть примерно как в этом примере:

   ![Поиск](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>Дополнительная информация

* [Дополнительные сведения о соглашениях](logic-apps-enterprise-integration-agreements.md).
