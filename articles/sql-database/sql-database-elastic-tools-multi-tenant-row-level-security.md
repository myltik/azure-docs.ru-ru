<properties 
	pageTitle="Мультитенантные приложения со средствами эластичных баз данных и безопасностью на уровне строк" 
	description="Узнайте, как использовать средства эластичных баз данных совместно с безопасностью на уровне строк для создания приложения с высокомасштабируемым уровнем данных в базе данных SQL Azure, поддерживающей мультитенантные сегменты." 
	services="sql-database" 
	documentationCenter=""  
	manager="jeffreyg" 
	authors="tmullaney"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/26/2015" 
	ms.author="thmullan;torsteng;sidneyh" />

# Мультитенантные приложения со средствами эластичных баз данных и безопасностью на уровне строк 

[Средства эластичных баз данных](sql-database-elastic-scale-get-started.md) и [безопасность на уровне строк (RLS)](https://msdn.microsoft.com/library/dn765131) предоставляют мощный набор возможностей для гибкого и эффективного масштабирования уровня данных мультитенантного приложения с базой данных SQL Azure. В этой статье показано, как использовать эти технологии совместно, чтобы построить приложение с высокомасштабируемым уровнем данных, который поддерживает мультиканальные сегменты, используя **ADO.NET SqlClient** и/или **Entity Framework**.

* **Средства эластичных баз данных** позволяют разработчикам развернуть уровень данных приложения через стандартные методы сегментирования с помощью набора библиотек .NET и шаблонов служб Azure. Управление сегментами с использованием клиентской библиотеки эластичных баз данных помогает автоматизировать и упростить многие инфраструктурные задачи, которые обычно связаны с сегментированием. 

* **Безопасность на уровне строк (предварительная версия)** позволяет разработчикам хранить данные для нескольких клиентов в той же базе данных, а также фильтровать строки, не принадлежащие клиенту, выполняющему запрос, с помощью политик безопасности. Централизующая логика доступа с RLS внутри базы данных, а не внутри приложения, упрощает обслуживание и снижает вероятность возникновения ошибок по мере роста базы кода приложения. Для RLS требуется последняя версия [обновления для базы данных SQL Azure (версия 12)](sql-database-preview-whats-new.md).

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

1. **Уровень приложения**. Измените код приложения, чтобы после установления подключения свойство CONTEXT\_INFO всегда имело текущее значение TenantId. В примере проекта данный шаг уже выполнен. 
2. **Уровень данных**. Создайте в каждой базе данных сегментов политику безопасности RLS, чтобы фильтровать строки, основанные на значении CONTEXT\_INFO. Этот шаг необходимо выполнить для каждой базы данных сегментов; в противном случае строки мультитенантных сегментов фильтроваться не будут. 


## Шаг 1. Уровень приложения. Установка CONTEXT\_INFO для TenantId

После установки соединения с базой данных сегментов с помощью API-интерфейса маршрутизации, управляемой данными, для клиентской библиотеки эластичных баз данных этому приложению по-прежнему необходимо указать базу данных, TenantId которой использует это подключение, чтобы отфильтровать строки, принадлежащих другим клиентам, с помощью политики безопасности RLS. Для передачи этой информации рекомендуется установить [CONTEXT\_INFO](https://msdn.microsoft.com/library/ms180125) в значение текущего TenantId этого соединения. Обратите внимание, что для базы данных SQL Azure свойству CONTEXT\_INFO заранее задается значение идентификатора сеанса GUID, и вы *должны* задать свойству CONTEXT\_INFO правильное значение TenantId, прежде чем выполнять запросы по новому подключению, чтобы случайно не потерять строки.

### Entity Framework

Для приложений, использующих Entity Framework, самый простой способ — это настроить CONTEXT\_INFO в рамках переопределения ElasticScaleContext, описанного в статье [Маршрутизация, управляемая данными, с помощью EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md/#data-dependent-routing-using-ef-dbcontext). Перед возвратом подключения, установленного с помощью маршрутизации, управляемой данными, просто создайте и выполните команду SqlCommand, которая устанавливает CONTEXT\_INFO в значение shardingKey (TenantId), указанное для данного соединения. Таким образом, чтобы установить CONTEXT\_INFO, вам необходимо написать код один раз.

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

        // Set CONTEXT_INFO to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SET CONTEXT_INFO @shardingKey";
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

Теперь CONTEXT\_INFO будет автоматически устанавливаться в значение указанного TenantdId при каждом вызове ElasticScaleContext:

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

Для приложений, которые используют ADO.NET SqlClient, рекомендованный подход заключается в следующем: создать функцию шифрования для ShardMap.OpenConnectionForKey(), которая автоматически задаст свойству CONTEXT\_INFO нужное значение TenantId, прежде чем создавать подключение. Чтобы гарантировать, что свойство CONTEXT\_INFO всегда имеет нужное значение, устанавливайте подключение только с помощью этой функции.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets CONTEXT_INFO to the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that CONTEXT_INFO is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set CONTEXT_INFO to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SET CONTEXT_INFO @shardingKey";
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

## Шаг 2. Уровень данных. Создание политики безопасности на уровне строк и ограничения 

### Создание политики безопасности для фильтрации запросов SELECT, UPDATE и DELETE 

Теперь, когда приложение устанавливает CONTEXT\_INFO в значение текущего TenantId перед выполнением запроса, политика безопасности RLS может фильтровать запросы и исключать строки, принадлежащие разным TenantId.

RLS реализована в T-SQL: функция-предикат, определяемая пользователем, описывает логику фильтрации, а политика безопасности связывает эту функцию с любым количеством таблиц. Для данного проекта функция-предикат просто проверяет, что приложение (а не какой-либо другой пользователь SQL) подключено к базе данных, а значение CONTEXT\_INFO соответствует TenantId данной строки. Строки, которые удовлетворяют этим условиям, будут отфильтрованы для запросов SELECT, UPDATE и DELETE. Если значение CONTEXT\_INFO не задано, не будет возвращено ни одной строки.

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
		AND CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) = @TenantId -- @TenantId (int) is 4 bytes 
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [AZURE.TIP]Для более сложных проектов, требующих добавления функции-предиката в сотни таблиц, можно использовать вспомогательную хранимую процедуру, которая автоматически создает политику безопасности, добавляя предикат во все таблицы схемы. Подробнее можно узнать в статье [Применение безопасности на уровне строк ко всем таблицам — вспомогательный сценарий (блог)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).

Если впоследствии вы добавите новую таблицу, просто измените политику безопасности с помощью команды ALTER и добавьте в новую таблицу предикат фильтра:

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable 
GO 
```

Теперь, если вы снова запустите пример приложения, клиенты не смогут просматривать строки, которые им не принадлежат.

### Добавление проверочных ограничений для блокировки команд INSERT и UPDATE от других клиентов

В настоящее время политики безопасности RLS не могут уберечь приложение от случайной вставки строк для неправильного TenantId или обновления TenantId отображаемой строки на новое значение. Для некоторых приложений, например для приложений отчетности, поддерживающих только чтение, это не проблема. Однако, поскольку это приложение позволяет клиентам вставлять новые блоги, целесообразно создать дополнительные средства защиты, которые будут вызывать ошибку, если код приложения ошибочно попытается вставить или обновить строки, нарушающие предикат фильтра. В статье [Безопасность на уровне строк: блокировка несанкционированных команд INSERT (блог)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/23/row-level-security-blocking-unauthorized-inserts) рекомендуется создать проверочное ограничение для каждой таблицы, чтобы принудительно установить тот же предикат фильтра RLS для операций вставки и обновления.

Чтобы добавить проверочные ограничения, выполните следующий запрос T-SQL на всех сегментах, использующих среду SSMS, SSDT или прилагаемый сценарий PowerShell (или задания эластичной базы данных), как это описано выше:

```
-- Create a scalar version of the predicate function for use in check constraints 
CREATE FUNCTION rls.fn_tenantAccessPredicateScalar(@TenantId int)     
	RETURNS bit 
AS     
	BEGIN     
		IF EXISTS( SELECT 1 FROM rls.fn_tenantAccessPredicate(@TenantId) )         
			RETURN 1     
		RETURN 0 
	END 
GO 

-- Add the function as a check constraint on all sharded tables 
ALTER TABLE Blogs     
	WITH NOCHECK -- don't check data already in table     
	ADD CONSTRAINT chk_blocking_Blogs -- needs a unique name     
	CHECK( rls.fn_tenantAccessPredicateScalar(TenantId) = 1 ) 
GO

ALTER TABLE Posts     
	WITH NOCHECK     
	ADD CONSTRAINT chk_blocking_Posts     
	CHECK( rls.fn_tenantAccessPredicateScalar(TenantId) = 1 ) 
GO 
```

Теперь приложение не сможет вставлять строки, принадлежащие клиентам, которые в настоящее время подключены к базе данных сегментов, другим клиентам. Кроме того, приложению не удастся обновлять видимые строки, чтобы получить другие значения TenantId. Если приложение пытается выполнить одно из этих действий, будет инициировано исключение DbUpdateException.


### Добавление ограничений по умолчанию на автоматическое заполнение TenantId для команд INSERT 

В дополнение к проверочным ограничениям на блокировку операций вставки не тем клиентом для каждой таблицы можно добавить ограничение по умолчанию на автоматическое заполнение TenantId текущим значением CONTEXT\_INFO при вставке строк. Например:

```
-- Create default constraints to auto-populate TenantId with the value of CONTEXT_INFO for inserts 
ALTER TABLE Blogs     
	ADD CONSTRAINT df_TenantId_Blogs      
	DEFAULT CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) FOR TenantId 
GO

ALTER TABLE Posts     
	ADD CONSTRAINT df_TenantId_Posts      
	DEFAULT CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) FOR TenantId 
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

