<properties title="Split and Merge Service Tutorial" pageTitle="Учебник по службе Azure SQL Split and Merge (служба разбиения и слияния Azure SQL)" description="Splitting and Merging with Elastic Scale" metaKeywords="sharding scaling, Azure SQL Database sharding, elastic scale, splitting and merging elastic scale" services="sql-database" documentationCenter=""  manager="jhubbard" authors="sidneyh@microsoft.com"/>

<tags ms.service="sql-database" ms.workload="sql-database" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/02/2014" ms.author="sidneyh" />

#Учебник по службе Elastic Scale Split and Merge

## Загрузите пакеты Split-Merge 
1. Загрузите последнюю версию NuGet из [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget). 
2. Откройте командную строку и перейдите в каталог, куда вы загрузили nuget.exe. 
3. Загрузите последний пакет Split-Merge в текущий каталог командой, представленной ниже: 
`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge`  

Описанные выше действия приведут к загрузке файлов Split-Merge в текущий каталог. Файлы помещаются в каталог **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x**, где *x.x.xxx.x* это номер версии. Найдите файлы службы Split-Merge в подкаталоге **content\splitmerge\service**, и скрипты PowerShell для Split-Merge (и требуемые клиентские dll-библиотеки) в подкаталоге **content\splitmerge\powershell**.

##Предварительные требования 

