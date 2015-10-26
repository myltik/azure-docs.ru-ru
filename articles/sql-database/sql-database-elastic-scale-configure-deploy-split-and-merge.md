<properties
	title="Elastic database Split-Merge tool tutorial"
	pageTitle="Учебник по инструменту разбиения и объединения эластичной базы данных | Microsoft Azure"
	description="Разбиение и объединение с помощью инструментов эластичной базы данных"
	metaKeywords="elastic database tools, split and merge, Azure SQL Database sharding, elastic scale, splitting and merging elastic databases"
	services="sql-database" documentationCenter=""  
	manager="jeffreyg"
	authors="sidneyh"/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="sidneyh" />

# Учебник по инструменту разбиения и объединения эластичной базы данных

## Загрузка пакетов разбиения/объединения
1. Скачайте последнюю версию NuGet с сайта [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).
2. Откройте командную строку и перейдите в каталог, куда вы загрузили nuget.exe.
3. Скачайте последний пакет разбиения и объединения в текущем каталоге с помощью приведенной ниже команды: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge`.  

Приведенные выше действия выполняют загрузку файлов Split-Merge в текущий каталог. Файлы находятся в каталоге **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x**, где *x.x.xxx.x* отражает номер версии. Файлы службы разбиения и объединения находятся в подкаталоге **content\\splitmerge\\service**, а сценарии разбиения и объединения PowerShell (и необходимые библиотеки DLL клиентов) — в подкаталоге **content\\splitmerge\\powershell**.

## Предварительные требования

1. Создайте базу данных SQL Azure DB, которая будет использоваться в качестве базы данных состояния разбиения и объединения. Войдите на [портал Azure](https://ms.portal.azure.com). Создайте новую **Базу данных SQL**. Введите имя базы данных и создайте нового пользователя и пароль. Обязательно запишите имя и пароль для последующего использования.

2. Убедитесь, что сервер баз данных SQL Azure позволяет службам Azure подключиться к нему. На портале в колонке **Параметры брандмауэра** задайте для параметра **Разрешить доступ к службам Azure** значение **Вкл.**. Щелкните значок «Сохранить».

    ![Допустимые службы][1]

3. Создайте учетную запись хранения Azure, которая будет использоваться для вывода диагностических данных. Войдите на портал предварительной версии Azure. На левой панели выберите **Создать**, затем **Данные и хранилище** и **Хранилище**.

4. Создайте облачную службу Azure для размещения службы разбиения и объединения. Войдите на портал предварительной версии Azure. На левой панели выберите **Создать**, затем **Вычисления**, **Облачная служба** и **Создать**.


## Настройка службы разбиения и объединения

### Настройка службы разбиения и объединения

1. В папке, куда вы скачали файлы Split/Merge, создайте копию файла **ServiceConfiguration.Template.cscfg**, который поставляется вместе с **SplitMergeService.cspkg**, и назовите его **ServiceConfiguration.cscfg**.

2. Откройте в текстовом редакторе файл ServiceConfiguration.cscfg. Мы рекомендуем использовать Visual Studio, так как он проверяет входные данные, например формат отпечатков сертификатов.

3. Создайте новую базу данных или выберите существующую для хранения состояния операций разбиения и объединения и получите строку подключения к этой базе.

	**Важно.** В этот момент база данных состояний должна использовать латинское сопоставление (SQL\_Latin1\_General\_CP1\_CI\_AS). Дополнительные сведения см. в разделе [Имя сопоставления Windows (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).

	Строка подключения для базы данных SQL Azure обычно выглядит следующим образом:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    Введите эту строку подключения в CSCFG-файл в разделы ролей **SplitMergeWeb** и **SplitMergeWorker** в параметре ElasticScaleMetadata.

5.    Для роли **SplitMergeWorker** введите допустимую строку подключения к службе хранилища Azure для параметра **WorkerRoleSynchronizationStorageAccountConnectionString**.
        
### Настройка безопасности
Подробные указания по настройке безопасности службы см. в разделе [Настройка безопасности службы разбиения и объединения](sql-database-elastic-scale-split-merge-security-configuration.md).

В целях простого развертывание теста, подходящего для работы с этим учебником, для запуска службы необходимо выполнить минимальный набор шагов настройки. Выполнение следующих действий позволяет взаимодействовать со службой только одной машине или учетной записи.

### Создание самозаверяющего сертификата

Создайте новый каталог и из этого каталога выполните следующие команды в окне [командной строки разработчика для Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx):

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

Необходимо ввести пароль для защиты закрытого ключа. Введите надежный пароль и подтвердите его. После этого еще раз появится запрос на ввод пароля. В конце нажмите кнопку **Да**, чтобы импортировать сертификат в хранилище доверенных корневых сертификатов.

### Создание PFX-файла

Выполните следующую команду в том же окне, в котором была выполнена команда makecert; используйте тот же пароль, который использовался для создания сертификата:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### Импортируйте сертификат клиента в личное хранилище сертификатов.
1. В проводнике Windows дважды щелкните **MyCert.pfx**.
2. В **мастере импорта сертификатов** выберите **Текущий пользователь** и нажмите кнопку **Далее**.
3. Подтвердите путь к файлу и нажмите кнопку **Далее**.
4. Введите пароль, оставьте флажок **Включить все расширенные свойства** установленным и нажмите кнопку **Далее**.
5. Оставьте флажок **Автоматический выбор хранилища сертификатов[…]** установленным и нажмите кнопку **Далее**.
6. Нажмите кнопки **Готово** и **ОК**.

### Загрузите PFX-файл в облачную службу.

Войдите на [портал предварительной версии Azure](https://portal.azure.com).

1. Выберите **Облачные службы**.
2. Выберите облачную службу, созданную ранее для службы Split/Merge.
3. Щелкните **Сертификаты** в верхнем меню.
4. Щелкните **Передать** на нижней панели.
5. Выберите PFX-файл и введите тот же пароль, что и выше.
6. После завершения скопируйте отпечаток сертификата из новой записи в списке.

### Обновление файла конфигурации службы

Вставьте скопированный ранее отпечаток сертификата в атрибут thumbprint/value этих настроек. Для рабочей роли:

    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

Для веб-роли:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />


Пожалуйста, обратите внимание, что в рабочих развертываниях необходимо использовать отдельные сертификаты для ЦС, шифрования, сервера и клиента. Подробные указания см. в разделе [Конфигурация безопасности](sql-database-elastic-scale-split-merge-security-configuration.md).

### Развертывание службы разбиения и объединения

1. Войдите на [портал Azure](https://manage.windowsazure.com).
2. Щелкните вкладку **Облачные службы** слева и выберите облачную службу, созданную ранее.
3. Нажмите на кнопку **Панель мониторинга**.
4. Выберите промежуточную среду, а затем щелкните **Отправка нового промежуточного развертывания**.

    ![Промежуточная][3]

5. В диалоговом окне введите метку развертывания. Для «Пакет» и «Конфигурация» выберите «Из локальных файлов» и выберите файл **SplitMergeService.cspkg** и настроенный ранее CSCFG-файл.
6. Убедитесь, что флажок **Развернуть, даже если одна или несколько ролей содержат отдельный экземпляр** установлен.
7. Для начала развертывания нажмите справа внизу кнопку с галочкой. Это может занять несколько минут.

![Отправить][4]


## Устранение неполадок при развертывании

Если не удается подключить веб-роль, существует вероятность наличия проблемы с настройкой безопасности. Проверьте, что протокол SSL настроен, как описано выше.

Если не удается подключить рабочую роль, но веб-роль создана успешно, то, скорее всего, возникли неполадки при подключении к базе данных состояния, созданной ранее.

* Убедитесь, что строка подключения в CSCFG-файле задана верно.
* Проверьте, что сервер и база данных существуют, а идентификатор пользователя и пароль верны.
* Для баз данных SQL Azure строка подключения должна выглядеть следующим образом:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* Убедитесь, что имя сервера не начинается с ****https://**.
* Убедитесь, что сервер баз данных SQL Azure позволяет службам Azure подключиться к нему. Для этого перейдите по адресу https://manage.windowsazure.com, нажмите кнопку «База данных SQL» слева, щелкните «Серверы» в верхней части страницы и выберите сервер. Щелкните **Настройка** в верхней части страницы и убедитесь, что для параметра **Службы Azure** задано значение «Да». (См. раздел «Предварительные требования» в верхней части этой статьи.)

## Тестирование развертывания службы разбиения и объединения

### Подключение с помощью веб-браузера

Определите конечную веб-точку службы разбиения и объединения. Ее можно найти на портале управления Azure, открыв **панель мониторинга** облачной службы и посмотрев справа в разделе **URL-адрес узла**. Замените ****http://** на ****https://**, так как параметры безопасности по умолчанию отключают конечную точку HTTP. Откройте в браузере этот URL-адрес.

### Тестирование с помощью скриптов PowerShell

Развертывание и среду можно проверить путем запуска включенных примеров скриптов PowerShell.

Будут включены файлы скриптов:

1. **SetupSampleSplitMergeEnvironment.ps1** — задает уровень данных тестирования для разбиения и объединения (подробное описание см. в таблице ниже).
2. **ExecuteSampleSplitMerge.ps1** — выполняет операции тестирования на уровне данных (подробное описание см. в таблице ниже).
3. **GetMappings.ps1** — пример сценария верхнего уровня, который выводит текущее состояние сопоставления отдельных сегментов.
4. **ShardManagement.psm1** — вспомогательный сценарий, который выступает оболочкой для ShardManagement API.
5. **SqlDatabaseHelpers.psm1** — вспомогательный сценарий для создания баз данных SQL и управления ими.

<table style="width:100%">
  <tr>
    <th>Файл PowerShell</th>
    <th>Действия</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1. Создает базу данных диспетчера сопоставления сегментов.</td>
  </tr>
  <tr>
    <td>2. Создает 2 сегментированные базы данных.
  </tr>
  <tr>
    <td>3. Создает сопоставление сегментов для этих баз данных (удаляет любые существующие сопоставления сегментов в этих базах данных). </td>
  </tr>
  <tr>
    <td>4. Создает небольшой пример таблицы в обоих сегментах и заполняет эту таблицу сегментами.</td>
  </tr>
  <tr>
    <td>5. Объявляет SchemaInfo для сегментированной таблицы.</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>Файл PowerShell</th>
    <th>Действия</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1. Отправляет запрос разбиения к внешнему веб-серверу службы разбиения и объединения, которая отделяет половину данных из первого сегмента и переносит их во второй сегмент.</td>
  </tr>
  <tr>
    <td>2. Запрашивает у внешнего веб-сервера состояние запроса на разбиение и ожидает завершения запроса.</td>
  </tr>
  <tr>
    <td>3. Отправляет запрос на объединение внешнему веб-серверу службы разбиения и объединения, который перемещает данные из второго сегмента обратно в первый.</td>
  </tr>
  <tr>
    <td>4. Запрашивает у внешнего веб-сервера состояние запроса объединения и ожидает завершения запроса.</td>
  </tr>
</table>

##Проверка развертывания с помощью PowerShell

1.    Откройте новое окно PowerShell и перейдите в каталог, куда вы загрузили пакет разбиения и объединения, затем перейдите в каталог powershell.
2.    Создайте сервер базы данных SQL Azure (или выберите существующий сервер), где будут созданы диспетчер сопоставлений сегментов и сегменты.

    Примечание. Сценарий SetupSampleSplitMergeEnvironment.ps1 создает эти базы данных на том же сервере по умолчанию, чтобы упростить сценарий. Это не является ограничением службы разбиения и объединения.

    Имя для входа с аутентификацией SQL и доступом на чтение и запись для баз данных необходимы службе разбиения и объединения для перемещения данных и обновления сопоставления сегментов. Так как служба разбиения и объединения выполняется в облаке, она не поддерживает встроенную аутентификацию.

    Убедитесь, что сервер SQL Azure настроен на разрешение доступа для IP-адреса компьютера, на котором выполняются сценарии. Можно найти этот параметр на сервере SQL Azure (конфигурация — разрешенные IP-адреса).

3.    Выполните сценарий SetupSampleSplitMergeEnvironment.ps1 для создания примера среды.

    Выполнение этого сценария очистит все существующие структуры данных управления сопоставлением сегментов в базе данных диспетчера сопоставлений сегментов и сегментов. Может быть полезно выполнить сценарии повторно, если необходимо повторно инициализировать сопоставление сегментов или сегменты.

    Пример командной строки:

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

4.    Выполните сценарий Getmappings.ps1 для просмотра сопоставлений, которые в настоящее время существуют в образце среды.

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    Выполните сценарий ExecuteSampleSplitMerge.ps1 для выполнения операции разбиения (перенос половины данных из первого сегмента во второй), а затем операции объединения (перемещения данных обратно в первый сегмент). Если настроить SSL и оставить конечную точку http отключенной, убедитесь, что можно использовать вместо этого конечную точку https://.

    Пример командной строки:

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

    Если появляется ниже ошибка, то, скорее, возникла ошибка в сертификате конечной веб-точки. Попробуйте подключиться к конечной веб-точке с предпочитаемым браузером и проверьте, есть ли сообщение об ошибке сертификата.

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    Если выполнено успешно, результат должен выглядеть следующим образом.

        > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' –CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
        Sending split request
        Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
        Polling split-merge request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
        Sending merge request
        Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
        Polling request status. Press Ctrl-C to end
        Progress: 0% | Status: Queued | Details: [Informational] Queued request
        Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
        Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
        Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
        Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
        ...
        ...
        Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
        Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
        Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.

6.    Поэкспериментируйте с другими типами данных! Все эти сценарии принимают дополнительный параметр -ShardKeyType, который позволяет указать тип ключа. Значение по умолчанию – Int32, но можно также указать Int64, Guid или Binary.

## Создание собственных запросов

Службу можно использовать через веб-интерфейс или посредством импорта модуля PowerShell SplitMerge.psm1, отправляющего запросы через веб-роль.

Служба разбиения и объединения может перемещать данные в сегментированные и ссылочные таблицы. Сегментированная таблица содержит ключевой столбец сегментирования и различные строчные данные в каждом сегменте. Ссылочная таблица не сегментирована, поэтому содержит одни и те же строчные данные в каждом сегменте. Ссылочные таблицы полезны для данных, которые изменяются редко и используются для объединения (JOIN) в запросах с сегментированными таблицами.

Для выполнения операции разбиения-объединения необходимо объявить сегментированные и ссылочные таблицы, которые будут перемещены. Это осуществляется с помощью API **SchemaInfo**. Этот API находится в пространстве имен **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema**.

1.    Создайте для каждой сегментированной таблицы объект **ShardedTableInfo**, описывающий имя схемы родительской таблицы (необязательно, по умолчанию используется значение dbo), имя таблицы и имя столбца в таблице, которая содержит ключ сегментирования.
2.    Создайте для каждой ссылочной таблицы объект **ReferenceTableInfo**, описывающий имя схемы родительской таблицы (необязательно, по умолчанию используется значение dbo), и имя таблицы.
3.    Добавьте приведенные выше объекты TableInfo в новый объект **SchemaInfo**.
4.    Получите ссылку на объект **ShardMapManager** и вызовите **GetSchemaInfoCollection**.
5.    Добавьте **SchemaInfo** в **SchemaInfoCollection**, указав имя сопоставления сегмента.

Пример этого можно увидеть в сценарии SetupSampleSplitMergeEnvironment.ps1.

Обратите внимание, что служба разбиения и объединения не создает для вас целевую базу данных (или схему для любой таблицы в базе данных). Они должны быть созданы предварительно перед отправкой запроса в службу.


## Устранение неполадок
При выполнении образцов сценариев Powershell может появиться следующее сообщение:

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

Эта ошибка означает, что SSL-сертификат настроен неправильно. Следуйте инструкциям в разделе "Подключение с помощью веб-браузера".

Если не удается отправить запросы, может появиться такое сообщения:

 «[Исключение] System.Data.SqlClient.SqlException (0x80131904). Не удалось найти хранимую процедуру dbo.InsertRequest».

В этом случае проверьте файл конфигурации, в частности значение параметра **WorkerRoleSynchronizationStorageAccountConnectionString**. Эта ошибка обычно означает, что рабочей роли не удалось успешно инициализировать базу данных метаданных при первом использовании.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png
 

<!---HONumber=Oct15_HO3-->