> [AZURE.NOTE]Если вы используете ограничения по умолчанию для проекта Entity Framework, рекомендуется НЕ включать столбец TenantId в свою модель данных EF. Это объясняется тем, что запросы Entity Framework автоматически предоставляют значения по умолчанию, которые переопределят ограничения по умолчанию, созданные в T-SQL, использующем CONTEXT\_INFO. Чтобы использовать ограничения по умолчанию, например в примере проекта, следует удалить TenantId из файла DataClasses.cs (и запустить Add-Migration в консоли диспетчера пакетов) и воспользоваться T-SQL, чтобы убедиться, что это поле существует только в таблицах базы данных. Таким образом, EF не будет автоматически предоставлять неверные значения по умолчанию при вставке данных.

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
            AND CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### Обслуживание 

* **Добавление новых сегментов**. Чтобы включить RLS (и добавить проверочные ограничения) на любых новых сегментах, необходимо выполнить скрипт T-SQL, в противном случае запросы на этих сегментах фильтроваться не будут.

* **Добавление новых таблиц**.Необходимо добавлять предикат фильтра в политику безопасности на всех сегментах каждый раз, когда создается новая таблица, в противном случае запросы в новых таблицах фильтроваться не будут. Это действие можно автоматизировать с помощью триггера DDL, как это описано в статье [Автоматическое применение безопасности на уровне строк для вновь созданных таблиц (блог)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).


## Сводка 

Средства эластичных баз данных и безопасность на уровне строк могут использоваться совместно для масштабирования уровня данных приложения благодаря поддержке мультитенантных и однотенантных сегментов. Мультитенантные сегменты могут использоваться для более эффективного хранения данных (особенно в случаях, где большое количество клиентов имеет лишь несколько строк данных), в то время как однотенантные сегменты — для поддержки premium-клиентов с более строгими требованиями к производительности и изоляции. Дополнительные сведения можно узнать в статье [Карта документации для средств эластичных баз данных](sql-database-elastic-scale-documentation-map.md) или в [Справочник по безопасности на уровне строк](https://msdn.microsoft.com/library/dn765131) на сайте MSDN.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->

<!---HONumber=August15_HO6-->