---
title: "Создание учетной записи DocumentDB для использования с приложениями MongoDB | Документация Майкрософт"
description: "В этом руководстве вы узнаете, как создать учетную запись DocumentDB с поддержкой протокола MongoDB, доступную в предварительной версии."
keywords: "приложения mongodb, приложение mongodb"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 413f3ae1-a68e-43be-b0d7-fa2987644f3e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 1c9e8d5a00445b31c81547df7bd2dbc55f8feac2
ms.openlocfilehash: e9176ec520b05e5613eab610fbc96144e9405d07
ms.lasthandoff: 02/27/2017


---

# <a name="create-a-documentdb-account-for-use-with-mongodb-apps"></a>Создание учетной записи DocumentDB для использования с приложениями MongoDB
Теперь базы данных DocumentDB можно использовать как хранилище данных для приложений, написанных для MongoDB. Чтобы использовать эту возможность, требуются учетные записи Azure и DocumentDB. В этом руководстве описано, как создать учетную запись DocumentDB для использования с приложениями MongoDB. 

Учетную запись DocumentDB с поддержкой учетной записи MongoDB можно создать с помощью портала Azure или интерфейса командной строки Azure с шаблонами Azure Resource Manager. Из этой статьи вы узнаете, как создать учетную запись DocumentDB с поддержкой учетной записи MongoDB с помощью портала Azure. Сведения о том, как создать учетную запись с помощью интерфейса командной строки Azure с Azure Resource Manager, см. в статье [Автоматизация управления учетными записями Azure DocumentDB с помощью Azure CLI 2.0](documentdb-automation-resource-manager-cli.md).

## <a name="prerequisite"></a>Предварительные требования
Учетная запись Azure. Если у вас нет учетной записи, вы можете создать [бесплатную учетную запись Azure](https://azure.microsoft.com/free/).
## <a name="create-a-documentdb-account"></a>Создание учетной записи DocumentDB

1. В браузере войдите на [портал Azure](https://portal.azure.com).
2. В левой области навигации щелкните **NoSQL (DocumentDB)**.

    ![Снимок экрана левой области навигации портала с выделенной записью DocumentDB NoSQL](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. Можно также щелкнуть **Больше служб** и ввести **DocumentDB** в верхней строке поиска, а затем щелкнуть **NoSQL (DocumentDB)**.

    ![Снимок экрана поиска записи DocumentDB NoSQL в колонке "Больше служб"](./media/documentdb-create-mongodb-account/more-services-search.PNG)

4. В верхней части колонки **NoSQL (DocumentDB)** щелкните **+ Добавить** в верхней панели действий.

    ![Снимок экрана кнопки "Добавить" в колонке ресурсов DocumentDB NoSQL](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. В колонке **Учетная запись DocumentDB** укажите желаемую конфигурацию учетной записи.

   ![Снимок экрана колонки создания учетной записи DocumentDB с поддержкой протокола MongoDB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.PNG)

    - В поле **Идентификатор** введите имя для идентификации учетной записи.  После успешной проверки **идентификатора** в поле **Идентификатор** отображается зеленая галочка. Значение **идентификатора** становится именем узла в универсальном коде ресурса (URI). В **идентификаторе** могут использоваться только строчные буквы, цифры и знак "-". Его длина должна быть от 3 до 50 знаков. Обратите внимание, что к выбранному вами имени конечной точки добавляется *documents.azure.com*. Полученное значение и станет конечной точкой вашей учетной записи.

    - Для **API NoSQL** выберите **MongoDB**. Таким образом вы выберите API обмена данными, который будет использоваться для взаимодействия с базой данных DocumentDB.

    - В поле **Подписка**выберите подписку Azure, которую вы хотите использовать для этой учетной записи. Если ваша учетная запись включает только одну подписку, эта учетная запись будет выбрана по умолчанию.

    - В поле **Группа ресурсов** выберите или создайте группу ресурсов для учетной записи.  По умолчанию будет выбрана существующая группа ресурсов в подписке Azure.  Однако вы можете создать новую группу ресурсов, чтобы добавить к ней эту учетную запись. Дополнительные сведения см. в статье [Управление ресурсами Azure с помощью портала Azure](../azure-portal/resource-group-portal.md).

    - В поле **Расположение** укажите географическое расположение, в котором будет размещена учетная запись.

6. Когда вы завершите настройку учетной записи, щелкните **Создать**.  Создание учетной записи может занять несколько минут.

   Вы можете наблюдать за ходом выполнения на портале "Уведомления".  

   ![Снимок экрана: центр уведомлений, где показано, что создается учетная запись DocumentDB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. Чтобы получить доступ к новой учетной записи, щелкните **DocumentDB (NoSQL)** в меню слева. В списке обычных учетных записей DocumentDB и учетных записей DocumentDB с поддержкой протокола Mongo щелкните имя новой учетной записи.
8. Теперь учетная запись DocumentDB готова для использования с приложением MongoDB.

   ![Снимок экрана колонки учетной записи по умолчанию](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [подключиться](documentdb-connect-mongodb-account.md) к учетной записи DocumentDB с поддержкой протокола MongoDB.

