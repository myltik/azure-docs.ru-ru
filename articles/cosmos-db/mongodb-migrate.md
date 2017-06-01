---
title: "Использование mongoimport и mongorestore с API для MongoDB Azure Cosmos DB | Документация Майкрософт"
description: "Узнайте, как использовать mongoimport и mongorestore для импорта данных в учетную запись API для MongoDB."
keywords: mongoimport, mongorestore
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
ms.date: 05/22/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: ab4fb39f8784a2d6aa956056a30649aab1d08487
ms.contentlocale: ru-ru
ms.lasthandoff: 05/31/2017


---

# <a name="azure-cosmos-db-how-to-import-mongodb-data"></a>Как импортировать данные MongoDB в Azure Cosmos DB? 

Для переноса данных из MongoDB в учетную запись Azure Cosmos DB для использования с API MongoDB необходимо сделать следующее:

* Скачайте файл *mongoimport.exe* или *mongorestore.exe* из [центра скачивания MongoDB](https://www.mongodb.com/download-center).
* Получите [строку подключения API для MongoDB](connect-mongodb-account.md).

Если вы импортируете данные из MongoDB и планируете использовать их с API DocumentDB, используйте средство миграции данных для их переноса. Дополнительные сведения см. в статье [Импорт данных в DocumentDB с помощью средства миграции базы данных](import-data.md).

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Получение строки подключения
> * Импорт данных MongoDB с помощью mongoimport.
> * Импорт данных MongoDB с помощью mongorestore.

## <a name="prerequisites"></a>Предварительные требования

* Увеличьте пропускную способность. Продолжительность переноса данных зависит от пропускной способности, настроенной для коллекций. Увеличьте пропускную способность для крупных миграций. После переноса уменьшите пропускную способность для экономии расходов. Дополнительные сведения об увеличении пропускной способности на [портале Azure](https://portal.azure.com) см. в статье [Прекращение использования уровней производительности S1, S2 и S3 в DocumentDB](performance-levels.md).

* Включите SSL. В Azure Cosmos DB строгие требования к безопасности и стандарты. Обязательно включите SSL при взаимодействии с учетной записью. Процедуры, описанные в оставшейся части статьи, включают инструкции по включению SSL для *mongoimport* и *mongorestore*.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Поиск сведений о строке подключения (узел, порт, имя пользователя и пароль)

1. В левой панели на [портале Azure](https://portal.azure.com) щелкните запись **Azure Cosmos DB**.
2. На панели **Подписки** выберите имя своей учетной записи.
3. В колонке **Строка подключения** щелкните **Строка подключения**.  
На правой панели содержатся все сведения, необходимые для успешного подключения к учетной записи.

    ![Колонка "Строка подключения"](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-api-for-mongodb-with-mongoimport"></a>Импорт данных в API для MongoDB с помощью mongoimport

Чтобы импортировать данные в учетную запись Azure Cosmos DB, используйте следующий шаблон для выполнения импорта. Укажите *узел*, *имя пользователя* и *пароль* своей учетной записи.  

Шаблон:

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Пример:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-api-for-mongodb-with-mongorestore"></a>Импорт данных в API для MongoDB с помощью mongorestore

Чтобы восстановить данные в API для учетной записи MongoDB, используйте следующий шаблон для выполнения импорта. Укажите *узел*, *имя пользователя* и *пароль* своей учетной записи.

Шаблон:

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Пример:

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * Получили строку подключения.
> * Импортировали данные MongoDB с помощью mongoimport.
> * Импортировали данные MongoDB с помощью mongorestore.

Теперь вы можете перейти к следующему руководству, из которого вы узнаете, как запрашивать данные MongoDB с помощью Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Как выполнять запросы к данным в базе данных Azure Cosmos DB с помощью API для MongoDB](../cosmos-db/tutorial-query-mongodb.md)

