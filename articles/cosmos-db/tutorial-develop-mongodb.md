---
title: Создание веб-приложения с помощью API для MongoDB базы данных Azure Cosmos DB | Документация Майкрософт
description: Руководство по базе данных Azure Cosmos DB, в котором создается веб-приложение базы данных с помощью API для MongoDB.
keywords: примеры mongodb
services: cosmos-db
author: AndrewHoh
manager: kfile
editor: ''
documentationcenter: ''
ms.assetid: 61a2ab3a-2fc3-4d49-a263-ed87c66628f6
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 76a8e19bacdbde938758bf41ed7f209521f513aa
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2018
---
# <a name="azure-cosmos-db-connect-to-a-mongodb-app-using-net"></a>База данных Azure Cosmos DB. Подключение к приложению MongoDB с помощью .NET

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования базы данных Azure Cosmos DB. 

В этом руководстве показано, как создать учетную запись базы данных Azure Cosmos DB на портале Azure, а также базу данных и коллекцию для хранения данных с помощью [API MongoDB](mongodb-introduction.md). 

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * создание учетной записи Azure Cosmos DB; 
> * обновили строку подключения;
> * создание приложения MongoDB на виртуальной машине. 


## <a name="create-a-database-account"></a>Создание учетной записи базы данных

Сначала создадим учетную запись базы данных Azure Cosmos DB на портале Azure.  

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

> [!TIP]
> * Уже есть учетная запись Azure Cosmos DB? В таком случае перейдите к [настройке решения Visual Studio](#SetupVS).
> * Если вы используете эмулятор Azure Cosmos DB, выполните действия, описанные в статье об [эмуляторе Azure Cosmos DB](local-emulator.md), чтобы настроить эмулятор и сразу перейти к [настройке решения Visual Studio](#SetupVS). 
>
>

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Обновление строки подключения

1. На портале Azure на странице **базы данных Azure Cosmos DB** выберите учетную запись API для MongoDB. 
2. На левой панели в колонке учетной записи щелкните **Быстрый запуск**. 
3. Выберите платформу (*драйвер .NET*, *Node.js*, *Java*, *Python* или *оболочка MongoDB*). Если соответствующего драйвера или средства нет в списке, не беспокойтесь, мы постоянно добавляем дополнительные фрагменты кода для подключения. 
4. Скопируйте и вставьте фрагмент кода в приложение MongoDB. Теперь все готово к работе.

## <a name="set-up-your-mongodb-app"></a>Настройка приложения MongoDB

Вы можете использовать руководство [Создание веб-приложения в Azure, которое подключается к базе данных MongoDB, работающей на виртуальной машине](../app-service/app-service-web-tutorial-nodejs-mongodb-app.md) (с минимальными изменениями), чтобы быстро настроить приложение MongoDB (локально или опубликовать его в веб-приложение Azure), подключающееся к учетной записи API для MongoDB.  

1. Выполните инструкции из руководства,  заменив код Dal.cs следующим фрагментом:

    ```csharp   
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using MyTaskListApp.Models;
    using MongoDB.Driver;
    using MongoDB.Bson;
    using System.Configuration;
    using System.Security.Authentication;
   
    namespace MyTaskListApp
    {
        public class Dal : IDisposable
        {
            //private MongoServer mongoServer = null;
            private bool disposed = false;
   
            // To do: update the connection string with the DNS name
            // or IP address of your server. 
            //For example, "mongodb://testlinux.cloudapp.net
            private string connectionString = "mongodb://localhost:27017";
            private string userName = "<your user name>";
            private string host = "<your host>";
            private string password = "<your password>";
   
            // This sample uses a database named "Tasks" and a 
            //collection named "TasksList".  The database and collection 
            //will be automatically created if they don't already exist.
            private string dbName = "Tasks";
            private string collectionName = "TasksList";
   
            // Default constructor.        
            public Dal()
            {
            }
   
            // Gets all Task items from the MongoDB server.        
            public List<MyTask> GetAllTasks()
            {
                try
                {
                    var collection = GetTasksCollection();
                    return collection.Find(new BsonDocument()).ToList();
                }
                catch (MongoConnectionException)
                {
                    return new List<MyTask>();
                }
            }
   
            // Creates a Task and inserts it into the collection in MongoDB.
            public void CreateTask(MyTask task)
            {
                var collection = GetTasksCollectionForEdit();
                try
                {
                    collection.InsertOne(task);
                }
                catch (MongoCommandException ex)
                {
                    string msg = ex.Message;
                }
            }
   
            private IMongoCollection<MyTask> GetTasksCollection()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
   
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            private IMongoCollection<MyTask> GetTasksCollectionForEdit()
            {
                MongoClientSettings settings = new MongoClientSettings();
                settings.Server = new MongoServerAddress(host, 10255);
                settings.UseSsl = true;
                settings.SslSettings = new SslSettings();
                settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                settings.Credentials = new List<MongoCredential>()
                {
                    new MongoCredential("SCRAM-SHA-1", identity, evidence)
                };
                MongoClient client = new MongoClient(settings);
                var database = client.GetDatabase(dbName);
                var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                return todoTaskCollection;
            }
   
            # region IDisposable
   
            public void Dispose()
            {
                this.Dispose(true);
                GC.SuppressFinalize(this);
            }
   
            protected virtual void Dispose(bool disposing)
            {
                if (!this.disposed)
                {
                    if (disposing)
                    {
                    }
                }
   
                this.disposed = true;
            }
   
            # endregion
        }
    }
    ```

2. Замените следующие переменные в файле Dal.cs параметрами учетной записи со страницы "Ключи"на портале Azure.

    ```csharp   
    private string userName = "<your user name>";
    private string host = "<your host>";
    private string password = "<your password>";
    ```

3. Пользуйтесь приложением!

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение дальше, удалите все ресурсы, созданные в ходе работы с этим руководством, на портале Azure, выполнив следующие действия. 

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите имя созданного ресурса. 
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите имя ресурса для удаления и щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * создание учетной записи Azure Cosmos DB; 
> * обновили строку подключения;
> * создали приложение MongoDB на виртуальной машине.

Вы можете перейти к следующему руководству и импортировать данные MongoDB в базу данных Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Перенос данных MongoDB в Azure Cosmos DB](mongodb-migrate.md)

