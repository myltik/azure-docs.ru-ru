---
title: "Использование Robomongo с Azure Cosmos DB | Документация Майкрософт"
description: "Узнайте, как использовать Robomongo с учетной записью API для MongoDB в Azure Cosmos DB."
keywords: Robomongo
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: anhoh
ms.openlocfilehash: 8983594776a1bbe413a6d7cf2cd518f0e327648a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Использование Robomongo с учетной записью API для MongoDB в Azure Cosmos DB
Чтобы подключиться к учетной записи API для MongoDB в Azure Cosmos DB с помощью Robomongo, необходимо:

* скачать и установить [Robomongo](https://robomongo.org/);
* Подготовить сведения о [строке подключения](connect-mongodb-account.md) для учетной записи API для MongoDB в Azure Cosmos DB.

## <a name="connect-using-robomongo"></a>Подключение с использованием Robomongo
Чтобы добавить к подключениям Robomongo MongoDB учетную запись API для MongoDB в Azure Cosmos DB, сделайте следующее.

1. Извлеките сведения о подключении учетной записи API для MongoDB в Azure Cosmos DB. Ознакомьтесь с инструкциями [здесь](connect-mongodb-account.md).

    ![Снимок экрана, колонка строки подключения](./media/mongodb-robomongo/connectionstringblade.png)
2. Запустите *Robomongo.exe*.

3. Нажмите кнопку подключения под меню **File** (Файл) для управления подключениями. Щелкните **Create** (Создать) в окне **MongoDB Connections** (Подключения MongoDB). Откроется окно **Connection Settings** (Параметры подключения).

4. В окне **Connection Settings** (Параметры подключения) выберите имя. Затем найдите **узел** и **порт**, указанные в сведениях о подключении, полученных на шаге 1, и введите их в полях **Address** (Адрес) и **Port** (Порт) соответственно.

    ![Снимок экрана окна управления подключениями Robomongo](./media/mongodb-robomongo/manageconnections.png)
5. На вкладке **Authentication** (Аутентификация) установите флажок **Perform authentication** (Выполнять аутентификацию). Затем введите базу данных (по умолчанию — *Admin*), **имя пользователя** и **пароль**.
**Имя пользователя** и **пароль** можно найти в данных подключения, полученных на шаге 1.

    ![Снимок экрана вкладки аутентификации Robomongo](./media/mongodb-robomongo/authentication.png)
6. На вкладке **SSL** установите флажок **Use SSL protocol** (Использовать протокол SSL), затем измените значение параметра **Authentication Method** (Метод аутентификации) на **Self-signed Certificate** (Самозаверяющий сертификат).

    ![Снимок экрана вкладки SSL Robomongo](./media/mongodb-robomongo/SSL.png)
7. Наконец, нажмите кнопку **Test** (Проверить), чтобы проверить возможность подключения, затем нажмите кнопку **Save** (Сохранить).

## <a name="next-steps"></a>Дополнительная информация
* Ознакомьтесь с [примерами](mongodb-samples.md) API для MongoDB в Azure Cosmos DB.
