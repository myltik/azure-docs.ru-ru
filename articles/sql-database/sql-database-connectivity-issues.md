<properties
	pageTitle="Устранение ошибок подключения SQL, временные ошибки | Microsoft Azure"
	description="Узнайте, как устранять, диагностировать и предотвращать ошибки подключения SQL или временные ошибки в базе данных SQL Azure. "
	keywords="подключение sql,строка подключения,проблемы с подключением, временная ошибка,ошибка подключения"
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="daleche"/>


# Устранение, диагностика и предотвращение ошибок подключения SQL и временных ошибок для базы данных SQL

Эта статья содержит информацию о предотвращении, диагностике и устранении ошибок подключения и временных ошибок, которые происходят в клиентском приложении во время взаимодействия с базой данных SQL Azure. Узнайте, как настроить логику повторных попыток, создать строку подключения и настроить другие параметры подключения.

<a id="i-transient-faults" name="i-transient-faults"></a>

## Временные ошибки (временные сбои)

Временные ошибки (или временные сбои) возникают из-за причин, которые вскоре устраняются автоматически. Иногда временная ошибка возникает потому, что система Azure быстро сменяет аппаратные ресурсы, чтобы лучше распределить нагрузку, связанную с разными рабочими нагрузками. Большинство этих событий перенастройки часто завершаются менее чем за 60 секунд. Когда выполняется эта перенастройка, могут возникнуть ошибки подключения к базе данных SQL Azure. При разработке приложений, подключающихся к базе данных SQL Azure, следует учитывать возможность возникновения этих временных ошибок и предусматривать их обработку путем реализации логики повторных попыток в коде вместо вывода пользователю сообщений об ошибках приложения.

