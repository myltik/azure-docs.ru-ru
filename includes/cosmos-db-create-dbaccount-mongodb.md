---
title: Создание учетной записи API MongoDB Azure Cosmos DB
description: В этой статье описано, как создать учетную запись API MongoDB Azure Cosmos DB на портале Azure.
services: cosmos-db
author: mimig1
ms.service: cosmos-db
ms.topic: include
ms.date: 03/20/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 02ea0e011642313b885bc48ec48104fa2789da81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
1. В новом окне войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева щелкните **Создать ресурс**, **Базы данных**, а затем в разделе **Azure Cosmos DB** выберите **Создать**.
   
   ![Снимок экрана портала Azure, на котором выделено меню "Больше служб" и Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. В колонке **Новая учетная запись** укажите **MongoDB** в качестве API и укажите желаемую конфигурацию учетной записи Azure Cosmos DB.
 
    ![Снимок экрана новой колонки Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)

    * **Идентификатор** должен быть уникальным именем, выбранным для идентификации учетной записи Azure Cosmos DB. В нем могут использоваться только строчные буквы, цифры и символ -, а его длина должна составлять от 3 до 50 символов.
    * **Подписка.** Ваша подписка Azure. Она будет указана автоматически.
    * **Группа ресурсов.** Имя группы ресурсов для вашей учетной записи Azure Cosmos DB.
    * **Расположение.** Географическое расположение, в котором будет размещен экземпляр Azure Cosmos DB. Выберите ближайшее к пользователям расположение.

4. Щелкните **Создать** , чтобы создать учетную запись.
5. На панели инструментов щелкните **Уведомления**, чтобы отслеживать процесс развертывания.

    ![Уведомление с текстом "Развертывание начато"](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  После завершения развертывания откройте новую учетную запись из плитки "Все ресурсы". 

    ![Учетная запись Cosmos DB на плитке "Все ресурсы"](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
