---
title: Строка подключения MongoDB для учетной записи Azure Cosmos DB | Документация Майкрософт
description: Здесь содержатся сведения о подключении приложения MongoDB к учетной записи Azure Cosmos DB с помощью строки подключения MongoDB.
keywords: строка подключения MongoDB
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 12/19/2017
ms.author: sngun
ms.openlocfilehash: 441f6725b96dc7a099304e06db6d46a65e7f9c81
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795964"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Подключение приложения MongoDB к Azure Cosmos DB
Здесь содержатся сведения о подключении приложения MongoDB к учетной записи Azure Cosmos DB с помощью строки подключения MongoDB. Вы сможете использовать базу данных Azure Cosmos DB в качестве хранилища данных для приложения MongoDB. 

В этом руководстве описаны два способа получения строки подключения.

- [Метод "Быстрый запуск"](#QuickstartConnection) для использования с драйверами .NET, Node.js, Java, Python или оболочкой MongoDB.
- [Метод с использованием пользовательской строки подключения](#GetCustomConnection) для других драйверов.

## <a name="prerequisites"></a>предварительным требованиям

- Учетная запись Azure. Если у вас нет учетной записи, вы можете создать [бесплатную учетную запись Azure](https://azure.microsoft.com/free/). 
- Учетная запись Azure Cosmos DB. Инструкции см. в статье [Azure Cosmos DB. Создание веб-приложения API MongoDB с использованием языка .NET и портала Azure](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Получение строки подключения MongoDB с помощью метода быстрого запуска
1. В браузере войдите на [портал Azure](https://portal.azure.com).
2. В колонке **Azure Cosmos DB** выберите API для учетной записи MongoDB. 
3. В левой панели в колонке учетной записи щелкните **Быстрый запуск**. 
4. Выберите платформу (**.NET**, **Node.js**, **оболочка MongoDB**, **Java**, **Python**). Если соответствующего драйвера или средства нет в списке, не беспокойтесь, мы постоянно добавляем дополнительные фрагменты кода для подключения. Оставьте в конце статьи свои комментарии о том, что, по вашему мнению, нужно добавить. Дополнительные сведения о том, как создавать собственное подключение, см.в разделе [Получение строки подключения MongoDB для настройки](#GetCustomConnection).
5. Скопируйте и вставьте фрагмент кода в приложение MongoDB.

    ![Колонка "Быстрый запуск"](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Получение строки подключения MongoDB для настройки
1. В браузере войдите на [портал Azure](https://portal.azure.com).
2. В колонке **Azure Cosmos DB** выберите API для учетной записи MongoDB. 
3. На левой панели в колонке учетной записи щелкните **Строка подключения**. 
4. Откроется колонка **Строка подключения**. Она содержит все необходимые сведения для подключения к учетной записи с помощью драйвера для MongoDB, включая автоматически сформированную строку подключения.

    ![Колонка "Строка подключения"](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Требования к строке подключения
> [!Important]
> В Azure Cosmos DB строгие требования к безопасности и стандарты. Для всех учетных записей Azure Cosmos DB требуется проверка подлинности и безопасный обмен данными через *SSL*. 
>
>

Azure Cosmos DB поддерживает стандартный формат универсального кода ресурса для строки подключения MongoDB, налагая пару дополнительных условий. Для учетных записей Azure Cosmos DB требуется использовать проверку подлинности и безопасный обмен данными через SSL. В этом случае строка подключения имеет следующий вид:

    mongodb://username:password@host:port/[database]?ssl=true

Значения для этой строки можно найти в колонке **Строка подключения**, как показано выше:

* Имя пользователя (обязательно): имя учетной записи Azure Cosmos DB.
* Пароль (обязательно): пароль для учетной записи Azure Cosmos DB.
* Узел (обязательно): полное доменное имя учетной записи Azure Cosmos DB.
* Порт (обязательно): 10255.
* База данных (необязательно): база данных, используемая для подключения. Если база данных не указана, база данных по умолчанию — test.
* ssl=true (обязательно)

Рассмотрим в качестве примера учетную запись, сведения о которой приведены выше в колонке **Строка подключения**. Допустимая строка подключения:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Дополнительная информация
* Узнайте, как [использовать Studio 3T (MongoChef)](mongodb-mongochef.md) с учетной записью API для MongoDB в Azure Cosmos DB.
* Ознакомьтесь с [примерами](mongodb-samples.md) API для MongoDB в Azure Cosmos DB.