Если клиентская программа использует пакет ADO.NET, ваша программа получает исключение **SqlException** и таким образом узнает о временной ошибке. Значение свойства **Number** можно сверить со списком временных ошибок в начале статьи [Сообщения об ошибках для клиентских программ базы данных SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### Подключения и команды

Вам следует восстановить соединение SQL или установить его заново в зависимости от следующих условий.

* **Временная ошибка возникает при попытке подключения**: попытку нужно повторить через несколько секунд.

* **Временная ошибка возникает, когда выполняется команда SQL-запроса**: выполнение команды не нужно повторять сразу. Лучше заново установить подключение после некоторой задержки. Затем выполнение команды можно повторить.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### Повтор логики для временных ошибок


Клиентские программы, в которых иногда происходят временные ошибки, являются более надежными, если используют логику повторных попыток.


Если программа взаимодействует с базой данных SQL Azure через стороннее ПО промежуточного слоя, спросите поставщика, содержит ли это стороннее ПО логику повторных попыток на случай временных ошибок.

<a id="principles-for-retry" name="principles-for-retry"></a>

#### Принципы повторных попыток


- Повторите попытку открыть подключение, если ошибка является временной.


- Выполнение SQL-инструкции SELECT, которая закончилась временным сбоем, не нужно повторять напрямую.
 - Лучше установите новое подключение, а затем повторно выполните инструкцию SELECT.


- Когда выполнение SQL-инструкции UPDATE заканчивается временным сбоем, прежде чем выполнять инструкцию UPDATE еще раз, нужно установить новое подключение.
 - Логика повторных попыток обеспечивает полноценное выполнение транзакции базы данных или откат всей транзакции.


#### Другие соображения по поводу повторных попыток


- Пакетная программа, которая автоматически запускается после окончания рабочего дня и завершает свою работу до наступления утра, может выполнять повторные попытки с продолжительными интервалами.


- Программа, использующая пользовательский интерфейс, должна учитывать, что человек не любит ждать очень долго.
 - Это не значит, что нужно повторять попытку каждые несколько секунд, так как подобная политика может затопить систему запросами.


#### Увеличение интервала между повторными попытками



Рекомендуется подождать 5 секунд, прежде чем выполнять первую повторную попытку. Повторная попытка после ожидания менее 5 секунд может привести к перегрузке облачной службы. Для каждой последующей повторной попытки ожидание должно увеличиваться экспоненциально, но не более чем до 60 секунд.

Обсуждение *периода блокировки* для клиентов, которые используют ADO.NET, см. в статье [Пул подключений SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Кроме того, вы можете задать максимальное количество повторных попыток, которые программа должна выполнить перед автоматическим завершением работы.


#### Образцы кода с логикой повторных попыток


В этой статье доступны образцы кода с логикой повторных попыток на разных языках программирования:

- [Библиотеки подключений для Базы данных SQL и SQL Server](sql-database-libraries.md)


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### Тестирование логики повторных попыток


Чтобы протестировать логику повторных попыток, нужно имитировать или вызвать ошибку, которую можно исправить во время работы программы.


##### Тестирование с отключением от сети


Один из способов протестировать логику повторных попыток — это отключить клиентский компьютер от сети во время работы программы. Появится такая ошибка:

- **SqlException.Number** = 11001
- Сообщение: "Данный узел неизвестен."


В рамках первой повторной попытки программа может исправить опечатку, а затем попытаться подключиться.


Чтобы воссоздать такое поведение, следует отключить компьютер от сети, прежде чем запускать программу. Программа распознает параметр времени выполнения, после чего делает следующее.

1. Временно добавляет 11001 в свой список ошибок, чтобы эта ошибка считалась временной.
2. Первый раз пытается подключиться как обычно.
3. После выявления ошибки удаляет 11001 из списка.
4. Выводит сообщение с просьбой подключить компьютер к сети.
 - Программа приостанавливает дальнейшее выполнение с помощью метода **Console.ReadLine** или диалогового окна с кнопкой "ОК". Клавишу ВВОД пользователю нужно нажимать после подключения компьютера к сети.
5. Попытайтесь подключиться еще раз. В этот раз попытка должна завершиться успехом


##### Выполните проверку, сделав опечатку в имени базы данных при подключении.


Программа может намеренно сделать опечатку в имени пользователя перед первой попыткой подключения. Появится такая ошибка:

- **SqlException.Number** = 18456
- Сообщение: "Ошибка входа пользователя WRONG\_MyUserName."


В рамках первой повторной попытки программа может исправить опечатку, а затем попытаться подключиться.


Чтобы получить такое поведение, программа должна распознать параметр времени выполнения и сделать следующее:

1. Временно добавить 18456 в свой список ошибок, чтобы эта ошибка считалась временной.
2. Добавить элемент WRONG\_ к имени пользователя.
3. После выявления ошибки удалить 18456 из списка.
4. Удалить WRONG\_ из имени пользователя.
5. Попытаться подключиться еще раз. В этот раз попытка должна завершиться успехом.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### Параметры .NET SqlConnection для повторной попытки подключения


Если клиентская программа подключается к базе данных SQL Azure с помощью класса .NET Framework **System.Data.SqlClient.SqlConnection**, вам следует использовать .NET 4.6.1 или более позднюю версию этой платформы. В этих версиях реализована поддержка повторных попыток подключения. Дополнительную информацию об этой функции см. [здесь](http://go.microsoft.com/fwlink/?linkid=393996).


<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


При создании [строки подключения](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) для объекта **SqlConnection** нужно правильно настроить значения следующих параметров.

- ConnectRetryCount — *количество повторных попыток. Значение по умолчанию — 1; диапазон — от 0 до 255).*
- ConnectRetryInterval — *интервал между повторными попытками. Значение по умолчанию — 1 секунда; диапазон — от 1 до 60).*
- Connection Timeout — *время ожидания подключения. Значение по умолчанию — 15 секунд; диапазон — от 0 до 2147483647).*


Выбранные значения должны обеспечить выполнение следующего равенства:

