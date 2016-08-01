<properties 
	pageTitle="Примеры DocumentDB для MongoDB | Microsoft Azure" 
	description="Примеры поддержки протокола DocumentDB для MongoDB." 
	keywords="примеры mongodb"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

# Примеры поддержки протокола DocumentDB для MongoDB
Чтобы использовать эти примеры, требуется следующее:

- [Создать](documentdb-create-mongodb-account.md) учетную запись Azure DocumentDB с поддержкой протокола MongoDB.
- Получить учетную запись DocumentDB с поддержкой протокола и данных о [строке подключения](documentdb-connect-mongodb-account.md) MongoDB.

## Приступая к работе с примером приложения списка задач ASP.NET MVC

Вы можете использовать руководство [Создание веб-приложения в Azure, которое подключается к базе данных MongoDB, работающей на виртуальной машине](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md) (с минимальными изменениями), чтобы быстро настроить приложение MongoDB (локально или опубликовать его в веб-приложение Azure), подключающееся к учетной записи DocumentDB с поддержкой протокола MongoDB.

1. Выполните инструкции из руководства, заменив код Dal.cs следующим фрагментом:
	
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
            		settings.Server = new MongoServerAddress(host, 10250);
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
            		settings.Server = new MongoServerAddress(host, 10250);
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

2.	Замените следующие переменные в файле Dal.cs параметрами учетной записи:

        private string userName = "<your user name>";
    	private string host = "<your host>";
        private string password = "<your password>";

3. Пользуйтесь приложением!

## Дальнейшие действия

- Сведения о DocumentDB с поддержкой протокола MongoDB см. в [предварительной версии руководства по разработке](documentdb-mongodb-guidelines.md).
- Узнайте, как [использовать MongoChef](documentdb-mongodb-mongochef.md) с учетной записью DocumentDB с поддержкой протокола MongoDB.

 

<!---HONumber=AcomDC_0720_2016-->