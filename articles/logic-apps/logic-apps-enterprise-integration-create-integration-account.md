---
title: Создание и администрирование учетных записей интеграции для решений B2B в Azure Logic Apps | Документация Майкрософт
description: Создание, привязка, перемещение и удаление учетных записей интеграции для корпоративной интеграции и решений B2B в Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: 2a1fe501386884e02657d4b6cbef58ffc533fa33
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297994"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>Создание и администрирование учетных записей интеграции для решений B2B в приложениях логики

Прежде чем создавать [корпоративную интеграцию и решения B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) в [Azure Logic Apps](../logic-apps/logic-apps-overview.md), необходимо создать учетную запись интеграции, с помощью которой вы будете создавать и хранить такие артефакты B2B, как торговые партнеры, соглашения, карты, схемы, сертификаты и т. д., и сможете управлять ими. Чтобы приложение логики могло работать с артефактами в вашей учетной записи интеграции и использовать такие соединители Logic Apps B2B, как проверка XML, необходимо [связать учетную запись интеграции](#link-account) с приложением логики. Для этого учетная запись интеграции и приложение логики должны находиться в *одном* регионе или расположении Azure.

В этой статье показано, как выполнять следующие задачи:

* создание учетной записи интеграции;
* привязка учетной записи интеграции к приложению логики;
* перемещение учетной записи интеграции в другую подписку или группу ресурсов Azure;
* удаление учетной записи интеграции.

Если у вас еще нет подписки Azure, <a href="https://azure.microsoft.com/free/" target="_blank">зарегистрируйтесь для получения бесплатной учетной записи Azure</a>.

## <a name="sign-in-to-the-azure-portal"></a>Выполните вход на портал Azure.

Войдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a> с помощью учетных данных учетной записи Azure.

## <a name="create-integration-account"></a>Создание учетной записи интеграции

1. В главном меню Azure выберите **Все службы**. В поле поиска в качестве фильтра введите текст "учетные записи интеграции" и выберите элемент **Учетные записи интеграции**.

   ![Поиск учетных записей интеграции](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. В разделе **Учетные записи интеграции** нажмите кнопку **Добавить**.

   ![Выбор элемента "Добавить" для создания учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Укажите сведения об учетной записи интеграции. 

   ![Ввод сведений для учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Свойство | Обязательно | Пример значения | ОПИСАНИЕ | 
   |----------|----------|---------------|-------------|
   | ИМЯ | Yes | test-integration-account | Имя вашей учетной записи интеграции. В этом примере используйте указанное имя. | 
   | Подписка | Yes | <*Azure-subscription-name*> | Имя подписки Azure, которую нужно использовать. | 
   | Группа ресурсов | Yes | test-integration-account-rg | Имя [группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md), используемой для упорядочения связанных ресурсов. В этом примере создайте группу ресурсов с указанным именем. | 
   | Ценовая категория | Yes | Free | Ценовая категория, которую необходимо использовать. В этом примере выберите вариант **Free** (Бесплатный). Дополнительные сведения см. в разделе [Ограничения и настройка Logic Apps](../logic-apps/logic-apps-limits-and-config.md) и на странице с [ценами на приложения логики](https://azure.microsoft.com/pricing/details/logic-apps/). | 
   | Расположение | Yes | Запад США | Регион для хранения сведений вашей учетной записи интеграции. Выберите то же расположение, что и для приложения логики, или создайте приложение логики в том же расположении, что и учетную запись интеграции. | 
   | Log Analytics | Нет  | Отключить | Сохраните параметр **Выкл.** для журнала ведения диагностики. | 
   ||||| 

4. По завершении установите флажок **Закрепить на панели мониторинга** и нажмите кнопку **Создать**.

   Когда Azure развернет вашу учетную запись интеграции в выбранном расположении (на это обычно уходит не более минуты), она откроется в Azure.

   ![Открытие учетной записи интеграции в Azure](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

Теперь, прежде чем приложение логики сможет использовать учетную запись интеграции, необходимо связать учетную запись интеграции с приложением логики.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Привязка к приложению логики

Чтобы предоставить приложениям логики доступ к учетной записи интеграции, содержащей такие артефакты B2B, как торговые партнеры, соглашения, карты и схемы, необходимо связать ее с приложением логики. 

> [!NOTE]
> Учетная запись интеграции и приложение логики должны находиться в одном регионе.

1. На портале Azure найдите и откройте нужное приложение логики.

2. В меню приложения логики в разделе **Параметры** выберите **Параметры рабочего процесса**. Выберите учетную запись интеграции, которую необходимо связать с приложением логики, из списка **Выберите учетную запись интеграции**.

   ![Выбор учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. Чтобы завершить связывание, нажмите кнопку **Сохранить**.

   ![Выбор учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   После успешной привязки учетной записи интеграции к приложению логики в Azure отобразится подтверждение. 

   ![Подтверждение успешной привязки в Azure](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

Теперь приложение логики может использовать любые или все артефакты, содержащиеся в учетной записи интеграции, а также соединители B2B, например проверку XML и кодирование и декодирование неструктурированного файла.  

## <a name="unlink-from-logic-app"></a>Удаление привязки к приложению логики

Чтобы связать приложение логики с другой учетной записью интеграции или прекратить использование учетной записи интеграции в приложении логики, можно удалить привязку через обозреватель ресурсов Azure.

1. В браузере перейдите в <a href="https://resources.azure.com" target="_blank">обозреватель ресурсов Azure (https://resources.azure.com)</a>. Убедитесь, что вы вошли с помощью тех же учетных данных Azure.

   ![Обозреватель ресурсов Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. В поле поиска введите имя приложения логики, а затем найдите и выберите нужное приложение логики.

   ![Поиск и выбор приложения логики](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. В строке заголовка обозревателя выберите **Чтение и запись**.

   ![Включение режима "Чтение и запись"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. На вкладке **Данные** нажмите кнопку **Редактировать**.

   ![Выбор кнопки "Редактировать" на вкладке "Данные"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. В редакторе найдите свойство `integrationAccount` учетной записи интеграции и удалите свойство, указанное в таком формате:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Например: 

   ![Поиск определения свойства integrationAccount](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. На вкладке **Данные** нажмите кнопку **Отправить**, чтобы сохранить изменения. 

   ![Выбор кнопки "Отправить", чтобы сохранить изменения](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. На портале Azure в разделе **Параметры рабочего процесса** приложения логики убедитесь, что свойство **Учетная запись интеграции** теперь отображается пустым.

   ![Проверка на предмет отсутствия привязки у учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Перемещение учетной записи интеграции

Вы можете переместить учетную запись интеграции в другую подписку или группу ресурсов Azure.

1. В главном меню Azure выберите **Все службы**. В поле поиска в качестве фильтра введите текст "учетные записи интеграции" и выберите элемент **Учетные записи интеграции**.

   ![Поиск учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. В разделе **Учетные записи интеграции** выберите учетную запись интеграции, которую необходимо переместить. В меню учетной записи интеграции в разделе **Параметры** выберите пункт **Свойства**.

   ![Выбор элемента "Свойства" в разделе "Параметры"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Измените группу ресурсов или подписку Azure для учетной записи интеграции.

   ![Выбор параметра "Изменить группу ресурсов" или "Изменить подписку"](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. По завершении убедитесь, что в некоторых или во всех скриптах используются новые идентификаторы ресурсов для ваших артефактов.  

## <a name="delete-integration-account"></a>Удаление учетной записи интеграции

1. В главном меню Azure выберите **Все службы**. В поле поиска в качестве фильтра введите текст "учетные записи интеграции" и выберите элемент **Учетные записи интеграции**.

   ![Поиск учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. В разделе **Учетные записи интеграции** выберите учетную запись интеграции, которую необходимо удалить. В меню учетной записи интеграции выберите **Обзор**, а затем нажмите **Удалить**. 

   ![Выберите учетную запись интеграции. Выбор элемента "Удалить" на странице "Обзор"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. Чтобы подтвердить удаление учетной записи интеграции, выберите **Да**.

   ![Выбор кнопки "Да" для подтверждения удаления](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Дополнительная информация

* [Создание торговых партнеров](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Создание соглашений](../logic-apps/logic-apps-enterprise-integration-agreements.md)
