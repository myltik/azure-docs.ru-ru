---
title: "Использование mongoimport и mongorestore с Azure DocumentDB | Документация Майкрософт"
description: "Узнайте, как использовать mongoimport и mongorestore для импорта данных в учетную запись &quot;DocumentDB: API для MongoDB&quot;."
keywords: mongoimport, mongorestore
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 2af8691326550c631e6668890cb0d6b893fa7740
ms.lasthandoff: 03/08/2017


---
# <a name="migrate-data-to-documentdb-by-using-mongoimport-and-mongorestore"></a>Перенос данных в DocumentDB с помощью mongoimport и mongorestore
> [!div class="op_single_selector"]
> * [Импорт в DocumentDB](documentdb-import-data.md)
> * [Импорт в API для MongoDB](documentdb-mongodb-migrate.md)
>
>

Чтобы перенести данные в учетную запись Azure "DocumentDB: API для MongoDB", сделайте следующее:

* Скачайте файл *mongoimport.exe* или *mongorestore.exe* из [центра скачивания MongoDB](https://www.mongodb.com/download-center).
* Получите [поддержку DocumentDB для строки подключения MongoDB](documentdb-connect-mongodb-account.md).

## <a name="before-you-begin"></a>Перед началом работы

* Увеличьте пропускную способность. Продолжительность переноса данных зависит от пропускной способности, настроенной для коллекций. Увеличьте пропускную способность для крупных миграций. После переноса уменьшите пропускную способность для экономии расходов. Дополнительные сведения об увеличении пропускной способности на [портале Azure](https://portal.azure.com) см. в статье [Уровни производительности и ценовые категории в DocumentDB](documentdb-performance-levels.md).

* Включите SSL. У DocumentDB строгие требования к безопасности и стандарты. Обязательно включите SSL при взаимодействии с учетной записью. Процедуры, описанные в оставшейся части статьи, включают инструкции по включению SSL для *mongoimport* и *mongorestore*.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Поиск сведений о строке подключения (узел, порт, имя пользователя и пароль)

1. В левой панели на [портале Azure](https://portal.azure.com) щелкните запись **NoSQL (DocumentDB)**.
2. На панели **Подписки** выберите имя своей учетной записи.
3. В колонке **Строка подключения** щелкните **Строка подключения**.  
На правой панели содержатся все сведения, необходимые для успешного подключения к учетной записи.

    ![Колонка "Строка подключения"](./media/documentdb-mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-api-for-mongodb-with-mongoimport"></a>Импорт данных в API для MongoDB с помощью mongoimport

Чтобы импортировать данные в учетную запись DocumentDB, используйте следующий шаблон для выполнения импорта. Укажите *узел*, *имя пользователя* и *пароль* своей учетной записи.  

Шаблон:

    mongoimport.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Пример:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-api-for-mongodb-with-mongorestore"></a>Импорт данных в API для MongoDB с помощью mongorestore

Чтобы восстановить данные в учетной записи DocumentDB, используйте следующий шаблон для выполнения импорта. Укажите *узел*, *имя пользователя* и *пароль* своей учетной записи.

Шаблон:

    mongorestore.exe --host <your_hostname>:10250 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Пример:

    mongorestore.exe --host anhoh-host.documents.azure.com:10250 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения см. в [примерах использования DocumentDB: API для MongoDB](documentdb-mongodb-samples.md).

