---
title: "Руководство по NoSQL C++ для DocumentDB | Документация Майкрософт"
description: "Руководство по NoSQL C++, в котором создается база данных и консольное приложение C++ с помощью пакета SDK для DocumentDB C++. DocumentDB — это глобально масштабируемая служба баз данных NoSQL."
services: documentdb
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: 
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: hero-article
ms.date: 11/02/2016
ms.author: aasthan
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 31368661906bc8ca0f8ac1d3329bb053d41a94d7


---
# <a name="nosql-c-tutorial-documentdb-c-console-application"></a>Руководство по NoSQL C++. Консольное приложение DocumentDB на языке C++
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
> 
> 

Добро пожаловать в руководство по C++, посвященное пакету SDK для C++ для Azure DocumentDB. После прохождения этого руководства у вас будет консольное приложение, которое создает ресурсы DocumentDB, в том числе базу данных C++, и отправляет запросы к ним.

Мы рассмотрим следующие вопросы:

* создание учетной записи DocumentDB и подключение к ней;
* Настройка приложения
* создание базы данных DocumentDB C++;
* создание коллекции;
* создание документов JSON;
* выполнение запросов к коллекции;
* замена документа;
* удаление документа;
* удаление базы данных DocumentDB C++;

У вас нет времени? Не беспокойтесь! Полное решение доступно на [GitHub](https://github.com/stalker314314/DocumentDBCpp). Краткие инструкции см. в разделе [Получение полного решения для руководства по Node.js](#GetSolution).

Ознакомившись с руководством по C++, воспользуйтесь кнопками голосования в нижней части этой страницы, чтобы отправить нам отзыв. 

Если вы хотите, чтобы мы связались с вами, укажите свой электронный адрес в комментариях или [оправьте свои данные отсюда](https://www.research.net/r/8BKRJ3Z). 

А теперь приступим к работе!

## <a name="prerequisites-for-the-c-tutorial"></a>Предварительные требования для прохождения руководства по C++
Убедитесь, что у вас есть указанные ниже компоненты.

* Активная учетная запись Azure. Если у вас нет такой учетной записи, вы можете зарегистрироваться для использования [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/) и установленные компоненты языка C++.

## <a name="step-1-create-a-documentdb-account"></a>Этап 1: создание учетной записи DocumentDB
Создадим учетную запись DocumentDB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к шагу [Настройка приложения C++](#SetupNode).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="a-idsetupcastep-2-set-up-your-c-application"></a><a id="SetupC++"></a>Шаг 2. Настройка приложения C++
1. Откройте Visual Studio, выберите меню **Файл**, щелкните команду **Создать**, а затем **Проект**. 
2. В окне **Создание проекта** в области **Установленные** разверните узел **Visual C++**, щелкните **Win32**, а затем **Консольное приложение Win32**. Присвойте проекту имя hellodocumentdb и нажмите кнопку **ОК**. 
   
    ![Снимок экрана с изображением мастера создания проектов](media/documentdb-cpp-get-started/hellodocumentdb.png)
3. Когда мастер настройки приложений Win32 запустится, нажмите кнопку **Готово**.
4. Когда проект будет создан, откройте диспетчер пакетов NuGet. Для этого щелкните правой кнопкой мыши проект **hellodocumentdb** в **обозревателе решений** и выберите пункт **Manage NuGet Packages** (Управление пакетами NuGet). 
   
    ![Снимок экрана с изображением пункта Manage NuGet Packages ("Управление пакетами NuGet") в меню проекта](media/documentdb-cpp-get-started/nuget.png)
5. На вкладке **NuGet: hellodocumentdb** щелкните раздел **Browse** (Обзор) и найдите *documentdbcpp*. Среди результатов выберите DocumentDbCPP, как показано на снимке экрана ниже. Этот пакет устанавливает ссылки на пакет C++ REST SDK, который зависит от DocumentDbCPP.  
   
    ![Снимок экрана с изображением выделенного пакета DocumentDbCpp](media/documentdb-cpp-get-started/documentdbcpp.png)
   
    После добавления пакетов в проект, можно приступить к написанию кода.   

## <a name="a-idconfigastep-3-copy-connection-details-from-azure-portal-for-your-documentdb-database"></a><a id="Config"></a>Шаг 3. Копирование сведений о подключении для базы данных DocumentDB из портала Azure
Откройте [портал Azure](https://portal.azure.com) и просмотрите созданную учетную запись базы данных NoSQL (DocumentDB). Для следующего шага понадобится универсальный код ресурса (URI) и первичный ключ из портала Azure, чтобы установить подключение с помощью фрагмента кода C++. 

![URI и ключи DocumentDB на портале Azure](media/documentdb-cpp-get-started/nosql-tutorial-keys.png)

## <a name="a-idconnectastep-4-connect-to-a-documentdb-account"></a><a id="Connect"></a>Шаг 4. Подключение к учетной записи DocumentDB
1. Добавьте в исходный код после `#include "stdafx.h"` следующие пространства имен и заголовки.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. Добавьте следующий код к функции main, а затем замените настройки учетной записи и первичный ключ соответствующими параметрами DocumentDB, скопированными на шаге 3. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    Теперь, когда у нас есть код для инициализации клиента DocumentDB, мы рассмотрим принципы работы с ресурсами DocumentDB.

## <a name="a-idcreatedbcollastep-5-create-a-c-database-and-collection"></a><a id="CreateDBColl"></a>Шаг 5. Создание базы данных и коллекции C++
Прежде чем выполнить этот шаг, рассмотрим, как взаимодействуют база данных, коллекция и документы, на случай если вы еще не знакомы с принципами работы DocumentDB. [База данных](documentdb-resources.md#databases) представляет собой логический контейнер для хранения документов, распределенных по коллекциям. [Коллекция](documentdb-resources.md#collections) — это контейнер JSON-документов и связанной логики приложения на JavaScript. Дополнительные сведения об иерархической модели ресурсов и основных понятиях DocumentDB см. [здесь](documentdb-resources.md).

Чтобы создать базу данных и соответствующую коллекцию, добавьте следующий код в конец функции main. Так вы создадите базу данных с именем FamilyRegistry и коллекцию FamilyCollection, используя конфигурацию клиента, объявленного на предыдущем шаге.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a name="a-idcreatedocastep-6-create-a-document"></a><a id="CreateDoc"></a>Шаг 6. Создание документа
[Документы](documentdb-resources.md#documents) относятся к пользовательскому (произвольному) JSON-содержимому. Теперь можно вставить документ в DocumentDB. Документ можно создать, скопировав следующий код в конец функции main. 

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

Таким образом, этот код создает базу данных, коллекцию и документы DocumentDB, которые можно запросить на портале Azure в проводнике документов. 

![Руководство по C++. Схема, иллюстрирующая иерархические отношения между учетной записью, базой данных, коллекцией и документами](media/documentdb-cpp-get-started/documentdbdocs.png)

## <a name="a-idquerydbastep-7-query-documentdb-resources"></a><a id="QueryDB"></a>Этап 7: запросы ресурсов DocumentDB
DocumentDB поддерживает [полнофункциональные запросы](documentdb-sql-query.md) к документам JSON, хранящимся в каждой коллекции. Ниже приведен пример кода запроса, где используется синтаксис SQL DocumentDB. Его можно запускать, чтобы запрашивать документы, созданные на предыдущем шаге.

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

## <a name="a-idreplaceastep-8-replace-a-document"></a><a id="Replace"></a>Шаг 8. Замена документа
DocumentDB поддерживает замену JSON-документов, как показано в следующем коде. Добавьте этот код после функции executesimplequery.

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

## <a name="a-iddeleteastep-9-delete-a-document"></a><a id="Delete"></a>Шаг 9. Удаление документа
DocumentDB поддерживает удаление JSON-документов. Чтобы удалить документ, скопируйте код ниже и вставьте его после функции replacedocument. 

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

## <a name="a-iddeletedbastep-10-delete-a-database"></a><a id="DeleteDB"></a>Шаг 10. Удаление базы данных
Удаление созданной базы данных влечет удаление всех ее дочерних ресурсов (коллекций, документов и т. д.).

Скопируйте и вставьте следующий фрагмент кода (функция cleanup) после функции deletedocument, чтобы удалить базу данных и все ее дочерние ресурсы.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a name="a-idrunastep-11-run-your-c-application-all-together"></a><a id="Run"></a>Шаг 11. Запуск консольного приложения C++
Мы добавили код для создания, запроса, изменения и удаления различных ресурсов DocumentDB.  А теперь мы объединим все это, добавив вызовы различных функций с помощью функции main в hellodocumentdb.cpp, а также некоторые диагностические сообщения.

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

Должны отобразиться выходные данные вашего приложения. Выходные данные должны соответствовать показанным на снимке экрана ниже.

![Выходные данные приложения DocumentDB C++](media/documentdb-cpp-get-started/docdbconsole.png)

Поздравляем! Вы ознакомились с руководством по C++ и создали первое консольное приложение DocumentDB.

## <a name="a-idgetsolutionaget-the-complete-c-tutorial-solution"></a><a id="GetSolution"></a>Получение завершенного решения C++ для этого руководства
Чтобы собрать решение GetStarted, содержащее все примеры из этой статьи, вам понадобится следующее:

* [учетная запись DocumentDB][documentdb-create-account];
* решение [GetStarted](https://github.com/stalker314314/DocumentDBCpp) , доступное в GitHub.

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как [контролировать учетную запись DocumentDB](documentdb-monitor-accounts.md).
* Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).
* Дополнительные сведения о модели программирования см. в разделе "Разработка" [на странице документации DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md






<!--HONumber=Dec16_HO2-->


