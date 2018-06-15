---
title: Руководство по C++ для Azure Cosmos DB | Документация Майкрософт
description: Руководство по C++, в котором создается база данных и консольное приложение C++ с помощью Azure Cosmos DB с поддержкой пакета SDK для C++. Azure Cosmos DB — это глобально масштабируемая служба базы данных.
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: cpp
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: sngun
ms.openlocfilehash: 0e142eaf4182331e0a5803c54d2cc1284e21b221
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807181"
---
# <a name="azure-cosmos-db-c-console-application-tutorial-for-the-sql-api"></a>Azure Cosmos DB. Руководство по использованию консольного приложения C++ для API SQL
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js для MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Мы рады представить вам руководство по C++ для соответствующего пакета SDK для API SQL Azure Cosmos DB. По завершении работы с этим руководством у вас будет консольное приложение, которое создает ресурсы Azure Cosmos DB и выполняет запросы этих ресурсов, включая базу данных C++.

В этом кратком руководстве рассматриваются следующие темы:

* создание учетной записи Azure Cosmos DB и подключение к ней;
* Настройка приложения
* создание базы данных Azure Cosmos DB для C++;
* создание коллекции;
* создание документов JSON;
* выполнение запросов к коллекции;
* замена документа;
* удаление документа;
* удаление базы данных Azure Cosmos DB для C++.

