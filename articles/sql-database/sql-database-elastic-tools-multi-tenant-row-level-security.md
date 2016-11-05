---
title: Мультитенантные приложения со средствами эластичных баз данных и безопасностью на уровне строк
description: Узнайте, как использовать средства эластичных баз данных совместно с безопасностью на уровне строк для создания приложения с высокомасштабируемым уровнем данных в базе данных SQL Azure, поддерживающей мультитенантные сегменты.
metakeywords: azure sql database elastic tools multi tenant row level security rls
services: sql-database
documentationcenter: ''
manager: jhubbard
author: tmullaney

ms.service: sql-database
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2016
ms.author: thmullan;torsteng

---
# Мультитенантные приложения со средствами эластичных баз данных и безопасностью на уровне строк
[Средства эластичных баз данных](sql-database-elastic-scale-get-started.md) и [безопасность на уровне строк (RLS)](https://msdn.microsoft.com/library/dn765131) предоставляют мощный набор возможностей для гибкого и эффективного масштабирования уровня данных мультитенантного приложения с базой данных SQL Azure. Дополнительные сведения см. в статье [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).

В этой статье показано, как использовать эти технологии совместно, чтобы построить приложение с высокомасштабируемым уровнем данных, который поддерживает мультиканальные сегменты, используя **ADO.NET SqlClient** и/или **Entity Framework**.

* **Средства эластичных баз данных** позволяют разработчикам развернуть уровень данных приложения через стандартные методы сегментирования с помощью набора библиотек .NET и шаблонов служб Azure. Управление сегментами с использованием клиентской библиотеки эластичных баз данных помогает автоматизировать и упростить многие инфраструктурные задачи, которые обычно связаны с сегментированием. 
* **Безопасность на уровне строк** позволяет разработчикам хранить данные для нескольких клиентов в одной базе данных, а также отфильтровывать строки, не принадлежащие клиенту, выполняющему запрос, с помощью политик безопасности. Централизующая логика доступа с RLS внутри базы данных, а не внутри приложения, упрощает обслуживание и снижает вероятность возникновения ошибок по мере роста базы кода приложения. Для RLS требуется последняя версия [обновления для базы данных SQL Azure (версия 12)](sql-database-v12-whats-new.md).

При совместном использовании этих функций приложение может выиграть на снижении издержек и повышении эффективности за счет хранения данных для нескольких клиентов в одной базе данных сегментов. В то же время приложение по-прежнему обладает достаточной гибкостью, чтобы предоставлять изолированные, принадлежащие однотенантные сегменты для premium-клиентов, которым требуются более строгие гарантии производительности, поскольку мультитенантные сегменты не гарантируют равномерного распространения ресурсов среди клиентов.

Другими словами, API-интерфейсы [маршрутизации, управляемой данными](sql-database-elastic-scale-data-dependent-routing.md), для клиентской библиотеки эластичных баз данных автоматически подключают клиентов к нужной базе данных сегментов, которая содержит их ключ сегментирования (обычно "TenantId"). После подключения политика безопасности RLS внутри базы данных гарантирует, что клиентам доступны только те строки, которые содержат их TenantId. Предполагается, что все таблицы содержат столбец TenantId для обозначения того, какие строки принадлежат каждому из клиентов.

![Архитектура приложений для ведения блогов][1]

## Загрузка примера проекта
### Предварительные требования
* Используйте Visual Studio 2012 или более поздней версии. 
* Создайте три базы данных SQL Azure. 
* Скачайте пример проекта: [Elastic DB Tools for Azure SQL — Multi-Tenant Shards](http://go.microsoft.com/?linkid=9888163).
  * Заполните информацию по своим базам данных в начале файла **Program.cs**. 

Этот проект расширяет проект, описанный в [Elastic DB Tools for Azure SQL — Entity Framework Integration](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md), за счет добавления поддержки для баз данных с мультитенантными сегментами. Он создает простое консольное приложение для построения блогов и записей с помощью четырех клиентов и двух баз данных с мультитенантными сегментами, как это показано на схеме выше.

Создайте и запустите приложение. Запустится диспетчер карт сегментов для средств эластичных баз данных, выполните следующие проверки:

1. С помощью Entity Framework и LINQ, создайте новый блог, а затем отобразите все блоги для каждого клиента.
2. С помощью ADO.NET SqlClient отобразите все блоги для клиента.
3. Попробуйте вставить блог неправильного клиента, чтобы проверить, возникнет ли ошибка.  

Обратите внимание, что поскольку RLS пока не включена в базах данных сегментов, каждая из этих проверок выдает ошибки: клиенты видят блоги, которые им не принадлежат, а приложение не запрещается вставить блог неправильному клиенту. В оставшейся части этой статьи описано, как решить эти проблемы, применив изоляцию клиентов с помощью RLS. Существует два шага:

1. **Уровень приложений**. Измените код приложения, чтобы после открытия подключения в хранилище SESSION\_CONTEXT всегда использовалось текущее значение TenantId. В примере проекта данный шаг уже выполнен. 
2. **Уровень данных**. Создайте в каждой базе данных сегментов политику безопасности RLS, чтобы фильтровать строки на основе значения TenantId, хранимого в SESSION\_CONTEXT. Этот шаг необходимо выполнить для каждой базы данных сегментов; в противном случае строки мультитенантных сегментов фильтроваться не будут. 

## Шаг 1. Уровень приложений — выбор значения TenantId в SESSION\_CONTEXT
После установки соединения с базой данных сегментов с помощью API-интерфейса маршрутизации, управляемой данными, для клиентской библиотеки эластичных баз данных этому приложению по-прежнему необходимо указать базу данных, TenantId которой использует это подключение, чтобы отфильтровать строки, принадлежащих другим клиентам, с помощью политики безопасности RLS. Чтобы передать эту информацию, рекомендуется сохранить текущее значение TenantId для этого подключения в [SESSION\_CONTEXT](https://msdn.microsoft.com/library/mt590806.aspx). (Примечание. Также можно использовать [CONTEXT\_INFO](https://msdn.microsoft.com/library/ms180125.aspx), но SESSION\_CONTEXT — лучший вариант, так как это хранилище проще в использовании. Кроме того, оно возвращает значение NULL по умолчанию и поддерживает пары «ключ — значение».)

### Entity Framework
Для приложений, использующих Entity Framework, задать нужное значение в SESSION\_CONTEXT проще всего в рамках переопределения ElasticScaleContext, как описано в статье [Зависящая от данных маршрутизация с помощью EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Перед возвратом подключения, установленного с помощью зависящей от данных маршрутизации, просто создайте и выполните команду SqlCommand, которая устанавливает в SESSION\_CONTEXT для TenantId значение shardingKey, указанное для этого подключения. Таким образом, чтобы задать нужное значение в SESSION\_CONTEXT, необходимо написать код один раз.

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data dependent routing. This call will open a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call will fail for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
} 
// ... 
```

Теперь при каждом вызове ElasticScaleContext хранилище SESSION\_CONTEXT будет автоматически использоваться с указанным значением TenantId:

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
    {     
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
        foreach (var item in query)     
        {       
            Console.WriteLine(item.Name);     
        }   
    } 
}); 
```

### ADO.NET SqlClient
Для приложений, которые используют ADO.NET SqlClient, рекомендованный подход заключается в следующем. Вам нужно создать функцию-оболочку для ShardMap.OpenConnectionForKey(), которая перед возвратом подключения автоматически будет задавать в хранилище SESSION\_CONTEXT нужное значение ключа TenantId. Чтобы в хранилище SESSION\_CONTEXT всегда использовалось нужное значение, открывайте подключение только с помощью этой функции-оболочки.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that SESSION_CONTEXT is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs will be listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## Шаг 2. Уровень данных — создание политики безопасности на уровне строк
### Создание политики безопасности для фильтрации строк, доступных для всех клиентов
Теперь, когда перед выполнением запроса приложение задает в хранилище SESSION\_CONTEXT нужное текущее значение TenantId, политика безопасности RLS может фильтровать запросы и исключать строки с разным значением TenantId.

Политика RLS реализована в инструкциях T-SQL: определяемая пользователем функция определяет логику доступа, а политика безопасности связывает эту функцию с любым количеством таблиц. В рамках этого проекта функция просто проверяет, что к базе данных подключено приложение (а не какой-либо другой пользователь SQL), а значение TenantId в SESSION\_CONTEXT соответствует значению TenantId определенной строки. Предикат фильтрации отфильтрует для запросов SELECT, UPDATE и DELETE строки, соответствующие этим условиям. А предикат блокировки предотвратит использование в запросах INSERT и UPDATE строк, не соответствующих этим условиям. Если нужное значение в SESSION\_CONTEXT не задано, будет возвращено значение NULL, а видимые строки или строки, доступные для вставки, будут отсутствовать.

Чтобы включить RLS, выполните следующий запрос T-SQL на всех сегментах, использующих Visual Studio (SSDT), среду SSMS или сценарий PowerShell, которые включены в проект (либо, если вы используете [службу заданий эластичной базы данных](sql-database-elastic-jobs-overview.md), вы можете использовать ее для автоматизации выполнения этого запроса T-SQL на всех сегментах):

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
    RETURNS TABLE     
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult          
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [!TIP]
> В более сложных проектах, требующих добавления предиката в сотни таблиц, можно использовать вспомогательную хранимую процедуру, которая автоматически создает политику безопасности, добавляя предикат во все таблицы схемы. Подробнее можно узнать в статье [Применение безопасности на уровне строк ко всем таблицам — вспомогательный сценарий (блог)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).
> 
> 

Теперь, если вы снова запустите пример приложения, клиенты смогут просматривать только те строки, которые им принадлежат. Кроме того, приложение не сможет ни вставлять строки, принадлежащие клиентам, которые в настоящее время не подключены к базе данных сегментов, ни изменять значение TenantId в видимых строках. Если приложение пытается выполнить одно из этих действий, будет инициировано исключение DbUpdateException.

Если впоследствии вы добавите новую таблицу, просто измените политику безопасности с помощью запроса ALTER и добавьте в новую таблицу предикаты фильтрации и блокировки:

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable
GO 
```

### Добавление ограничений по умолчанию на автоматическое заполнение TenantId для команд INSERT
Для каждой таблицы при вставке строк можно установить стандартное ограничение на автоматическое заполнение ключа TenantId значением, в настоящее время хранимым в SESSION\_CONTEXT. Например:

```
-- Create default constraints to auto-populate TenantId with the value of SESSION_CONTEXT for inserts 
ALTER TABLE Blogs     
    ADD CONSTRAINT df_TenantId_Blogs      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO

ALTER TABLE Posts     
    ADD CONSTRAINT df_TenantId_Posts      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO 
```

Теперь приложению не требуется указывать TenantId при вставке строк:

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
        db.Blogs.Add(blog);     
        db.SaveChanges();   
    } 
}); 
```

> [!NOTE]
> Если вы используете ограничения по умолчанию для проекта Entity Framework, рекомендуется НЕ включать столбец TenantId в свою модель данных EF. Это объясняется тем, что запросы Entity Framework автоматически предоставляют значения по умолчанию. Эти значения переопределят применяемые к хранилищу SESSION\_CONTEXT ограничения по умолчанию, созданные в инструкциях T-SQL. Чтобы использовать ограничения по умолчанию, например в примере проекта, следует удалить TenantId из файла DataClasses.cs (и запустить Add-Migration в консоли диспетчера пакетов) и воспользоваться T-SQL, чтобы убедиться, что это поле существует только в таблицах базы данных. Таким образом, EF не будет автоматически предоставлять неверные значения по умолчанию при вставке данных.
> 
> 

### (Необязательно) Включение суперпользователя для получения доступа ко всем строкам
Некоторым приложениям требуется суперпользователь, который имеет доступ ко всем строкам. Например, чтобы включить создание отчетов по всем клиентам всех сегментов или чтобы выполнять операции разделения и объединения сегментов, для которых потребуется перемещение строк между базами данных. Для этого потребуется создать нового пользователя SQL (в данном случае суперпользователя) в каждой базе данных сегмента. Затем измените политику безопасности, внеся в нее новую функцию предиката, которая разрешит пользователю доступ ко всем строкам:

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### Обслуживание
* **Добавление новых сегментов**. Необходимо выполнить сценарий T-SQL, чтобы включить политику RLS для новых сегментов, иначе запросы в таких сегментах фильтроваться не будут.
* **Добавление новых таблиц**. Необходимо добавлять предикат фильтрации и блокировки в политику безопасности для всех сегментов каждый раз, когда создается новая таблица, иначе запросы в такой таблице фильтроваться не будут. Это действие можно автоматизировать с помощью триггера DDL, как это описано в статье [Автоматическое применение безопасности на уровне строк для вновь созданных таблиц (блог)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## Сводка
Средства эластичных баз данных и безопасность на уровне строк могут использоваться совместно для масштабирования уровня данных приложения благодаря поддержке мультитенантных и однотенантных сегментов. Мультитенантные сегменты могут использоваться для более эффективного хранения данных (особенно в случаях, где большое количество клиентов имеет лишь несколько строк данных), в то время как однотенантные сегменты — для поддержки premium-клиентов с более строгими требованиями к производительности и изоляции. Дополнительные сведения см. в [справочнике по безопасности на уровне строк](https://msdn.microsoft.com/library/dn765131).

## Дополнительные ресурсы
* [Что такое пул эластичных баз данных Azure](sql-database-elastic-pool.md)
* [Общие сведения о возможностях эластичных баз данных](sql-database-elastic-scale-introduction.md)
* [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Authentication in multitenant apps, using Azure AD and OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md) (Проверка подлинности в мультитенантных приложениях с помощью Azure AD и OpenID Connect)
* [Сведения о приложении Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## Вопросы и запросы на функции
Все возникшие вопросы задавайте на [форуме по базам данных SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted), а запросы новых функций оставляйте на [форуме отзывов и предложений по базам данных SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->



<!---HONumber=AcomDC_0615_2016-->