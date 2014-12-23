<properties title="Split and Merge Service Tutorial" pageTitle="Учебник по службе Azure SQL Split-Merge (разбиения и слияния)" description="Splitting and Merging with Elastic Scale" metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Учебник по службе Elastic Scale Split-Merge

## Загрузите пакеты Split-Merge 
1. Загрузите последнюю версию NuGet из [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget). 
2. Откройте командную строку и перейдите в каталог, куда вы загрузили nuget.exe. 
3. Загрузите последний пакет Split-Merge в текущем каталоге с приведенной ниже командой: 
nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge  

Приведенные выше действия выполняют загрузку файлов Split-Merge в текущий каталог. Файлы находятся в каталоге **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x**, где *x.x.xxx.x* отражает номер версии. Файлы службы Split-Merge находятся в подкаталоге **content\splitmerge\service**, а скрипты Split-Merge PowerShell (и необходимые библиотеки DLL клиентов) - в подкаталоге **content\splitmerge\powershell**.

##Предварительные требования 

1. Создайте базу данных Azure SQL DB, которая будет использоваться в качестве базы данных состояния Split-Merge. Перейдите на [портал управления Azure](https://manage.windowsazure.com). В нижнем левом углу выберите **Создать**, щелкните **Служба данных** -> **База данных SQL** -> **Настраиваемое создание**. Введите имя базы данных и создайте нового пользователя и пароль. Обязательно запишите имя и пароль для последующего использования.

2. Убедитесь, что сервер баз данных SQL Azure позволяет службам Windows Azure подключиться к нему. Перейдите на [портал управления Azure](https://manage.windowsazure.com), на левой панели щелкните **базы данных SQL**. На ленте в верхней части экрана щелкните **Серверы** и выберите сервер. Сверху щелкните **Настройка** и убедитесь, что в параметре **Службы Windows Azure** задано значение **Да**.

    ![Allowed services][1]

3. Создайте учетную запись хранения Azure, которая будет использоваться для вывода диагностических данных. Перейдите на [портал управления Azure](https://manage.windowsazure.com). В нижнем левом углу выберите **Создать**, щелкните **Служба данных**, **Хранилище**, затем **Быстрое создание**. 

4. Создание облачной службы Azure для размещения службы Split-Merge.  Перейдите на [портал управления Azure](https://manage.windowsazure.com). В нижнем левом углу выберите **Создать**, затем **Вычисления**, **Облачная служба** и **Быстрое создание**. 


## Настройка службы Split-Merge 

### Конфигурация службы Split-Merge 

1. В папке, куда вы загрузили файлы Split/Merge, создайте копию файла **ServiceConfiguration.Template.cscfg**, который поставляется вместе с **SplitMergeService.cspkg**, и назовите его **ServiceConfiguration.cscfg**.

2. Откройте в текстовом редакторе файл ServiceConfiguration.cscfg. Мы рекомендуем использовать Visual Studio, так как он проверяет входные данные, например формат отпечатков сертификатов. 

3. Создайте новую базу данных или выберите существующую для хранения состояния операций Split/Merge и получите строку подключения к этой базе. Строка подключения для базы данных SQL Azure обычно имеет вид:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    Введите эту строку подключения в файл cscfg в разделы ролей **SplitMergeWeb** и **SplitMergeWorker** в параметре ElasticScaleMetadata.

5.    Настройка целевого объекта для журналов диагностики требует учетной записи хранения в Azure. Конфигурация для Split/Merge требует наличия строки подключения к вашей учетной записи хранения Azure. Строка подключения должна иметь следующий вид:

        "DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccessKey" 
    
Для определения ключа доступа перейдите на [портал управления Azure](https://manage.windowsazure.com), щелкните левую вкладку **Хранение**, выберите имя, соответствующее учетной записи хранения, и щелкните внизу **Управление ключами доступа**. Нажмите кнопку **копировать** для **первичного ключа доступа**.

![manage access keys][2]

6.    Введите имя учетной записи хранения и один из ключей доступа, предоставленный в прототипах в строке подключения к хранилищу. Эта строка подключения используется в разделе роли **SplitMergeWeb** и **SplitMergeWorker** в параметре **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. Для каждой роли можно использовать другую учетную запись хранения. 

### Настройка безопасности 
Подробные инструкции по настройке безопасности службы см. в разделе [Настройки безопасности Elastic Scale](./sql-database-elastic-scale-configure-security.md).

В целях простого развертывание теста, подходящего для работы с этим учебником, для запуска службы необходимо выполнить минимальный набор шагов настройки. Выполнение следующих действий позволяет взаимодействовать со службой только одной машине или учетной записи.

### Создание самозаверяющего сертификата 

Создайте новый каталог и из этого каталога выполните следующие команды в окне [командной строки разработчика для Visual Studio](http://msdn.microsoft.com/ru-ru/library/ms229859.aspx):

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

Необходимо ввести пароль для защиты закрытого ключа. Введите надежный пароль и подтвердите его. После этого еще раз появится запрос на ввод пароля. В конце нажмите кнопку **Да**, чтобы импортировать сертификат в хранилище доверенных корневых сертификатов.

###    Создание PFX-файла 

Выполните следующую команду в том же окне, в котором была выполнена команда makecert; используйте тот же пароль, который использовался для создания сертификата:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### Импорт сертификата клиента в личное хранилище сертификатов
1. В проводнике Windows дважды щелкните **MyCert.pfx**.
2. В **мастере импорта сертификатов** выберите **Текущий пользователь** и нажмите кнопку **Далее**.
3. Подтвердите путь к файлу и нажмите кнопку **Далее**.
4. Введите пароль, оставьте флажок **Включить все расширенные свойства** установленным и нажмите кнопку **Далее**.
5. Оставьте флажок **Автоматический выбор хранилища сертификатов[...]** установленным и нажмите кнопку **Далее**.
6. Нажмите кнопки **Готово** и **ОК**.

### Загрузите PFX-файл в облачную службу

Перейдите на [портал управления Azure](https://manage.windowsazure.com).

1. Выберите **Облачные службы**.
2. Выберите облачную службу, созданную ранее для службы Split/Merge.
3. В верхнем меню щелкните **Сертификаты**.
4. На нижней панели щелкните **Отправить**.
5. Выберите PFX-файл и введите тот же пароль, что и выше.
6. После завершения скопируйте отпечаток сертификата из новой записи в списке.

### Обновление файла конфигурации службы

Вставьте скопированный ранее отпечаток сертификата в атрибут thumbprint/value этих настроек:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Пожалуйста, обратите внимание, что в рабочих развертываниях необходимо использовать отдельные сертификаты для ЦС, сертификата сервера и сертификатов клиента. Подробные инструкции см. в разделе [Конфигурация безопасности](./sql-database-elastic-scale-configure-security.md).

### Развертывание службы Split-Merge
1. Перейдите на [портал управления Azure](https://manage.windowsazure.com).
2. Щелкните левую вкладку **Облачные службы** и выберите облачную службу, созданную ранее. 
3. Щелкните элемент **Панель мониторинга**. 
4. Выберите промежуточную среду, а затем щелкните **Отправка нового промежуточного развертывания**.

    ![Staging][3]

5. В диалоговом окне введите метку развертывания. Для "Пакет" и "Конфигурация" выберите "Из локальных файлов" и выберите файл **SplitMergeService.cspkg** и настроенный ранее CSCFG-файл.
6. Убедитесь, что флажок **Развернуть, даже если одна или несколько ролей содержат отдельный экземпляр** установлен.
7. Для начала развертывания нажмите справа внизу кнопку с галочкой. Это может занять несколько минут.

![Upload][4]


## Устранение неполадок при развертывании
Если не удается подключить веб-роль, существует вероятность наличия проблемы с настройкой безопасности. Проверьте, что протокол SSL настроен, как описано выше.

Если не удается подключить рабочую роль, но веб-роль создана успешно, то, скорее всего, возникли неполадки при подключении к базе данных состояния, созданной ранее. 

* Убедитесь, что строка подключения в файле CSCFG задана верно. 
* Проверьте, что сервер и база данных существуют, а идентификатор пользователя и пароль верны.
* Для баз данных SQL Azure строка подключения должна иметь следующий вид: 

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* Убедитесь, что имя сервера не начинается с **https://**.
* Убедитесь, что сервер баз данных SQL Azure позволяет службам Windows Azure подключиться к нему. Для этого откройте https://manage.windowsazure.com, нажмите кнопку слева "База данных SQL", вверху щелкните "Серверы" и выберите сервер. Щелкните сверху **Настройка** и убедитесь, что в параметре **Службы Windows Azure** стоит значение "Да". (См. раздел "Предварительные требования" в верхней части этой статьи.)

* Просмотрите журналы диагностики для экземпляра службы Split/Merge. Откройте экземпляр Visual Studio и в строке меню щелкните **Вид**, затем **Обозреватель серверов**. Щелкните значок **Windows Azure** для подключения к подписке Azure. Перейдите в Windows Azure -> Хранилище -> <ваша учетная запись хранения> -> Таблицы -> WADLogsTable. Дополнительные сведения см. в разделе [Обзор ресурсов хранилища с помощью обозревателя серверов](http://msdn.microsoft.com/ru-ru/library/azure/ff683677.aspx) 

    ![][5]

    ![][6]

## Тестирование развертывания службы Split-Merge
### Подключение с помощью веб-браузера

Определите конечную веб-точку службы Split-Merge. Ее можно найти на портале управления Azure, открыв **панель мониторинга** облачной службы и посмотрев справа в разделе **URL-адрес узла**. Замените **http://** на **https://**, так как параметры безопасности по умолчанию отключают конечную точку HTTP. Откройте в браузере этот URL-адрес.

### Тестирование с помощью скриптов PowerShell

Развертывание и среду можно проверить путем запуска включенных примеров скриптов PowerShell.

Будут включены файлы скриптов:

1. **SetupSampleSplitMergeEnvironment.ps1** - задает уровень данных тестирования для Split/Merge (подробное описание см. в таблице ниже)
2. **ExecuteSampleSplitMerge.ps1** - выполняет операции тестирования на уровне данных (подробное описание см. в таблице ниже)
3. **GetMappings.ps1** - образец сценария верхнего уровня, который выводит текущее состояние сопоставления сегментов
4. **ShardManagement.psm1** - вспомогательный сценарий, который выступает оболочкой для ShardManagement API
5. **SqlDatabaseHelpers.psm1** - вспомогательный сценарий для создания баз данных SQL и управления ими

<table style="width:100%">
  <tr>
    <th>Файл PowerShell</th>
    <th>Действия</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1.    Создает базу данных диспетчера сопоставлений сегментов.</td>
  </tr>
  <tr>
    <td>2.    Создает 2 сегментированные базы данных. 
  </tr>
  <tr>
    <td>3.    Создает сопоставление сегментов для этих баз данных (удаляет любые существующие сопоставления сегментов в этих базах данных). </td>
  </tr>
  <tr>
    <td>4.    Создает небольшой образец таблицы в обоих сегментах и заполняет эту таблицу сегментами.</td>
  </tr>
  <tr>
    <td>5.    Объявляет SchemaInfo для сегментированной таблицы.</td>
  </tr>

</table>

<table style="width:100%">
  <tr>
    <th>Файл PowerShell</th>
    <th>Действия</th>
  </tr>
<tr>
    <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
    <td>1.    Отправляет запрос разбиения к внешнему веб-серверу службы Split-Merge, которая отделяет половину данных из первого сегмента и переносит их во второй сегмент.</td>
  </tr>
  <tr>
    <td>2.    Запрашивает у внешнего веб-сервера состояние запроса на разбиение и ожидает завершения запроса.</td>
  </tr>
  <tr>
    <td>3.    Отправляет запрос на слияние внешнему веб-серверу службы Split-Merge, который перемещает данные из второго сегмента обратно в первый.</td>
  </tr>
  <tr>
    <td>4.    Запрашивает у внешнего веб-сервера состояние запроса слияния и ожидает завершения запроса.</td>
  </tr>
</table>

##Проверка развертывания с помощью PowerShell

1.    Откройте новое окно PowerShell и перейдите в каталог, куда вы загрузили пакет Split-Merge, затем перейдите в каталог powershell.
2.    Создайте сервер базы данных Azure SQL (или выберите существующий сервер), где будут созданы диспетчер сопоставлений сегментов и сегменты. 

Примечание. Сценарий SetupSampleSplitMergeEnvironment.ps1 создает эти базы данных на том же сервере по умолчанию, чтобы упростить сценарий. Это не является ограничением службы Split-Merge.

    Имя входа с проверкой подлинности SQL с доступом на чтение и запись для баз данных необходимы службе Split-Merge для перемещения данных и обновления сопоставления сегментов. Поскольку служба Split-Merge выполняется в облаке, она не поддерживает встроенную проверку подлинности.

    Убедитесь, что сервер Azure SQL настроен на разрешение доступа для IP-адреса компьютера, на котором выполняются сценарии. Можно найти этот параметр на сервере Azure SQL / настройка / разрешенные IP-адреса.

3.    Выполните сценарий SetupSampleSplitMergeEnvironment.ps1 для создания образца среды. 

    Выполнение этого сценария очистит все существующие структуры данных управления сопоставлением сегментов в базе данных диспетчера сопоставлений сегментов и сегментов. Может быть полезно выполнить скрипт повторно, если необходимо повторно инициализировать сопоставление сегментов или сегменты.

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

5.    Выполните сценарий ExecuteSampleSplitMerge.ps1 для выполнения операции разделения (перенос половины данных из первого сегмента во второй), а затем операции слияния (перемещения данных обратно в первый сегмент). Если настроить SSL и оставить конечную точку http отключенной, убедитесь, что можно использовать вместо этого конечную точку https://.

    Пример командной строки:

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

    Если появляется ниже ошибка, то, скорее, возникла ошибка в сертификате конечной веб-точки. Попробуйте подключиться к конечной веб-точке с предпочитаемым веб-браузером и проверьте, имеется ли сообщение об ошибке сертификата.

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    Если выполнено успешно, результат должен выглядеть следующим образом.

        > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
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

6.    Поэкспериментируйте с другими типами данных! Все эти сценарии принимают дополнительный параметр -ShardKeyType, который позволяет указать тип ключа. Значение по умолчанию - Int32, но можно также указать Int64, Guid или Binary. 

## Создание собственных запросов 

Службу можно использовать с помощью веб-интерфейса или импортировав модуль PowerShell SplitMerge.psm1.

Служба Split-Merge может перемещать данные в сегментированные и ссылочные таблицы. Сегментированная таблица содержит ключевой столбец сегментирования и различные строчные данные в каждом сегменте. Ссылочная таблица не сегментирована, поэтому содержит одни и те же строчные данные в каждом сегменте. Ссылочные таблицы полезны для данных, которые изменяются редко и используются для объединения (JOIN) в запросах с сегментированными таблицами.

Для выполнения операции разбиения-слияния необходимо объявить сегментированные и ссылочные таблицы, которые будут перемещены. Это осуществляется с помощью API **SchemaInfo**. Этот API находится в пространстве имен **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema**.

1.    Создайте для каждой сегментированной таблицы объект **ShardedTableInfo**, описывающий имя схемы родительской таблицы (необязательно, по умолчанию используется значение dbo), имя таблицы и имя столбца в таблице, которая содержит ключ сегментирования.
2.    Создайте для каждой ссылочной таблицы объект **ReferenceTableInfo**, описывающий имя схемы родительской таблицы (необязательно, по умолчанию используется значение dbo), и имя таблицы.
3.    Добавьте приведенные выше объекты TableInfo в новый объект **SchemaInfo**.
4.    Получите ссылку на объект **ShardMapManager** и назовите **GetSchemaInfoCollection**.
5.    Добавьте **SchemaInfo** в **SchemaInfoCollection**, указав имя сопоставления сегмента.

Пример этого можно увидеть в сценарии SetupSampleSplitMergeEnvironment.ps1.

Обратите внимание, что служба Split-Merge не создает для вас целевую базу данных (или схему для любой таблицы в базе данных). Они должны быть созданы предварительно перед отправкой запроса в службу.


## Устранение неполадок
При выполнении образцов сценариев Powershell может появиться следующее сообщение:

    Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.

Эта ошибка означает, что SSL-сертификат настроен неправильно. Следуйте инструкциям в разделе "Подключение с помощью веб-браузера".

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]
 
<!--Image references-->
[1]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/allowed-services.png
[2]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/manage.png
[3]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/staging.png
[4]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/upload.png
[5]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/storage.png
[6]: ./media/sql-database-elastic-scale-split-and-merge-tutorial/logs.png