- Connection Timeout = ConnectRetryCount * ConnectionRetryInterval

Например, если количество попыток = 3, а интервал между ними = 10 секунд, время ожидания подключения должно быть больше 29 секунд, иначе у системы не останется времени для последней (третьей) попытки подключения: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

### Подключения и команды


Параметры **ConnectRetryCount** и **ConnectRetryInterval** позволяют объекту **SqlConnection** повторять операцию подключения, не извещая об этом программу и не вынуждая ее управлять этим процессом. Повторные попытки будут выполняться в следующих ситуациях:

- при вызове метода mySqlConnection.Open;
- при вызове метода mySqlConnection.Execute.

Есть один важный нюанс. Если во время выполнения *запроса* возникает временная ошибка, объект **SqlConnection** не пытается подключиться еще раз. Как следствие, запрос повторно не выполняется. Но перед отправкой запроса для выполнения объект **SqlConnection** очень быстро проверит наличие соединения. Если эта быстрая проверка обнаружит проблемы с подключением, **SqlConnection** повторит операцию подключения. Если повторная попытка подключения завершится успешно, запрос отправится на выполнение.


#### Нужно ли сочетать ConnectRetryCount с логикой повторных попыток в приложении?

Предположим, что в вашем приложении реализована надежная настраиваемая логика повторных попыток. Допустим, приложение 4 раза повторяет операцию подключения. Если вы добавите в строку подключения значения **ConnectRetryInterval** и **ConnectRetryCount** = 3, общее количество повторных попыток составит 4 * 3 = 12. Вряд ли вам действительно нужно такое количество повторных попыток.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## Подключения к базе данных SQL Azure

<a id="c-connection-string" name="c-connection-string"></a>

### Подключение: строка подключения


