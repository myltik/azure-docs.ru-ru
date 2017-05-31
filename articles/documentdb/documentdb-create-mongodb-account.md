---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-create-account
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 4b2ae4a8d3b93b5c27e180a5875ae9e90483068a
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017



---

# <a name="create-an-azure-cosmos-db-account-with-mongodb-api"></a>Создание учетной записи Azure Cosmos DB с помощью API MongoDB
Теперь базы данных Azure Cosmos DB можно использовать как хранилище данных для приложений, написанных для MongoDB. Чтобы использовать эту возможность, требуются учетные записи Azure и Azure Cosmos DB. В этом руководстве описано, как создать учетную запись Azure Cosmos DB для использования с приложениями MongoDB. 

Учетную запись Azure Cosmos DB с поддержкой учетной записи MongoDB можно создать с помощью портала Azure или интерфейса командной строки Azure с шаблонами Azure Resource Manager. Из этой статьи вы узнаете, как создать учетную запись Azure Cosmos DB с поддержкой учетной записи MongoDB с помощью портала Azure. Сведения о том, как создать учетную запись с помощью интерфейса командной строки Azure с Azure Resource Manager, см. в статье [Создание учетной записи для базы данных Azure Cosmos DB с помощью Azure CLI](documentdb-automation-resource-manager-cli.md).

## <a name="prerequisite"></a>Предварительные требования
Учетная запись Azure. Если у вас нет учетной записи, вы можете создать [бесплатную учетную запись Azure](https://azure.microsoft.com/free/).
## <a name="create-an-azure-cosmos-db-account"></a>Создание учетной записи Azure Cosmos DB

1. В браузере войдите на [портал Azure](https://portal.azure.com).
2. В левой области навигации щелкните **Azure Cosmos DB**.

    ![Снимок экрана левой области навигации портала с выделенной записью DocumentDB NoSQL](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. Можно также щелкнуть **Больше служб** и ввести **DocumentDB** в верхней строке поиска, а затем щелкнуть **Azure Cosmos DB**.

    ![Снимок экрана поиска записи DocumentDB NoSQL в колонке "Больше служб"](./media/documentdb-create-mongodb-account/more-services-search.PNG)

4. В верхней части колонки **Azure Cosmos DB** щелкните **+ Добавить** на верхней панели действий.

    ![Снимок экрана кнопки "Добавить" в колонке ресурсов Cosmos DB](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. В колонке **Azure Cosmos DB account** (Учетная запись Azure Cosmos DB) укажите желаемую конфигурацию учетной записи.

   ![Снимок экрана колонки создания учетной записи Azure Cosmos DB с поддержкой протокола MongoDB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.PNG)

    - В поле **Идентификатор** введите имя для идентификации учетной записи.  После успешной проверки **идентификатора** в поле **Идентификатор** отображается зеленая галочка. Значение **идентификатора** становится именем узла в универсальном коде ресурса (URI). В **идентификаторе** могут использоваться только строчные буквы, цифры и знак "-". Его длина должна быть от 3 до 50 знаков. Обратите внимание, что к выбранному вами имени конечной точки добавляется *documents.azure.com*. Полученное значение и станет конечной точкой вашей учетной записи.

    - Для **API** выберите **MongoDB**. Таким образом вы выберете API обмена данными, который будет использоваться для взаимодействия с базой данных Azure Cosmos DB.

    - В поле **Подписка**выберите подписку Azure, которую вы хотите использовать для этой учетной записи. Если ваша учетная запись включает только одну подписку, эта учетная запись будет выбрана по умолчанию.

    - В поле **Группа ресурсов** выберите или создайте группу ресурсов для учетной записи.  По умолчанию будет выбрана существующая группа ресурсов в подписке Azure.  Однако вы можете создать новую группу ресурсов, чтобы добавить к ней эту учетную запись. Дополнительные сведения см. в статье [Управление ресурсами Azure с помощью портала Azure](../azure-portal/resource-group-portal.md).

    - В поле **Расположение** укажите географическое расположение, в котором будет размещена учетная запись.

6. Когда вы завершите настройку учетной записи, щелкните **Создать**.  Создание учетной записи может занять несколько минут.

   Вы можете наблюдать за ходом выполнения на портале "Уведомления".  

   ![Снимок экрана: центр уведомлений, где показано, что создается учетная запись Azure Cosmos DB](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. Чтобы получить доступ к новой учетной записи, щелкните **Azure Cosmos DB** в меню слева. В списке обычных учетных записей DocumentDB и учетных записей DocumentDB с поддержкой протокола Mongo щелкните имя новой учетной записи.
8. Теперь учетная запись Azure Cosmos DB готова для использования с приложением MongoDB.

   ![Снимок экрана колонки учетной записи по умолчанию](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [подключиться](documentdb-connect-mongodb-account.md) к учетной записи DocumentDB с поддержкой протокола MongoDB.

