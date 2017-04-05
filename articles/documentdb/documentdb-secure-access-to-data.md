---
title: "Как защитить доступ к данным в DocumentDB | Документация Майкрософт"
description: "Узнайте об основных понятиях управления доступом в DocumentDB, включая главные ключи, ключи только для чтения, пользователей и разрешения."
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 8641225d-e839-4ba6-a6fd-d6314ae3a51c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: e4cbc9b0c9532d56376c4fabebcde8c64cb0474b
ms.lasthandoff: 03/24/2017


---
# <a name="securing-access-to-documentdb-data"></a>Защита доступа к данным DocumentDB
В этой статье приведены общие сведения о защите доступа к данным, хранящимся в [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

Для аутентификации пользователей и предоставления доступа к своим данным и ресурсам DocumentDB использует два типа ключей. 

|Тип ключа|Ресурсы|
|---|---|
|[Главные ключи](#master-keys) |Используются для административных ресурсов: учетных записей базы данных, баз данных, пользователей и разрешений.|
|[Маркеры ресурсов](#resource-tokens)|Используются для ресурсов приложения: коллекций, документов, вложений, хранимых процедур, триггеров и определяемых пользователем функций (UDF).|

<a id="master-keys"></a>

## <a name="master-keys"></a>Главные ключи 

Главные ключи предоставляют доступ ко всем административным ресурсам для учетной записи базы данных. Главные ключи.  
- предоставляют доступ к учетным записям, базам данных, пользователям и разрешениям; 
- не могут использоваться для предоставления детального доступа к коллекциям и документам;
- создаются в процессе создания учетной записи;
- можно создать повторно в любое время.

Каждая учетная запись состоит из двух главных ключей: первичного и вторичного. Двойные ключи позволяют повторно создавать или сменять ключи, обеспечивая постоянный доступ к учетной записи и данным. 

Помимо двух главных ключей в учетной записи DocumentDB существует еще два ключа только для чтения. Эти ключи разрешают только операции чтения в учетной записи. Ключи только для чтения не предоставляют доступ к ресурсам разрешений на чтение.

Главные ключи (первичные, вторичные, с доступом только для чтения и с доступом для чтения и записи) можно получить и повторно создать с помощью портала Azure. Дополнительные сведения см. в разделе [Просмотр, копирование и повторное создание ключей доступа](documentdb-manage-account.md#a-idkeysaview-copy-and-regenerate-access-keys).

![Управление доступом (IAM) на портале Azure: демонстрация безопасности базы данных NoSQL](./media/documentdb-secure-access-to-data/nosql-database-security-master-key-portal.png)

Процесс ротации главного ключа прост. Перейдите на портал Azure, чтобы получить вторичный ключ, затем замените первичный ключ вторичным ключом в приложении, а затем смените первичный ключ на портале Azure.

![Ротация главного ключа на портале Azure: демонстрация безопасности базы данных NoSQL](./media/documentdb-secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Пример кода для использования главного ключа

В следующем примере кода показано, как использовать конечную точку учетной записи DocumentDB и главный ключ для создания экземпляра DocumentClient и построения новой базы данных. 

```csharp
//Read the DocumentDB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the NOSQL (DocumentDB) account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Маркеры ресурсов

Маркеры ресурсов предоставляют доступ к ресурсам приложения в базе данных. Маркеры ресурсов.
- предоставляют доступ к определенным коллекциям, ключам секции, документам, вложениям, хранимым процедурам, триггерам и определяемым пользователем функциям (UDF);
- создаются, когда [пользователю](#users) предоставляются [разрешения](#permissions) на доступ к определенному ресурсу;
- создаются повторно, когда в отношении ресурса разрешения выполняется вызов POST, GET или PUT;
- используют хэш, который маркер ресурса специально создал для пользователя, ресурса и разрешения;
- имеют ограничение по времени (настраиваемый срок действия). Допустимый интервал времени по умолчанию составляет один час. Однако продолжительность действия маркера можно задать явным образом. Ее значение не должно превышать пять часов;
- обеспечивают безопасную альтернативу выдаче главного ключа; 
- позволяют клиентам читать, записывать и удалять ресурсы в учетной записи DocumentDB в соответствии с предоставленными им разрешениями.

Маркер ресурсов можно использовать(создавая пользователей и разрешения DocumentDB), если доступ к ресурсам в учетной записи DocumentDB требуется предоставить клиенту, которому нельзя доверять.  

Маркеры ресурсов DocumentDB — это безопасное альтернативное решение, которое позволяет клиентам читать, записывать и удалять ресурсы в учетной записи DocumentDB согласно предоставленным разрешениям и без необходимости использовать главный ключ или ключ только для чтения.

Ниже приведена стандартная схема запроса, создания и отправки маркеров ресурсов клиентам.

1. Служба среднего уровня настроена для обслуживания мобильного приложения по обмену фотографиями. 
2. Служба среднего уровня обрабатывает главный ключ учетной записи DocumentDB.
3. Приложение по обмену фотографиями устанавливается на мобильных устройствах конечных пользователей. 
4. При входе в систему приложение для обмена фотографиями идентифицирует пользователя службы. Механизм установления личности пользователя реализуется исключительно в приложении.
5. После установления личности служба среднего уровня запрашивает разрешения на основе удостоверения.
6. Служба среднего уровня отправляет маркер ресурса обратно в приложение для телефона.
7. Приложение может по-прежнему использовать маркер ресурса для прямого доступа к ресурсам DocumentDB с разрешениями, определенными маркером, и в течение интервала времени, разрешенного этим маркером. 
8. По истечении срока действия маркера ресурса последующие запросы получают исключение: 401 — не авторизовано.  На этом этапе приложение для телефона повторно установит личность и запросит новый маркер ресурса.

    ![Рабочий процесс маркеров ресурсов DocumentDB](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

Создание маркеров ресурсов и управление ими осуществляется собственными клиентскими библиотеками DocumentDB. Однако, если используется REST, то необходимо создать заголовки запросов и аутентификации. Дополнительные сведения о создании заголовков аутентификации для REST см. в статье [Access Control on DocumentDB Resources](https://docs.microsoft.com/en-us/rest/api/documentdb/access-control-on-documentdb-resources) (Управление доступом к ресурсам DocumentDB) или в [репозитории исходного кода для наших пакетов SDK](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Пример службы среднего уровня, используемой для создания маркеров ресурсов или в качестве брокера, см. в [репозитории приложения ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Пользователи
Пользователи DocumentDB связаны с базой данных DocumentDB.  Каждая база данных может содержать несколько пользователей DocumentDB или не содержать ни одного.  В следующем примере кода показано, как создать ресурс пользователя DocumentDB.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Каждый пользователь DocumentDB обладает свойством PermissionsLink, которое можно использовать для получения списка [разрешений](#permissions), связанных с этим пользователем.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Разрешения
Ресурс разрешения DocumentDB связан с пользователем DocumentDB.  Каждый пользователь может иметь несколько разрешений DocumentDB или не иметь ни одного.  Ресурс разрешения предоставляет доступ к маркеру безопасности, необходимому пользователю при попытке доступа к конкретному ресурсу приложения.
Существует два уровня доступа, предоставляемых ресурсом разрешения:

* Все. Пользователь имеет полный набор разрешений для ресурса.
* Чтение. Пользователь может только считывать содержимое ресурса, но не может выполнять на ресурсе операции записи, обновления или удаления.

> [!NOTE]
> Для выполнения хранимых процедур DocumentDB пользователь должен иметь полные разрешения на использование коллекции, в которой будут выполняться хранимые процедуры.
> 
> 

### <a name="code-sample-to-create-permission"></a>Пример кода для создания разрешения

В следующем примере кода показано, как создать ресурс разрешения, прочесть его маркер и связать разрешения с созданным выше [пользователем](#users).

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = documentCollection.SelfLink,
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Если вы указали ключ секции для коллекции, то разрешение для ресурсов коллекции, документа и вложения также должно включать ResourcePartitionKey наряду с ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Пример кода для чтения разрешений пользователя

Чтобы получить все ресурсы разрешений, связанные с конкретным пользователем, DocumentDB предоставляет каждому объекту пользователя доступ к каналу разрешений.  В следующем фрагменте кода показано, как получить разрешение, связанное с созданным выше пользователем, сформировать список разрешений и создать новый DocumentClient от имени пользователя.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о безопасности базы данных DocumentDB см. в статье [DocumentDB: безопасность базы данных NoSQL](documentdb-nosql-database-security.md).
* Сведения об управлении главными ключами и ключами только для чтения см. в статье [Управление учетной записью DocumentDB](documentdb-manage-account.md#a-idkeysaview-copy-and-regenerate-access-keys).
* Сведения о создании маркеров авторизации DocumentDB см. в статье [Access Control on DocumentDB Resources](https://docs.microsoft.com/en-us/rest/api/documentdb/access-control-on-documentdb-resources) (Управление доступом к ресурсам DocumentDB).

