<properties 
	pageTitle="Маршрутизация, зависящая от данных | Microsoft Azure" 
	description="Как использовать класс ShardMapManager в приложениях .NET для маршрутизации, зависящей от данных — функции эластичных баз данных для базы данных SQL Azure" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/03/2016" 
	ms.author="torsteng;sidneyh"/>

#Маршрутизация, зависящая от данных

**Маршрутизация на основе данных** — это возможность использования данных в запросе для направления запроса к соответствующей базе данных. Это основная модель при работе с сегментированными базами данных. Контекст запроса может также использоваться для маршрутизации запроса, особенно в том случае, если ключ сегментирования не является частью запроса. Каждый конкретный запрос или транзакция в приложении, которое использует зависящую от данных маршрутизацию, ограничены обращением только к одной базе данных за раз. Для средств эластичной базы данных SQL Azure эта маршрутизация выполняется с помощью **[класса ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx)** в приложениях ADO.NET.

Приложение не должно отслеживать различные строки подключения или расположения базы данных, связанные с различными срезами данных в сегментированной среде. Вместо этого [диспетчер сопоставления сегментов](sql-database-elastic-scale-shard-map-management.md) при необходимости открывает подключения к соответствующим базам данных на основе данных в сопоставлении сегментов и значения ключа сегментирования, которое является целевым объектом запроса приложения. (Этим ключом обычно является *customer\_id*, *tenant\_id*, *date\_key* или другой конкретный идентификатор, являющийся основным параметром запроса к базе данных.)