1. Создайте базу данных SQL Azure, которая будет использоваться как база данных состояния Split-Merge. Откройте [портал управления Azure](https://manage.windowsazure.com). В нижнем левом углу нажмите кнопку **Создать**, затем выберите **Службы данных** **База данных SQL** -> **Настраиваемое создание**. Введите имя базы данных, создайте нового пользователя и пароль. Не забудьте записать имя и пароль для последующего использования.

2. Убедитесь, что сервер баз данных SQL Azure разрешает службам Windows Azure подключение к нему. Перейдите в [портал управления Azure](https://manage.windowsazure.com), и в левой панели выберите **Базы данных SQL**. Затем щелкните **Сервера** на ленте в верхней части экрана и выберите ваш сервер. Затем нажмите кнопку **Настроить** вверху и убедитесь, что параметру **Службы Windows Azure** задано значение **Yes** (Да).

    ![Allowed services][1]

3. Создайте учетную запись хранилища Azure, которая будет использоваться для выходных данных диагностики. Откройте [портал управления Azure](https://manage.windowsazure.com). В нижнем левом углу нажмите кнопку **Создать**, выберите **Службы данных**, **Хранилище**, а затем **Быстрое создание**. 

4. Создайте облачную службу Azure, которая будет содержать вашу службу Split-Merge.  Откройте [портал управления Azure](https://manage.windowsazure.com). В нижнем левом углу нажмите кнопку **Создать**, затем **Вычисления**, **Облачная служба** и **Быстрое создание**. 


## Настройка службы Split-Merge 

### Конфигурация службы Split-Merge 

1. В папке, куда вы загрузили файлы Split-Merge, создайте копию файла **ServiceConfiguration.Template.cscfg**, который идет вместе с **SplitMergeService.cspkg** и переименуйте его в **ServiceConfiguration.cscfg**.

2. Откройте в текстовом редакторе файл ServiceConfiguration.cscfg. Мы рекомендуем использовать Visual Studio, так эта среда проверяет входных данных, такие как формат отпечатков сертификатов. 

3. Создайте новую базу данных или выберите существующую, она будет служить базой данных состояния для операций Split-Merge. Получите строку подключения для этой базы данных. Строка подключения к базу данных Azure SQL, обычно имеет вид:

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .
4.    Введите эту строку подключения в файле cscfg в параметре ElasticScaleMetadata разделов **SplitMergeWeb** и **SplitMergeWorker**.

5.    Настройка целевого объекта для журналов диагностики требует наличия учетной записи хранилища в Azure. Для конфигурации Split/Merge требуется строка подключения к вашей учетной записи хранилища Azure. Строка подключения должна иметь следующий вид:

        "DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccessKey" 
    
Чтобы определить ключ доступа, перейдите в [портал управления Azure](https://manage.windowsazure.com), откройте вкладку **Хранилище** в левой части окна, выберите имя, соответствующее вашей учетной записи хранилища и нажмите кнопку **Управление ключами доступа** в нижней части окна. Нажмите кнопку **Копировать** рядом с **Первичным ключом доступа**.

![manage access keys][2]

6.    Введите имя учетной записи хранилища и один полученных из ключей доступа вместо заполнителей в строке подключения к хранилищу. Эта строка подключения используется в разделах ролей **SplitMergeWeb** и **SplitMergeWorker** в параметре **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. Для каждой роли можно использовать собственную учетную запись хранилища. 

### Настройка безопасности 
Подробные инструкции по настройке безопасности службы см. в статье [Конфигурации безопасности Elastic Scale](./sql-database-elastic-scale-configure-security.md).

В целях простого тестового развертывания для завершения этого учебника, будет выполнен минимальный набор действий для подготовки и запуска службы. В них задействуется только одна машина и учетная запись для связи со службой.

### Создание самозаверяющего сертификата 

Создайте новый каталог и из этого каталога выполните следующую команду в окне [командной строки разработчика для Visual Studio](http://msdn.microsoft.com/ru-ru/library/ms229859.aspx):

    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer

Будет предложено ввести пароль для защиты закрытого ключа. Введите надежный пароль и подтвердите его. Затем пароль будет запрошен еще один раз. Нажмите кнопку **Да** в конце операции, чтобы импортировать его в хранилище доверенных корневых центров сертификации.

###    Создание PFX-файла 

Выполните следующую команду в том же окне, в котором была выполнен makecert; введите тот же пароль, что использовался для создания сертификата.

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### Импортирование сертификата клиента в личное хранилище сертификатов
1. В проводнике Windows дважды щелкните файл **MyCert.pfx.**.
2. В **мастере импорта сертификатов** выберите **текущего пользователя** и нажмите кнопку **Далее**.
3. Проверьте путь к файлу и нажмите кнопку **Далее**.
4. Введите пароль, оставьте установленным флажок **Включить все расширенные свойства** и нажмите кнопку **Далее**.
5. Оставьте установленным флаг **Автоматически выбрать хранилище сертификатов[... ]** и нажмите кнопку **Далее**.
6. Нажмите кнопку **Готово**, затем **OK**.

### Загрузите файл PFX в облачную службу

Откройте [портал управления Azure](https://manage.windowsazure.com).

1. Выберите **Облачные службы**.
2. Выберите облачную службу, созданную ранее для службы Split/Merge.
3. В верхнем меню выберите **Сертификаты**.
4. Нажмите кнопку **Загрузить** на нижней панели.
5. Выберите файл PFX и введите пароль, выбранный выше.
6. После завершения операции скопируйте отпечаток сертификата из новой записи списка.

### Обновление файла конфигурации службы

Вставьте отпечаток сертификата, скопированный ранее, в атрибут thumbprint/value этих параметров:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Обратите внимание, что для рабочей среды развертывания следует использовать отдельные сертификаты для ЦС, сертификат сервера и сертификаты клиента. Подробные инструкции по этому вопросу см. в статье [конфигурацию безопасности](./sql-database-elastic-scale-configure-security.md).

### Развертывание службы Split-Merge
1. Откройте [портал управления Azure](https://manage.windowsazure.com).
2. Перейдите на вкладку **Облачные службы** в левой части окна и выберите службу, созданную ранее. 
3. Щелкните элемент **Панель мониторинга**. 
4. Выберите промежуточную среду, а затем нажмите кнопку **Отправить новое промежуточное развертывание**.

    ![Staging][3]

5. Введите в диалоговом окне метку развертывания. Выберите вариант "Из локальной" для параметров "Пакет" и "Конфигурация", затем укажите файл **SplitMergeService.cspkg** и cscfg-файл, который настроили ранее.
6. Убедитесь, что установлен флажок с надписью **Развернуть, даже если одна или несколько ролей содержат отдельный экземпляр**.
7. Нажмите кнопку справа внизу, чтобы начать развертывание. Выполнение этой операции может занять несколько минут.

![Upload][4]


## Устранение неполадок при развертывании
Если веб-роль не запускается, то, вероятно, это из-за проблемы с конфигурацией безопасности. Проверьте, что протокол SSL настроен так, как описано выше.

Если не удается запустить рабочую роль, а веб-роль запускается успешно, то, скорее всего, это означает проблему при подключении к базе данных состояния, созданную вами ранее. 

* Убедитесь, что строка подключения в файле .cscfg задана правильно. 
* Проверьте существование сервера и базы данных, и правильность идентификатора пользователя и пароля.
* Для баз данных SQL Azure строка подключения должна иметь вид: 

        "Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30" .

* Убедитесь, что имя сервера не начинается с **https://**.
* Убедитесь, что сервер баз данных SQL Azure разрешает службам Windows Azure подключение к нему. Для этого откройте адрес https://manage.windowsazure.com, нажмите кнопку "Базы данных SQL" слева, щелкните "Серверы" вверху и выберите свой сервер. Нажмите кнопку **Настроить** вверху и убедитесь, что параметру **Службы Windows Azure** задано значение "Да". (См. раздел предварительных условий в начале данной статьи).

* Просмотрите журналы диагностики для экземпляра службы Split/Merge. Откройте экземпляр Visual Studio и в строке меню выберите пункт **Вид** и **Обозреватель серверов**. Щелкните значок **Windows Azure**, чтобы подключиться к своей подписке Azure. Перейдите к разделу Windows Azure -> Хранилище -> <ваша учетная запись хранилища> -> Таблицы -> WADLogsTable. Дополнительные сведения см. в статье [Обзор ресурсов хранилища с помощью обозревателя серверов](http://msdn.microsoft.com/ru-ru/library/azure/ff683677.aspx) 

    ![][5]

    ![][6]

## Тестирование развертывания службы Split-Merge
### Подключение с помощью веб-браузера

Определите конечную веб-точку своей службы Split-Merge. Его можно найти на портале управления Azure, перейдя к **панели мониторинга** облачной службы в разделе **URL-адрес сайта** в правой части панели. Замените **http://** на **https://**, так как параметры безопасности по умолчанию отключают конечную точку HTTP. Загрузите в браузере страницу с этим URL-адресом.

### Тестирование скриптов PowerShell

Развертывание и среду можно проверить, запустив скрипты PowerShell, включенные в пример приложения.

Файлы скриптов:

1. **SetupSampleSplitMergeEnvironment.ps1** - задает тестовый уровень данных для службы Split/Merge (см. в таблице ниже подробное описание)
2. **ExecuteSampleSplitMerge.ps1** - выполняет операции теста над уровнем данных (см. в таблице ниже подробное описание)
3. **GetMappings.ps1** - скрипт верхнего уровня, который выводит текущее состояние сопоставлений сегментов.
4. **ShardManagement.psm1** - вспомогательный скрипт, который выступает в виде оболочки для ShardManagement API
5. **SqlDatabaseHelpers.psm1** - вспомогательный скрипт для создания и управления базами данных SQL

<table style="width:100%">
  <tr>
    <th>Файл PowerShell</th>
    <th>Действия</th>
  </tr>
  <tr>
    <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
    <td>1.    Создает базу данных для диспетчера сопоставлений сегментов </td>
  </tr>
  <tr>
    <td>2.    Создает 2 базы данных для сегментов. 
  </tr>
  <tr>
    <td>3.    Создает сопоставление сегментов для этой базы данных (удаляет в этих базах любые существующие сопоставления сегментов). </td>
  </tr>
  <tr>
    <td>4.    Создает небольшую таблицу в двух сегментах и заполняет ее на одном из них.</td>
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
    <td>1.    Отправляет запрос на разбиение в веб-интерфейс службы Split-Merge, которая разносит половину данных с первого сегмента на второй.</td>
  </tr>
  <tr>
    <td>2.    Опрашивает веб-интерфейс насчет состояния запроса на разбиение и ожидает завершения запроса.</td>
  </tr>
  <tr>
    <td>3.    Отправляет запрос на слияние в веб-интерфейс службы Split-Merge, которая перемещает данные из второго сегмента обратно в первый.</td>
  </tr>
  <tr>
    <td>4.    Опрашивает веб-интерфейс насчет состояния запроса на слияние и ожидает завершения этого запроса.</td>
  </tr>
</table>

##Проверка развертывания с помощью PowerShell

1.    Откройте новое окно PowerShell и перейдите в каталог, куда вы загрузили пакет Split-Merge, затем перейдите в каталог powershell.
2.    Создайте сервер базы данных Azure SQL (или выберите существующий), где будут созданы диспетчер сопоставлений сегментов и сами сегменты. 

Примечание. Чтобы сделать скрипт проще, код в SetupSampleSplitMergeEnvironment.ps1 создает все эти базы данных на одном сервере. Это делается не из-за какого-либо ограничения службы Split-Merge.

    Службе Split-Merge для перемещения данных и обновления сопоставления сегментов нужны учетные данные проверки подлинности SQL с доступом на чтение и запись к базам данных. Т.к. служба Split-Merge выполняется в облаке, то в данный момент она не поддерживает встроенную проверку подлинности.

    Убедитесь, что на сервере Azure SQL настроен доступ с IP-адреса компьютера, где запускаются эти скрипты. Этот параметр находится в разделе "Сервер Azure SQL / конфигурация / разрешенные IP-адреса".

3.    Запустите скрипт SetupSampleSplitMergeEnvironment.ps1, чтобы создать среду для проверки. 

    Выполнение этого скрипта очистит все существующие структуры данных для сопоставления сегментов в базе данных диспетчера сопоставлений и сами сегменты. Если необходимо повторно инициализировать сопоставление или сегменты, стоит запустить скрипт еще раз.

    Пример командной строки:

        .\SetupSampleSplitMergeEnvironment.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    
4.    Выполните скрипт Getmappings.ps1, чтобы посмотреть сопоставления, которые в настоящее время существуют в проверяемой среде.

        .\GetMappings.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net'

5.    Запустите скрипт ExecuteSampleSplitMerge.ps1, что выполнит операции разбиения (перенос половины данных первого сегмента во второй), а затем операцию слияния (перемещения данных обратно на первый сегмент). Если вы настроили службу на использование SSL и отключили конечную точку http, используйте вместо нее конечную точку https://.

    Пример командной строки:

        .\ExecuteSampleSplitMerge.ps1 `
            -UserName 'mysqluser' `
            -Password 'MySqlPassw0rd' `
            -ShardMapManagerServerName 'abcdefghij.database.windows.net' `
            -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' `
            -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'

    Если появляется ошибка, приведенная ниже, то, скорее всего, есть проблема с сертификатом конечной точки веб-роли. Попробуйте подключиться к конечной точке веб-роли с помощью браузера и проверьте, присутствует ли ошибка сертификата.

        Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.

    Если операции пройдут успешно, то результат должен выглядеть так:

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

6.    Поэкспериментируйте с другими типами данных. Все эти скрипты принимают необязательный параметр -ShardKeyType, который позволяет указать тип ключа. Значение по умолчанию - Int32, но можно также указать Int64, Guid или Binary. 

## Создание собственных запросов 

Службу можно использовать через веб-интерфейс или через импортированный модуль PowerShell SplitMerge.psm1.

Служба Split-Merge может перемещать данные как в сегментированных так и в ссылочных таблицах. Сегментированная таблица содержит столбец с ключом сегментирования и имеет различные данные в строке на каждом сегменте. Ссылочная таблица не разбита по сегментам, поэтому она содержит одинаковые данные в строках на каждом сегменте. Ссылочные таблицы полезны для данных, которые меняются редко и используются в запросах для операций JOIN с сегментированными таблицами.

Для выполнения операции разбиения-слияния, необходимо объявить сегментированные таблицы и ссылочные таблицы, которые нужно переместить. Это выполняется с помощью API-интерфейса **SchemaInfo**. Этот API-интерфейс входит в пространство имен **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema**.

1.    Для каждой сегментированной таблицы создайте объект **ShardedTableInfo**, описывающий имя схемы родительской таблицы (это необязательно, по умолчанию используется значение "dbo"), имя таблицы и имя столбца в таблице, который содержит ключ сегментирования. 
2.    Создайте для каждой ссылочной таблицы объект **ReferenceTableInfo**, описывающий имя схемы родительской таблицы (необязательно, по умолчанию используется значение "dbo") и имя таблицы.
3.    Добавьте объекты TableInfo к новому объекты **SchemaInfo**.
4.    Получите ссылку на объект **ShardMapManager** и вызовите метод **GetSchemaInfoCollection**.
5.    Добавьте **SchemaInfo** в **SchemaInfoCollection**, указав имя сопоставления сегментов.

Пример этого можно посмотреть в скрипте SetupSampleSplitMergeEnvironment.ps1.

Обратите внимание, что служба Split-Merge не создает автоматически целевую базы данных (или схему для каких-либо таблиц ней). Это ваша задача. Они уже должны быть созданы перед отправкой запроса к службе.


## Устранение неполадок
При выполнении примеров скриптов powershell может появиться следующее сообщение:

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
