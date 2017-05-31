---
title: "Строка подключения MongoDB для учетной записи Azure Cosmos DB | Документация Майкрософт"
description: "Здесь содержатся сведения о подключении приложения MongoDB к учетной записи Azure Cosmos DB с помощью строки подключения MongoDB."
keywords: "строка подключения MongoDB"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 95fe71c87a3f70174b59cc866d7d399b7e91720c
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017


---

# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Подключение приложения MongoDB к Azure Cosmos DB
Здесь содержатся сведения о подключении приложения MongoDB к учетной записи Azure Cosmos DB с помощью строки подключения MongoDB. Подключив приложение MongoDB к базе данных Azure Cosmos DB, вы сможете использовать базу данных в качестве хранилища данных для приложения MongoDB. 

В этом руководстве описаны два способа получения строки подключения.

- [Метод "Быстрый запуск"](#QuickstartConnection) для использования с драйверами .NET, Node.js, Java, Python или оболочкой MongoDB.
- [Метод с использованием пользовательской строки подключения](#GetCustomConnection) для других драйверов.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure. Если у вас нет учетной записи, вы можете создать [бесплатную учетную запись Azure](https://azure.microsoft.com/free/). 
- Учетная запись Azure Cosmos DB. Дополнительные сведения см. в статье [Azure Cosmos DB. Создание веб-приложения API DocumentDB с использованием языка .NET и портала Azure](documentdb-create-mongodb-account.md).

## <a id="QuickstartConnection"></a>Получение строки подключения MongoDB с помощью метода быстрого запуска
1. В браузере войдите на [портал Azure](https://portal.azure.com).
2. В колонке **Azure Cosmos DB** выберите учетную запись API MongoDB. 
3. На **левой панели навигации** в колонке учетной записи щелкните **Быстрый запуск**. 
4. Выберите платформу (*драйвер .NET*, *Node.js*, *Java*, *Python* или *оболочка MongoDB*). Если соответствующего драйвера или средства нет в списке, не беспокойтесь, мы постоянно добавляем дополнительные фрагменты кода для подключения. В конце статьи оставьте комментарий о том, что, по вашему мнению, нужно добавить, и прочтите статью [Подключение к учетной записи DocumentDB с поддержкой протокола MongoDB](#GetCustomConnection), чтобы научиться создавать собственные подключения.
5. Скопируйте и вставьте фрагмент кода в приложение MongoDB. Теперь все готово к работе.

    ![Снимок экрана колонки "Быстрый запуск"](./media/documentdb-connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Получение строки подключения MongoDB для настройки
1. В браузере войдите на [портал Azure](https://portal.azure.com).
2. В колонке **Azure Cosmos DB** выберите учетную запись API MongoDB. 
3. На **левой панели навигации** в колонке учетной записи щелкните **Строка подключения**. 
4. Откроется колонка **Сведения о строке подключения**, которая содержит все необходимые сведения для подключения к учетной записи с помощью драйвера для MongoDB, включая автоматически сформированную строку подключения.

    ![Снимок экрана, колонка строки подключения](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Требования к строке подключения
> [!Important]
> В Azure Cosmos DB строгие требования к безопасности и стандарты. Для всех учетных записей Azure Cosmos DB требуется проверка подлинности и безопасный обмен данными через **SSL**.
>
>

Важно отметить, что Azure Cosmos DB поддерживает стандартный формат универсального кода ресурса для строки подключения MongoDB, налагая пару дополнительных условий. Для учетных записей Azure Cosmos DB требуется использовать проверку подлинности и безопасный обмен данными через SSL.  Таким образом, строка подключения имеет следующий вид:

    mongodb://username:password@host:port/[database]?ssl=true

Значения для этой строки можно найти в колонке строки подключения, как показано выше.

* Имя пользователя (обязательно)
  * Имя учетной записи Azure Cosmos DB.
* Пароль (обязательно)
  * Пароль для учетной записи Azure Cosmos DB.
* Узел (обязательно)
  * Полное доменное имя учетной записи Azure Cosmos DB.
* Порт (обязательно)
  * 10250
* База данных (необязательно)
  * База данных по умолчанию, используемая подключением (если база данных не указана, то по умолчанию используется база данных test).
* ssl=true (обязательно)

Рассмотрим в качестве примера учетную запись, сведения о которой приведены выше в строке подключения.  Допустимая строка подключения:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@anhohmongo.documents.azure.com:10250/mydatabase?ssl=true

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [использовать MongoChef](documentdb-mongodb-mongochef.md) с учетной записью API для MongoDB в Azure Cosmos DB.
* Ознакомьтесь с [примерами](documentdb-mongodb-samples.md) API для MongoDB в Azure Cosmos DB.