Дополнительные сведения см. в разделе [Масштабирование SQL Server с помощью маршрутизации на основе данных](https://technet.microsoft.com/library/cc966448.aspx).

## Скачивание клиентской библиотеки

Для получения класса установите [клиентскую библиотеку эластичной базы данных](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## Использование ShardMapManager при маршрутизации с зависимостью от данных 

Приложения должны получить объект **ShardMapManager** во время инициализации, используя вызов фабрики **[GetSQLShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx)**. В данном примере инициализируются как диспетчер **ShardMapManager**, так и конкретное сопоставление **ShardMap**, которое он содержит. В этом примере показаны методы GetSqlShardMapManager и [GetRangeShardMap](https://msdn.microsoft.com/library/azure/dn824173.aspx).

    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnnectionString, 
                      ShardMapManagerLoadPolicy.Lazy);
    RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 

### Для получения карты сегментов используйте учетную запись с минимально возможными привилегиями.

Если приложение не работает с картой сегментов, учетные данные, используемые в методе фабрики, должны иметь разрешения только для чтения в базе данных **глобальной карты сегментов**. Обычно эти учетные данные отличаются от учетных данных, используемых в открытых подключениях к диспетчеру карты сегментов. Также см. раздел [Учетные данные для доступа к клиентской библиотеке эластичной базы данных](sql-database-elastic-scale-manage-credentials.md).

## Вызовите метод OpenConnectionForKey.

Метод **[ShardMap.OpenConnectionForKey ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx)** возвращает подключение к ADO.Net, готовое к выдаче команд в соответствующей базе данных на основе значения параметра **key**. Информация о сегментации кэшируется в приложении с помощью диспетчера **ShardMapManager**, поэтому такие запросы обычно не сопровождаются поиском в базе данных **Глобальная карта сегментов**.

	// Syntax: 
	public SqlConnection OpenConnectionForKey<TKey>(
		TKey key,
		string connectionString,
		ConnectionOptions options
	)


* Параметр **key** используется как ключ поиска в карте сегментов для определения соответствующей базы данных для запроса. 

* Строка **connectionString** используется для передачи только учетных данных пользователя для необходимого подключения. Имя базы данных и имя сервера не включаются в эту строку *connectionString*, так как метод будет определять базу данных и сервер с помощью сопоставления **ShardMap**.

* Если среда, в которой находятся сегменты, может измениться и строки могут быть перемещены в другие базы данных в результате операций разбиения или слияния, то значение параметра **[ConnectionOptions](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions.aspx)** необходимо установить в **ConnectionOptions.Validate**. Это включает краткий запрос для сопоставления локальной карты сегментов в целевой базе данных (не на глобальной карте сегментов), прежде чем подключение предоставляется приложению.

При наличии сбоя в проверке локальной карты сегментов (что означает неправильный кэш) диспетчер карты сегментов запросит глобальную карту сегментирования для получения новых правильных значений для поиска, обновления кэша и возврата к подключению к соответствующей базе данных.

Используйте **ConnectionOptions.None** только в том случае, если внесение изменений в сопоставление сегментов не предполагается, т. е. когда приложение подключено к сети. В этом случае можно предположить, что кэшированные значения всегда являются правильными, и дополнительный проверочный вызов в два конца к целевой базе данных можно безопасно пропустить. Это позволяет снизить объем трафика базы данных. Также можно задать параметры **connectionOptions** через значение в файле конфигурации, указывающее, ожидаются ли изменения сегментов в течение определенного периода времени.

В этом примере используется значение целочисленного ключа **CustomerID** с использованием объекта **ShardMap** с именем **customerShardMap**.

    int customerId = 12345; 
    int newPersonId = 4321; 

    // Connect to the shard for that customer ID. No need to call a SqlConnection 
	// constructor followed by the Open method.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, 
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
    { 
        // Execute a simple command. 
        SqlCommand cmd = conn.CreateCommand(); 
        cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

        cmd.Parameters.AddWithValue("@customerID", customerId); 
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
        cmd.ExecuteNonQuery(); 
    }  

Метод **OpenConnectionForKey** возвращает новое, уже открытое подключение к нужной базе данных. Подключения, используемые в этом случае, по-прежнему имеют все преимущества объединенных подключений ADO.Net. При условии, что запросы и транзакции, могут выполняться с одного сегмента одновременно, это должно быть единственным необходимым изменением в приложении, уже работающем с ADO.Net.

Метод **[OpenConnectionForKeyAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync.aspx)** также доступен, если в приложении используется асинхронное программирование с помощью ADO.Net. Его поведение представляет собой маршрутизацию, зависящую от данных, которая эквивалентна методу ****[Connection.OpenAsync](https://msdn.microsoft.com/library/hh223688(v=vs.110).aspx)** в ADO.NET.

## Интеграция с обработкой временных сбоев 

При разработке облачных приложений для доступа к данным рекомендуется убедиться, что временные сбои перехватываются приложением и что попытка выполнения операции осуществляется несколько раз, прежде чем возникнет сообщение об ошибке. Обработка временных сбоев в облачных приложениях рассматривается в разделе [Обработка временных сбоев](https://msdn.microsoft.com/library/dn440719(v=pandp.60).aspx)
 
Обработка временных сбоев может естественным образом сосуществовать с шаблоном маршрутизации, управляемой данными. Основным требованием является повтор всего запроса доступа к данным, включая блок **using**, с которым осуществляется подключение с маршрутизацией, зависящей от данных. Приведенный выше пример можно переписать следующим образом (обратите внимание на выделенные изменения).

### Пример: маршрутизация, управляемая данными, с обработкой временных сбоев 

<pre><code>int customerId = 12345; 
int newPersonId = 4321; 

<span style="background-color:  #FFFF00">Configuration.SqlRetryPolicy.ExecuteAction(() => </span> 
<span style="background-color:  #FFFF00">    { </span>
        // Подключение к сегменту для идентификатора клиента 
        using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId,  
        Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate)) 
        { 
            // Execute a simple command 
            SqlCommand cmd = conn.CreateCommand(); 

            cmd.CommandText = @"UPDATE Sales.Customer 
                            SET PersonID = @newPersonID 
                            WHERE CustomerID = @customerID"; 

            cmd.Parameters.AddWithValue("@customerID", customerId); 
            cmd.Parameters.AddWithValue("@newPersonID", newPersonId); 
            cmd.ExecuteNonQuery(); 

            Console.WriteLine(Update completed); 
        } 
<span style="background-color:  #FFFF00">    }); </span> 
</code></pre>


Пакеты, необходимые для реализации обработки временных сбоев, автоматически загружаются при построении примера приложения эластичной базы данных. Пакеты также доступны по отдельности в библиотеке [Enterprise Library — блок приложений для обработки временных сбоев](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/). Используйте версию 6.0 или более позднюю.

## Согласованность транзакций 

Свойства транзакций сохраняются для всех операций, являющихся локальными для сегмента. Например, транзакции, осуществленные посредством маршрутизации, зависящей от данных, выполняются в области целевого сегмента для подключения. В данный момент нет возможности связывания нескольких подключений с транзакцией, поэтому невозможно гарантировать осуществление транзакций для операций, выполняемых через сегменты.

## Дальнейшие действия
Для отсоединения сегмента или для его повторного присоединения см. раздел [Решение проблем карты сегментов с помощью класса RecoveryManager](sql-database-elastic-database-recovery-manager.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=AcomDC_0504_2016-->