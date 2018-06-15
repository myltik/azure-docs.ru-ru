---
title: Начало работы с хранилищем таблиц Azure и подключенными службами Visual Studio (ASP.NET) | Документация Майкрософт
description: Начало работы с хранилищем таблиц Azure в проекте ASP.NET в Visual Studio при подключении к учетной записи хранения с помощью подключенных служб Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ms.openlocfilehash: 39456380769e1c3b790d2bbc6fdf9c04c983d054
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798637"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Начало работы с хранилищем таблиц Azure и подключенными службами Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Обзор

В табличном хранилище Azure можно хранить большие объемы структурированных данных. Эта служба — хранилище данных NoSQL, которое принимает вызовы внутри и снаружи облака Azure с проверкой подлинности. Таблицы Azure идеально подходят для хранения нереляционных структурированных данных.

В этом руководстве показано, как написать код ASP.NET для некоторых распространенных сценариев с использованием сущностей хранилища таблиц Azure. К этим сценариям относится создание таблицы, а также добавление, запрос и удаление сущностей таблиц. 

## <a name="prerequisites"></a>предварительным требованиям

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Учетная запись хранения Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Создание контроллера MVC 

1. В **обозревателе решений** щелкните правой кнопкой мыши **Контроллеры** и в контекстном меню выберите **Добавить -> Контроллер**.

    ![Добавление контроллера в приложение ASP.NET MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC5 — пустой** и нажмите кнопку **Добавить**.

    ![Выбор типа контроллера MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. В диалоговом окне **Добавление контроллера** назовите контроллер *TablesController* и нажмите кнопку **Добавить**.

    ![Назначение имени для контроллера MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Добавьте в файл `TablesController.cs` следующие директивы *using*:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Создание класса модели

Во многих примерах в этой статье используется производный класс **TableEntity** с именем **CustomerEntity**. Чтобы объявить этот класс как класс модели, сделайте следующее:

1. В **обозревателе решений** щелкните правой кнопкой мыши **Модели**, а затем в контекстном меню выберите **Добавить -> Класс**.

1. В диалоговом окне **Добавление нового элемента** присвойте классу имя **CustomerEntity**.

1. Откройте файл `CustomerEntity.cs` и добавьте в него следующую директиву **using**:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Измените класс таким образом, чтобы он был объявлен, как показано в коде ниже. Класс определяет класс сущностей с именем **CustomerEntity**, который использует имя клиента как ключ строки, а фамилию клиента — как ключ раздела.

    ```csharp
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }
    }
    ```

## <a name="create-a-table"></a>Создание таблицы

Чтобы создать таблицу, выполните приведенные ниже действия.

> [!NOTE]
> 
> К данному разделу можно переходить, выполнив все действия, описанные в разделе [Настройка среды разработки](#set-up-the-development-environment). 

1. Откройте файл `TablesController.cs` .

1. Добавьте метод **CreateTable**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. В рамках метода **CreateTable** получите объект **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Получите объект **CloudTableClient**, представляющий клиент службы таблиц.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Получите объект **CloudTable**, представляющий ссылку на требуемое имя таблицы. Метод **CloudBlobClient.GetContainerReference** не выполняет запрос в отношении хранилища таблиц. Ссылка возвращается независимо от существования таблицы. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Вызовите метод **CloudTable.CreateIfNotExists**, чтобы создать таблицу, если она еще не создана. Метод **CloudTable.CreateIfNotExists** возвращает значение**true**, если таблица не существует или успешно создана. В противном случае возвращается значение **false**.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Обновите **ViewBag** именем таблицы.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **Таблицы**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите **CreateTable** для имени представления и выберите **Добавить**.

1. Откройте файл `CreateTable.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Запустите приложение и выберите **Создание таблицы**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Создать таблицу](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Как упоминалось ранее, метод **CloudTable.CreateIfNotExists** возвращает значение **true**, только если таблица не существует или была создана. Таким образом, если вы запустите приложение, когда таблица существует, метод вернет значение **false**. Чтобы запустить приложение несколько раз, вам нужно удалить таблицу перед повторным запуском приложения. Чтобы удалить таблицу, используйте метод **CloudTable.Delete**. Ее также можно удалить с помощью [портала Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) или в [обозревателе хранилищ Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Добавление сущности в таблицу

*Сущности* сопоставляются с объектами С\# с помощью настраиваемого класса, производного от **TableEntity**. Чтобы добавить сущность в таблицу, создайте класс, который определяет свойства сущности. В этом разделе вы узнаете, как определить класс сущностей, который использует имя клиента как ключ строки, а фамилию клиента — как ключ раздела. Вместе ключ раздела и ключ строки сущности уникальным образом идентифицируют сущность в таблице. Сущности с одинаковым ключом раздела можно запрашивать быстрее, чем с разными ключами раздела, но использование различных ключей разделов обеспечивает более высокую масштабируемость параллельных операций. Любое свойство, которое будет храниться в службе таблиц, должно быть открытым свойством поддерживаемого типа, предоставляющим возможности задания и получения значений.
Класс сущностей *должен* объявлять конструктор без общедоступного параметра.

> [!NOTE]
> 
> К данному разделу можно переходить, выполнив все действия, описанные в разделе [Настройка среды разработки](#set-up-the-development-environment).

1. Откройте файл `TablesController.cs` .

1. Добавьте следующую директиву, чтобы код в файле `TablesController.cs` мог получить доступ к классу **CustomerEntity**:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Добавьте метод **AddEntity**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. В рамках метода **AddEntity** получите объект **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Получите объект **CloudTableClient**, представляющий клиент службы таблиц.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Получите объект **CloudTable**, представляющий ссылку на таблицу, в которую требуется добавить новую сущность. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Создайте и инициализируйте класс **CustomerEntity**.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Создайте объект **TableOperation**, который вставляет пользовательскую сущность.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Выполните операцию вставки, вызвав метод **CloudTable.Execute**. Результат операции можно узнать, проверив значение свойства **TableResult.HttpStatusCode**. Код состояния 2xx означает, что запрошенное клиентом действие обработано успешно. Например, успешная вставка новых сущностей выводит код состояния HTTP 204. Это значит, что операция обработана и сервер не вернул содержимое.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Обновите **ViewBag** именем таблицы и результатами операции вставки.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **Таблицы**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите **AddEntity** для имени представления и выберите **Добавить**.

1. Откройте файл `AddEntity.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Запустите приложение и выберите **Добавление сущности**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Добавление сущности](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Чтобы убедиться, что сущность была добавлена, следуйте действиям, описанным в разделе [Получение одной сущности](#get-a-single-entity). Вы также можете использовать [обозреватель службы хранилища Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md), чтобы просмотреть все сущности для ваших таблиц.

## <a name="add-a-batch-of-entities-to-a-table"></a>Добавление пакета сущностей в таблицу

Вы можете [добавлять сущности в таблицу не только по одной](#add-an-entity-to-a-table), но и в ходе пакетной операции. Добавление сущностей в пакете уменьшает число операций приема-передачи между кодом и службой таблиц Azure. Вы узнаете, как добавить в таблицу несколько сущностей за одну операцию вставки.

> [!NOTE]
> 
> К данному разделу можно переходить, выполнив все действия, описанные в разделе [Настройка среды разработки](#set-up-the-development-environment).

1. Откройте файл `TablesController.cs` .

1. Добавьте метод **AddEntities**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. В рамках метода **AddEntities** получите объект **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Получите объект **CloudTableClient**, представляющий клиент службы таблиц.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Получите объект **CloudTable**, представляющий ссылку на таблицу, в которую требуется добавить новые сущности. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Создайте экземпляры некоторых пользовательских объектов на основе класса модели **CustomerEntity**, представленного в разделе [Добавление сущности в таблицу](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Получите объект **TableBatchOperation**.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Добавьте сущности в объект пакетной операции вставки.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Выполните пакетную операцию вставки, вызвав метод **CloudTable.ExecuteBatch**.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. Метод **CloudTable.ExecuteBatch** возвращает список объектов **TableResult**, где каждый объект **TableResult** можно проверить, чтобы определить результат выполнения каждой отдельной операции. В этом примере список передается в представление, что позволяет отобразить в нем результаты каждой операции. 
 
    ```csharp
    return View(results);
    ```

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **Таблицы**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите **AddEntities** для имени представления и выберите **Добавить**.

1. Откройте файл `AddEntities.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Запустите приложение и выберите **Добавление сущностей**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Добавление сущностей](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Чтобы убедиться, что сущность была добавлена, следуйте действиям, описанным в разделе [Получение одной сущности](#get-a-single-entity). Вы также можете использовать [обозреватель службы хранилища Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md), чтобы просмотреть все сущности для ваших таблиц.

## <a name="get-a-single-entity"></a>Получение одной сущности

Из этого раздела вы узнаете, как получить одну сущность из таблицы, используя ключ строки сущности и ключ раздела. 

> [!NOTE]
> 
> К данному разделу можно переходить, убедившись, что вы выполнили все действия, описанные в разделе [Установка среды разработки](#set-up-the-development-environment). В этом разделе используются данные из раздела [Добавление пакета сущностей в таблицу](#add-a-batch-of-entities-to-a-table). 

1. Откройте файл `TablesController.cs` .

1. Добавьте метод **GetSingle**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. В рамках метода **GetSingle** получите объект **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Получите объект **CloudTableClient**, представляющий клиент службы таблиц.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Получите объект **CloudTable**, представляющий ссылку на таблицу, из которой вы получаете сущность. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Создайте объект операции извлечения, который принимает объект сущности, производный от **TableEntity**. Первый параметр — ключ раздела *partitionKey*, а второй — ключ строки *rowKey*. Используя класс **CustomerEntity** и данные, представленные в разделе [Добавление пакета сущностей в таблицу](#add-a-batch-of-entities-to-a-table), следующий фрагмент кода направляет запрос к таблице на получение сущности **CustomerEntity**, свойство *partitionKey* которой имеет значение Smith, а свойство *rowKey* — значение Ben.

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Выполните операцию извлечения.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Передайте результат в представление, чтобы его отобразить.

    ```csharp
    return View(result);
    ```

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **Таблицы**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите **GetSingle** для имени представления и выберите **Добавить**.

1. Откройте файл `GetSingle.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Запустите приложение и выберите **Получение одной сущности**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Получение одной сущности](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Получение всех сущностей в разделе

Как уже упоминалось в разделе [Добавление сущности в таблицу](#add-an-entity-to-a-table), сочетание ключа раздела и ключа строки однозначно идентифицирует сущность в таблице. Сущности с одним ключом раздела можно запрашивать быстрее, чем сущности с разными ключами раздела. В этом разделе вы узнаете, как запрашивать таблицу для всех сущностей из указанного раздела.  

> [!NOTE]
> 
> К данному разделу можно переходить, убедившись, что вы выполнили все действия, описанные в разделе [Установка среды разработки](#set-up-the-development-environment). В этом разделе используются данные из раздела [Добавление пакета сущностей в таблицу](#add-a-batch-of-entities-to-a-table). 

1. Откройте файл `TablesController.cs` .

1. Добавьте метод **GetPartition**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. В рамках метода **GetPartition** получите объект **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Получите объект **CloudTableClient**, представляющий клиент службы таблиц.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Получите объект **CloudTable**, представляющий ссылку на таблицу, из которой вы получаете сущности. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Создайте экземпляр объекта **TableQuery**, поместив запрос в предложение **Where**. Используя класс **CustomerEntity** и данные, представленные в разделе [Добавление пакета сущностей в таблицу](#add-a-batch-of-entities-to-a-table), следующий фрагмент кода направляет запрос к таблице на получение всех сущностей, свойство **PartitionKey** (фамилия пользователя) которых имеет значение Smith.

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. В цикле вызовите метод **CloudTable.ExecuteQuerySegmented**, передавая ему экземпляр объекта запроса, который вы создали на предыдущем шаге.  Метод **CloudTable.ExecuteQuerySegmented** возвращает объект **TableContinuationToken**. Этот объект будет иметь значение **null**, когда нет больше сущностей для извлечения. В цикле используйте другой цикл для перечисления возвращаемых сущностей. В следующем примере кода каждая возвращенная сущность добавляется в список. Когда цикл завершается, список передается в представление для отображения: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **Таблицы**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите **GetPartition** для имени представления и выберите **Добавить**.

1. Откройте файл `GetPartition.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Запустите приложение и выберите **Получение раздела**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Получение раздела](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Удаление сущности

В этом разделе показано, как удалить сущность из таблицы.

> [!NOTE]
> 
> К данному разделу можно переходить, убедившись, что вы выполнили все действия, описанные в разделе [Установка среды разработки](#set-up-the-development-environment). В этом разделе используются данные из раздела [Добавление пакета сущностей в таблицу](#add-a-batch-of-entities-to-a-table). 

1. Откройте файл `TablesController.cs` .

1. Добавьте метод **DeleteEntity**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. В рамках метода **DeleteEntity** получите объект **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Получите объект **CloudTableClient**, представляющий клиент службы таблиц.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Получите объект **CloudTable**, представляющий ссылку на таблицу, из которой вы удаляете сущность. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Создайте объект операции удаления, который принимает объект сущности, производный от **TableEntity**. В этом случае используется класс **CustomerEntity** и данные, представленные в разделе [Добавление пакета сущностей в таблицу](#add-a-batch-of-entities-to-a-table). **ETag** сущности должно быть присвоено допустимое значение.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Выполните операцию удаления.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Передайте результат в представление, чтобы его отобразить.

    ```csharp
    return View(result);
    ```

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **Таблицы**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите **DeleteEntity** для имени представления и выберите **Добавить**.

1. Откройте файл `DeleteEntity.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Запустите приложение и выберите **Удаление сущности**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Получение одной сущности](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Дополнительная информация
Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.

  * [Приступая к работе с хранилищем BLOB-объектов Azure и подключенными службами Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Начало работы с хранилищем очередей Azure и подключенными службами Visual Studio](../storage/vs-storage-aspnet-getting-started-queues.md)
