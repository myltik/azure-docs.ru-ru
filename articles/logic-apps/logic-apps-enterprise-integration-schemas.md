---
title: Схемы для проверки XML — Azure Logic Apps | Документация Майкрософт
description: Проверка XML-документов с помощью схем для Azure Logic Apps и пакета интеграции Enterprise
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: e23500ec3c16e66b8dc74fcba29e9b58f0b41790
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299068"
---
# <a name="validate-xml-with-schemas-for-azure-logic-apps-and-the-enterprise-integration-pack"></a>Проверка XML с помощью схем для Azure Logic Apps и пакета интеграции Enterprise

Схемы используются для подтверждения того, что получаемые документы XML являются допустимыми и содержат ожидаемые данные в предопределенном формате. С помощью схем также можно проверить сообщения, которыми стороны обмениваются в сценарии B2B.

## <a name="add-a-schema"></a>Добавление схемы

1. На портале Azure щелкните **Все службы**.

    ![Портал Azure, меню "Все службы"](media/logic-apps-enterprise-integration-schemas/overview-11.png)

2. Введите **интеграции** в поле фильтра поиска и выберите **Учетные записи интеграции** в списке результатов.

    ![Поле фильтра поиска](media/logic-apps-enterprise-integration-schemas/overview-21.png)

3. Выберите **учетную запись интеграции**, в которую необходимо добавить схему.

    ![Список учетных записей интеграции](media/logic-apps-enterprise-integration-schemas/overview-31.png)

4. Выберите плитку **Схемы**.

    ![Пример учетной записи интеграции, "Схемы"](media/logic-apps-enterprise-integration-schemas/schema-11.png)

### <a name="add-a-schema-file-smaller-than-2-mb"></a>Добавление файла схемы, размер которого меньше 2 МБ

1. В открывшейся колонке **Схемы** (см. выше) нажмите кнопку **Добавить**.

    ![Колонка "Схемы", "Добавить"](media/logic-apps-enterprise-integration-schemas/schema-21.png)

2. Введите имя для схемы. Чтобы передать файл схемы, выберите значок папки рядом с текстовым полем **Схемы**. После завершения передачи нажмите кнопку **ОК**.

    ![Снимок экрана: колонка "Добавление схемы" с выделенным элементом "Мелкий файл"](media/logic-apps-enterprise-integration-schemas/schema-31.png)

### <a name="add-a-schema-file-larger-than-2-mb-up-to-8-mb-maximum"></a>Добавление файла схемы, размер которого больше 2 МБ (максимум 8 МБ)

Эта процедура зависит от уровня доступа к контейнеру больших двоичных объектов: **Общедоступный** или **Без анонимного доступа**.

**Определение уровня доступа**

1.  Откройте **обозреватель хранилищ Azure**. 

2.  В разделе **Контейнеры BLOB-объектов** выберите нужный контейнер двоичных объектов. 

3.  Выберите последовательно **Безопасность**, **Уровень доступа**.

Если для контейнера больших двоичных объектов определен уровень доступа **Общедоступный**, выполните следующие действия.

![Обозреватель хранилищ Azure с выделенными элементами "Контейнеры больших двоичных объектов", "Безопасность" и "Общедоступный"](media/logic-apps-enterprise-integration-schemas/blob-public.png)

1. Передайте схему в учетную запись хранения и скопируйте универсальный код ресурса (URI).

    ![Учетная запись хранения с выделенным URI](media/logic-apps-enterprise-integration-schemas/schema-blob.png)

2. В колонке **Добавление схемы** выберите **Большой файл** и укажите универсальный код ресурса (URI) в текстовом поле **URI контента**.

    ![Страница "Схемы", на которой выделены кнопка "Добавить" и параметр "Большой файл"](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

Если для контейнера больших двоичных объектов определен уровень доступа **Без анонимного доступа**, выполните следующие действия.

![Обозреватель хранилищ Azure с выделенными элементами "Контейнеры больших двоичных объектов", "Безопасность" и "Без анонимного доступа"](media/logic-apps-enterprise-integration-schemas/blob-1.png)

1. Передайте схему в учетную запись хранения.

    ![Учетная запись хранения](media/logic-apps-enterprise-integration-schemas/blob-3.png)

2. Создайте подписанный URL-адрес для схемы.

    ![Учетная запись хранения с выделенной вкладкой подписанных URL-адресов](media/logic-apps-enterprise-integration-schemas/blob-2.png)

3. В колонке **Добавление схемы** выберите **Большой файл** и укажите URI подписанного URL-адреса в текстовом поле **URI содержимого**.

    ![Страница "Схемы", на которой выделены кнопка "Добавить" и параметр "Большой файл"](media/logic-apps-enterprise-integration-schemas/schema-largefile.png)

4. В колонке **Схемы** учетной записи интеграции теперь должна быть видна добавленная схема.

    ![Учетная запись интеграции с выделенными плиткой "Схемы" и новой схемой](media/logic-apps-enterprise-integration-schemas/schema-41.png)

## <a name="edit-schemas"></a>Изменение схем

1. Выберите плитку **Схемы**.

2. В открывшейся колонке **Схемы** выберите схему, которую вы хотите изменить.

3. В колонке **Схемы** нажмите кнопку **Изменить**.

    ![Колонка "Схемы"](media/logic-apps-enterprise-integration-schemas/edit-12.png)

4. Выберите файл схемы, который требуется изменить, а затем нажмите кнопку **открыть**.

    ![Открытие файла схемы для редактирования](media/logic-apps-enterprise-integration-schemas/edit-31.png)

Azure отображает сообщение, что схема успешно отправлена.

## <a name="delete-schemas"></a>Удаление схем

1. Выберите плитку **Схемы**.

2. В открывшейся колонке **Схемы** выберите схему, которую вы хотите удалить.

3. В колонке **Схемы** нажмите кнопку **Удалить**.

    ![Колонка "Схемы"](media/logic-apps-enterprise-integration-schemas/delete-12.png)

4. Нажмите кнопку **Да**, чтобы подтвердить удаление выбранной схемы.

    ![Сообщение с подтверждением "Удаление схемы"](media/logic-apps-enterprise-integration-schemas/delete-21.png)

    В колонке **Схемы** список схем обновляется, и удаленная схема больше не отображается.

    ![Учетная запись интеграции с выделенной плиткой "Схемы"](media/logic-apps-enterprise-integration-schemas/delete-31.png)

## <a name="next-steps"></a>Дополнительная информация
* [Узнайте больше о пакете интеграции Enterprise.](logic-apps-enterprise-integration-overview.md "Узнайте о пакете интеграции Enterprise").  