У вас нет времени? Не беспокойтесь! Полное решение доступно на [GitHub](https://github.com/stalker314314/DocumentDBCpp). Краткие инструкции см. в разделе [Получение полного решения для руководства по Node.js](#GetSolution).

А теперь приступим к работе!

## <a name="prerequisites-for-the-c-tutorial"></a>Предварительные требования для прохождения руководства по C++
Убедитесь, что у вас есть указанные ниже ресурсы.

* Активная учетная запись Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) и установленные компоненты языка C++. Если вы еще не установили Visual Studio 2017, вы можете скачать и использовать **бесплатный** [выпуск Community для Visual Studio 2017](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Шаг 1. Создание учетной записи Azure Cosmos DB
Давайте создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к шагу [Настройка приложения C++](#SetupC++).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupC++"></a>Шаг 2. Настройка приложения C++
1. Откройте Visual Studio, выберите меню **Файл**, щелкните команду **Создать**, а затем **Проект**. 
2. В окне **Создание проекта** в области **Установленные** разверните узел **Visual C++**, щелкните **Win32**, а затем **Консольное приложение Win32**. Присвойте проекту имя hellodocumentdb и нажмите кнопку **ОК**. 
   
    ![Снимок экрана с изображением мастера создания проектов](media/sql-api-cpp-get-started/hello.png)
3. Когда мастер настройки приложений Win32 запустится, нажмите кнопку **Готово**.
4. Когда проект будет создан, откройте диспетчер пакетов NuGet. Для этого щелкните правой кнопкой мыши проект **hellodocumentdb** в **обозревателе решений** и выберите пункт **Manage NuGet Packages** (Управление пакетами NuGet). 
   
    ![Снимок экрана с изображением пункта Manage NuGet Packages ("Управление пакетами NuGet") в меню проекта](media/sql-api-cpp-get-started/nuget.png)
5. На вкладке **NuGet: hellodocumentdb** щелкните раздел **Browse** (Обзор) и найдите *documentdbcpp*. Среди результатов выберите DocumentDbCPP, как показано на снимке экрана ниже:   
   
    ![Снимок экрана с изображением выделенного пакета DocumentDbCpp](media/sql-api-cpp-get-started/cpp.png)
   
    Этот пакет устанавливает ссылки на пакет C++ REST SDK, который зависит от DocumentDbCPP. После добавления пакетов в проект можно приступить к написанию кода.   

## <a id="Config"></a>Шаг 3. Копирование сведений о подключении для базы данных Azure Cosmos DB из портала Azure
Откройте [портал Azure](https://portal.azure.com) и просмотрите созданную учетную запись Azure Cosmos DB. Для следующего шага понадобится универсальный код ресурса (URI) и первичный ключ с портала Azure, чтобы установить подключение с помощью фрагмента кода C++. 

![Универсальный код ресурса (URI) и ключи Azure Cosmos DB на портале Azure](media/sql-api-cpp-get-started/nosql-tutorial-keys.png)

## <a id="Connect"></a>Шаг 4. Подключение к учетной записи Azure Cosmos DB
1. Добавьте в исходный код после `#include "stdafx.h"` следующие пространства имен и заголовки.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. Далее добавьте следующий код к функции main, а затем замените настройки учетной записи и первичный ключ соответствующими параметрами Azure Cosmos DB, скопированными на шаге 3. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    Теперь, когда у нас есть код для инициализации клиента, мы рассмотрим принципы работы с ресурсами Azure Cosmos DB.

## <a id="CreateDBColl"></a>Шаг 5. Создание базы данных и коллекции C++
Прежде чем выполнить этот шаг, рассмотрим, как взаимодействуют база данных, коллекция и документы, на случай если вы еще не знакомы с принципами работы Azure Cosmos DB. [База данных](sql-api-resources.md#databases) представляет собой логический контейнер для хранения документов, распределенных по коллекциям. [Коллекция](sql-api-resources.md#collections) — это контейнер JSON-документов и связанной логики приложения на JavaScript. Дополнительные сведения об иерархической модели ресурсов и основных понятиях Azure Cosmos DB см. в [этой статье](sql-api-resources.md).

Чтобы создать базу данных и соответствующую коллекцию, добавьте следующий код в конец функции main. Так вы создадите базу данных с именем FamilyRegistry и коллекцию FamilyCollection, используя конфигурацию клиента, объявленного на предыдущем шаге.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>Шаг 6. Создание документа
[Документы](sql-api-resources.md#documents) относятся к пользовательскому (произвольному) JSON-содержимому. Теперь можно вставить документ в Azure Cosmos DB. Документ можно создать, скопировав следующий код в конец функции main. 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

Таким образом, этот код создает базу данных, коллекцию и документы Azure Cosmos DB, которые можно запросить в обозревателе данных на портале Azure. 

![Руководство по C++. Схема, иллюстрирующая иерархические отношения между учетной записью, базой данных, коллекцией и документами](media/sql-api-cpp-get-started/docs.png)

## <a id="QueryDB"></a>Шаг 7. Запрос ресурсов Azure Cosmos DB
Azure Cosmos DB поддерживает [полнофункциональные запросы](sql-api-sql-query.md) к документам JSON, хранящимся в каждой коллекции. Ниже приведен пример кода запроса, в котором используется синтаксис SQL. Его можно запускать, чтобы запрашивать документы, созданные на предыдущем шаге.

Функция принимает в качестве аргументов уникальный идентификатор или идентификатор ресурса для базы данных и коллекции, а также клиента документов. Добавьте следующий код перед функцией main.

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Replace"></a>Шаг 8. Замена документа
Azure Cosmos DB поддерживает замену JSON-документов, как показано в коде ниже. Добавьте этот код после функции executesimplequery.

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a id="Delete"></a>Шаг 9. Удаление документа
Azure Cosmos DB поддерживает удаление JSON-документов. Чтобы удалить документ, скопируйте код ниже и вставьте его после функции replacedocument. 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="DeleteDB"></a>Шаг 10. Удаление базы данных
Удаление созданной базы данных влечет удаление всех ее дочерних ресурсов (коллекций, документов и т. д.).

Скопируйте и вставьте следующий фрагмент кода (функция cleanup) после функции deletedocument, чтобы удалить базу данных и все ее дочерние ресурсы.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Run"></a>Шаг 11. Запуск консольного приложения C++
Вы добавили код для создания, запроса, изменения и удаления различных ресурсов Azure Cosmos DB.  Объедините все это, добавив вызовы этих различных функций из функции main в hellodocumentdb.cpp, а также некоторые диагностические сообщения.

Чтобы сделать это, замените функцию main приложения кодом ниже. Данные account_configuration_uri и primary_key, скопированные в код на шаге 3, будут перезаписаны, поэтому сохраните соответствующую строку или скопируйте значения на портале. 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

Теперь можно собрать и запустить код в Visual Studio, нажав клавишу F5, или в окне терминала через приложение, запустив исполняемый файл. 

Должны отобразиться выходные данные вашего приложения. Выходные данные должны соответствовать показанным на снимке экрана ниже:

![Выходные данные приложения C++ для Azure Cosmos DB](media/sql-api-cpp-get-started/console.png)

Поздравляем! Вы ознакомились с руководством по C++ и создали первое консольное приложение Azure Cosmos DB.

## <a id="GetSolution"></a>Получение завершенного решения C++ для этого руководства
Чтобы собрать решение GetStarted, содержащее все примеры из этой статьи, вам понадобится следующее:

* [Учетная запись Azure Cosmos DB][create-account].
* решение [GetStarted](https://github.com/stalker314314/DocumentDBCpp) , доступное в GitHub.

## <a name="next-steps"></a>Дополнительная информация
* Узнайте, как выполнять [мониторинг учетной записи Azure Cosmos DB](monitor-accounts.md).
* Отправьте запросы образцу набора данных в [Query Playground](https://www.documentdb.com/sql/demo).
* Дополнительные сведения о модели программирования см. в разделе "Разработка" [на странице документации Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

[create-account]: create-sql-api-dotnet.md#create-account