Для подключения к базе данных SQL Azure используется строка подключения, несколько отличная от той, что используется для подключения к решению Microsoft SQL Server. Строку подключения для своей базы данных вы можете скопировать на [портале Azure](https://portal.azure.com/).


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### Подключение: IP-адрес


Чтобы принимать подключение от IP-адреса компьютера, на котором установлено клиентское приложение, нужно настроить сервер базы данных SQL. Для этого измените параметры брандмауэра с помощью [портала Azure](https://portal.azure.com/).


Если не задать IP-адрес, произойдет сбой программы и отобразится сообщение об ошибке, содержащее необходимый IP-адрес.


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


Дополнительные сведения см. в статье
[Практическое руководство. Настройка параметров брандмауэра для базы данных SQL](sql-database-configure-firewall-settings.md).


<a id="c-connection-ports" name="c-connection-ports"></a>

### Подключение: порты


Обычно нужно убедиться лишь в том, что на компьютере, на котором установлено клиентское приложение, для исходящей связи открыт порт 1433.


Например, если ваша клиентская программа размещена на компьютере Windows, брандмауэр Windows на этом узле позволяет открыть порт 1433:


1. Откройте панель управления
2. &gt; Все элементы панели управления
3. &gt; Брандмауэр Windows
4. &gt; Дополнительные параметры
5. &gt; Правила для исходящих подключений
6. &gt; Действия
7. &gt; Создать правило


Если клиентская программа установлена на виртуальной машине Azure, см. статью <br/>[Порты, кроме 1433, для ADO.NET 4.5 и базы данных SQL версии 12](sql-database-develop-direct-route-ports-adonet-v12.md).


Основные сведения о конфигурации портов и IP-адресов собраны в статье
[Настройка брандмауэра базы данных SQL Azure](sql-database-firewall-configure.md).


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### Подключение: ADO.NET 4.6.1


Если для подключения к базе данных SQL Azure программа использует классы ADO.NET, например **System.Data.SqlClient.SqlConnection**, мы рекомендуем использовать .NET Framework 4.6.1 или более позднюю версию.


ADO.NET 4.6.1:

- Предлагает повышенную надежность подключения к базам данных SQL Azure с помощью метода **SqlConnection.Open**. В методе **Open** теперь реализованы лучшие механизмы повторных попыток при временных сбоях, в частности для некоторых ошибок, которые возникают при ожидании соединения.
- Поддерживает работу с пулами подключений. В нее входит также эффективная проверка функционирования объекта соединения, который видит ваша программа.



Когда вы используете объект соединения, входящий в пул подключений, программе следует временно закрывать подключение, если она его сейчас не использует. Повторное открытие подключения обойдется вам дешевле, чем создание нового.


Если вы используете пакет ADO.NET 4.0 (или более раннюю версию), мы рекомендуем обновить его до последней версии.

- С ноября 2015 г. доступна [версия ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## Диагностика

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### Диагностика: проверяет, могут ли служебные программы подключаться


Если программе не удается подключиться к базе данных SQL Azure, в качестве диагностики можно попытаться подключиться с помощью служебной программы. В идеале служебная программа будет подключаться с помощью библиотеки, которую использует ваша программа.


На компьютерах под управлением Windows можно использовать следующие служебные программы:

- SQL Server Management Studio (ssms.exe). Подключается с помощью ADO.NET.
- Sqlcmd.exe. Подключается с помощью [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).


После подключения проверьте, работает ли короткий SQL-запрос SELECT.


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### Диагностика: проверка открытых портов


Предположим, вы подозреваете, что попытки подключения завершаются неудачей из-за проблем с портом. Вы можете запустить на компьютере служебную программу, которая отображает данные о конфигурациях порта.


На компьютерах Linux могут использовать следующие служебные программы:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`
 - (Вместо указанного в примере IP-адреса укажите свой.)


В Windows можно использовать служебную программу [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148). Ниже приведен пример выполнения, в рамках которого запрошены сведения о ситуации с портами на сервере базы данных SQL Azure и который запущен на ноутбуке.


```
[C:\Users\johndoe]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### Диагностика: внесение ошибок в журнал


Эпизодические неисправности иногда лучше всего диагностировать, выявляя общий шаблон за несколько дней или недель подряд.


Клиент может помочь в диагностике. Для этого ему следует вносить в журнал все ошибки, с которыми он сталкивается. У вас может получиться коррелировать записи журнала со сведениями об ошибках, которые база данных SQL Azure вносит в журнал для внутренних целей.


Для облегчения ведения журналов можно использовать Enterprise Library 6 (EntLib60), где используются классы .NET.

- [5 - As Easy As Falling Off a Log: Using the Logging Application Block (5. Проще простого: использование блока приложения для ведения журналов).](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### Диагностика: проверка системных журналов на наличие ошибок


Ниже приведены некоторые Transact-SQL-инструкции SELECT, которые запрашивают в журналах сведения об ошибках и прочую информацию.


| Запрос у журнала | Описание |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | В представлении [sys.event\_log](http://msdn.microsoft.com/library/dn270018.aspx) приводится информация об отдельных событиях, в том числе о тех, которые могут привести к временным ошибкам или сбоям подключения.<br/><br/>В идеале вы можете сопоставить значения **start\_time** и **end\_time** с временем возникновения ошибок в клиентской программе.<br/><br/>**Совет**. Для выполнения этого запроса необходимо подключиться к базе данных **master**. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | Представление [sys.database\_connection\_stats](http://msdn.microsoft.com/library/dn269986.aspx) отображает суммарное количество событий каждого типа, что также бывает полезно при диагностике.<br/><br/>**Совет**. Для выполнения этого запроса необходимо подключиться к базе данных **master**. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### Диагностика: поиск событий проблемы в журнале базы данных SQL


Искать записи о событиях проблемы можно в журнале базы данных SQL Azure. Попробуйте выполнить в базе данных **master** такую Transact-SQL-инструкцию SELECT:


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### Несколько возвращенных строк из sys.fn\_xe\_telemetry\_blob\_target\_read\_file


Ниже показано, как может выглядеть возвращенная строка. Отображаемые пустые значения могут не быть пустыми в других строках.


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## Enterprise Library 6


Enterprise Library 6 (EntLib60) — это платформа классов .NET, которая помогает реализовывать надежные клиенты облачных служб, одной из которых является служба базы данных SQL Azure. Дополнительную информацию обо всех полезных возможностях EntLib60 вы найдете в следующей статье:

- [Enterprise Library 6. Апрель, 2013 г.](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


Одна из областей, в которой может помочь EntLib60, — логика повторных попыток для обработки временных ошибок.

- [4 - Perseverance, Secret of All Triumphs: Using the Transient Fault Handling Application Block (4. Настойчивость — секрет всех побед. Использование блока приложения для обработки временных ошибок).](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


> [AZURE.NOTE] Исходный код для EntLib60 доступен для открытого [скачивания](http://go.microsoft.com/fwlink/p/?LinkID=290898). Корпорация Майкрософт не планирует обновлять функции и менять характер обслуживания библиотеки EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### Классы EntLib60 для временных ошибок и повторов


Следующие классы EntLib60 особенно полезны для логики повторных ошибок. Все они входят в пространство имен **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** или во вложенные пространства.

*В пространстве имен* *Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:*

- класс **RetryPolicy**;
 - метод **ExecuteAction**;


- класс **ExponentialBackoff**;


- класс **SqlDatabaseTransientErrorDetectionStrategy**;


- класс **ReliableSqlConnection**;
 - метод **ExecuteCommand**.


В пространстве имен **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- класс **AlwaysTransientErrorDetectionStrategy**;

- класс **NeverTransientErrorDetectionStrategy**.


Ниже приведены ссылки на сведения о библиотеке EntLib60.

- Бесплатная [загрузка 2-го издания книги «Руководство разработчика по Microsoft Enterprise Library»](http://www.microsoft.com/download/details.aspx?id=41145).

- Рекомендации: в статье [Общие рекомендации по повторным попыткам](../best-practices-retry-general.md) содержится подробное обсуждение логики повторных попыток.

- Загрузка на сайте NuGet компонента [Enterprise Library 6.0: Transient Fault Handling application block](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### EntLib60: блок ведения журнала


- Блок ведения журнала — это очень гибкое и настраиваемое решение, которое позволяет выполнять такие действия:
 - создавать и хранить сообщения журнала в разных расположениях;
 - классифицировать и фильтровать сообщения;
 - собирать контекстную информацию, полезную для отладки, трассировки, аудита и выполнения общих требований к ведению журнала.


- Это решение извлекает функции ведения журнала из расположения журнала, что обеспечивает согласованность кода приложения независимо от расположения и типа хранилища журнала.


Дополнительные сведения см. в статье [5\. Простой вариант: использование решения Logging Application Block](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### Исходный код метода EntLib60 IsTransient


Ниже приведен исходный код (на языке C#) метода **IsTransient** из класса **SqlDatabaseTransientErrorDetectionStrategy**. Исходный код поясняет, какие ошибки считаются временными и приемлемыми для повторной попытки (версия за апрель 2013 г.).

Для удобочитаемости из этой копии удалены многочисленные строки комментариев (**//comment**).


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## Дальнейшие действия

- Сведения об устранении других распространенных неполадок, возникающих при подключении к базе данных SQL Azure, см. в разделе [Устранение неполадок подключения к базе данных SQL Azure](sql-database-troubleshoot-common-connection-issues.md).

- [Пул подключений SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*Retrying* — это лицензированная общая библиотека Apache 2.0 для повторных попыток, написанная на языке **Python**, которая позволяет легко добавить режим повтора куда угодно.](https://pypi.python.org/pypi/retrying)

<!---HONumber=AcomDC_0921_2016-->