---
title: включение файла
description: включение файла
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 690c5f07a2b0f7a88e16f0b0bbbaa9ca78e37317
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
1. В новом окне войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева щелкните **Создать ресурс**, **Базы данных**, а затем в разделе **Azure Cosmos DB** выберите **Создать**.
   
   ![Снимок экрана портала Azure, на котором выделено меню "Больше служб" и Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. В колонке **Новая учетная запись** укажите **MongoDB** в качестве API и укажите желаемую конфигурацию учетной записи Azure Cosmos DB.
 
    * **Идентификатор** должен быть уникальным именем, выбранным для идентификации учетной записи Azure Cosmos DB. В нем могут использоваться только строчные буквы, цифры и символ -, а его длина должна составлять от 3 до 50 символов.
    * **Подписка.** Ваша подписка Azure. Она будет указана автоматически.
    * **Группа ресурсов.** Имя группы ресурсов для вашей учетной записи Azure Cosmos DB. Выберите **Создать** и введите новое имя группы ресурсов для учетной записи. Для удобства можно использовать то же имя, которое присвоено идентификатору.
    * **Расположение.** Географическое расположение, в котором будет размещен экземпляр Azure Cosmos DB. Выберите ближайшее к пользователям расположение.

    Затем щелкните **Создать**.

    ![Страница "Новая учетная запись" для Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Создание учетной записи займет несколько минут. Дождитесь, пока на портале откроется страница с сообщением **Поздравляем! Учетная запись Azure Cosmos DB с API MongoDB создана**.

    ![Область "Уведомления" на портале Azure](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
