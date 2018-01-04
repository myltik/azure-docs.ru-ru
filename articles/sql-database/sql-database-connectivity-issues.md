---
title: "Устранение ошибок подключения SQL, временные ошибки | Документация Майкрософт"
description: "Узнайте, как устранять, диагностировать и предотвращать ошибки подключения SQL или временные ошибки в базе данных SQL Azure."
keywords: "подключение sql,строка подключения,проблемы с подключением, временная ошибка,ошибка подключения"
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: efb35451-3fed-4264-bf86-72b350f67d50
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/29/2017
ms.author: daleche
ms.openlocfilehash: 7d393cd08ef5c20ef680e4e1ab3aded191abe932
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Устранение, диагностика и предотвращение ошибок подключения SQL и временных ошибок для базы данных SQL
Эта статья содержит информацию о предотвращении, диагностике и устранении ошибок подключения и временных ошибок, которые происходят в клиентском приложении во время взаимодействия с базой данных SQL Azure. Узнайте, как настроить логику повторных попыток, создать строку подключения и настроить другие параметры подключения.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Временные ошибки (временные сбои)
Временная ошибка, также известные как временных сбоев имеет основную причину, разрешающим только сам. Иногда временная ошибка возникает потому, что система Azure быстро сменяет аппаратные ресурсы, чтобы лучше распределить нагрузку, связанную с разными рабочими нагрузками. Большинство этих событий изменения конфигурации завершено в менее 60 секунд. В течение этого интервала времени изменения конфигурации могут быть проблемы с подключением к базе данных SQL. Приложения, которые подключаются к базе данных SQL должен быть построен ожидать этих временных ошибок. Для их обработки необходимо Реализуйте логику повторных попыток в своем коде, а не отображает их пользователям в виде ошибок приложения.

Если программа клиент использует ADO.NET, программа укажет о временная ошибка throw из **SqlException**. Сравнение **номер** свойства со списком временных ошибок, которые находятся в верхней части статьи [коды ошибок SQL для клиентских приложений баз данных SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Подключение и команду
Повторите попытку подключения SQL или установить ее еще раз, в зависимости от следующих:

* **Временная ошибка возникает во время попытки подключения**: с задержкой несколько секунд и повторите попытку подключения.
* **Временная ошибка возникает во время команда SQL-запроса**: немедленно повторить команду. Вместо этого после задержки, заново установите соединение. Повторите команду.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Повтор логики для временных ошибок
Клиентские программы, в которых иногда происходят временные ошибки, являются более надежными, если используют логику повторных попыток.

Когда программа взаимодействует с базой данных SQL через стороннего ПО промежуточного слоя, обратиться к поставщику ли по промежуточного слоя содержит логику повторных попыток для временных ошибок.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Принципы повторных попыток
* Если ошибка является временным, повторно открыть соединение.
* Не повторить инструкцию SQL SELECT, завершилась с ошибкой временных напрямую.
  * Лучше установите новое подключение, а затем повторно выполните инструкцию SELECT.
* Если инструкции SQL UPDATE завершается неудачно с временной ошибкой, установите новое подключение перед тем как повторить обновление.
  * Логика повторных попыток необходимо убедиться, завершена транзакция всей базы данных или что вся транзакция откатывается.

### <a name="other-considerations-for-retry"></a>Другие соображения по поводу повторных попыток
* Программа пакета, автоматически начинается после окончания рабочего дня и завершается до утра можете проявить очень пациента с интервалам времени между его повторных попыток.
* Программа, использующая пользовательский интерфейс, должна учитывать, что человек не любит ждать очень долго.
  * Решение не выполнять попытки каждые несколько секунд, так как эта политика может переполнить системы вместе с запросами.

### <a name="interval-increase-between-retries"></a>Увеличение интервала между повторными попытками
Мы рекомендуем, подождите 5 секунд перед ваш первый повтор. Повторная попытка после ожидания менее 5 секунд может привести к перегрузке облачной службы. Для каждой последующей повторной попыткой задержка должен увеличиваться экспоненциально, но не более 60 секунд.

Обсуждение интервала блокирования для клиентов, использующих ADO.NET см. в разделе [SQL Server пулы соединений (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Также можно задать максимальное число попыток, прежде чем программа самостоятельно завершает.

### <a name="code-samples-with-retry-logic"></a>Образцы кода с логикой повторных попыток
Образцы кода с логикой повторных попыток доступны по ссылкам:

- [Step 4: Connect resiliently to SQL with ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n] (Шаг 4. Выполнение устойчивого подключения к SQL с помощью ADO.NET)
- [Step 4: Connect resiliently to SQL with PHP][step-4-connect-resiliently-to-sql-with-php-p42h] (Шаг 4. Выполнение устойчивого подключения к SQL с помощью PHP)

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Тестирование логики повторных попыток
Чтобы проверить логику повторов, необходимо имитировать или приводят к ошибке, которую можно исправить, пока выполняется приложение.

#### <a name="test-by-disconnecting-from-the-network"></a>Тестирование с отключением от сети
Один из способов протестировать логику повторных попыток — это отключить клиентский компьютер от сети во время работы программы. Ошибка::

* **SqlException.Number** = 11001
* Сообщение: "Данный узел неизвестен."

В составе первой попытки повтора программы можно исправить неверное написание и попытайтесь подключиться к.

Чтобы сделать этот тест практические, отключение питания компьютера от сети, перед запуском программы. Затем программа распознает параметр среды выполнения, в результате чего программа:

* Временно добавляет 11001 в свой список ошибок, чтобы эта ошибка считалась временной.
* Первый раз пытается подключиться как обычно.
* После выявления ошибки удаляет 11001 из списка.
* Отображать сообщение о том, пользователю подключите компьютер к сети.
   * Программа приостанавливает дальнейшее выполнение с помощью метода **Console.ReadLine** или диалогового окна с кнопкой "ОК". Пользователь нажимает клавишу ВВОД после компьютер подключен к сети.
* Попытаться подключиться еще раз. В этот раз попытка должна завершиться успехом.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Выполните проверку, сделав опечатку в имени базы данных при подключении.
Программа может намеренно сделать опечатку в имени пользователя перед первой попыткой подключения. Ошибка::

* **SqlException.Number** = 18456
* Сообщение: "Ошибка входа пользователя WRONG_MyUserName."

В составе первой попытки повтора программы можно исправить неверное написание и попытайтесь подключиться к.

Чтобы сделать этот тест практические, программа распознает параметр среды выполнения, в результате чего программа:

* Временно добавить 18456 в свой список ошибок, чтобы эта ошибка считалась временной.
* Добавить элемент WRONG_ к имени пользователя.
* После выявления ошибки удалить 18456 из списка.
* Удалить WRONG_ из имени пользователя.
* Попытаться подключиться еще раз. В этот раз попытка должна завершиться успехом.


<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Параметры .NET SqlConnection для повторной попытки подключения
Если клиентская программа подключается к базе данных SQL с помощью класса .NET Framework **System.Data.SqlClient.SqlConnection**, используйте .NET 4.6.1 или более поздней версии (или .NET Core), чтобы вы могли использовать его функций повторных попыток подключения. Дополнительные сведения о функции см. в разделе [веб-страницу](http://go.microsoft.com/fwlink/?linkid=393996).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

При построении [строка подключения](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) для вашей **SqlConnection** объекта, распределять значения между следующими параметрами:

* **ConnectRetryCount**:&nbsp;&nbsp;по умолчанию — 1. Диапазон — от 0 до 255.
* **ConnectRetryInterval**:&nbsp;&nbsp;по умолчанию составляет 1 секунду. Диапазон — от 1 до 60.
* **Время ожидания соединения**:&nbsp;&nbsp;по умолчанию — 15 секунд. Диапазон — от 0 до 2147483647.

Выбранные значения должны обеспечить выполнение следующего равенства:

Connection Timeout = ConnectRetryCount * ConnectionRetryInterval

Например, если счетчик равен 3 и интервал равен 10 секунд, время ожидания только 29 секунд не дает системе достаточно времени для его третьей и последней попытки подключиться: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Подключение и команду
Параметры **ConnectRetryCount** и **ConnectRetryInterval** позволяют объекту **SqlConnection** повторять операцию подключения, не извещая об этом программу и не вынуждая ее управлять этим процессом. Повторные попытки будут выполняться в следующих ситуациях:

* при вызове метода mySqlConnection.Open;
* при вызове метода mySqlConnection.Execute.

Есть один важный нюанс. Если возникает временная ошибка во время вашей *запроса* в ходе выполнения вашей **SqlConnection** объекта не повтор операции подключения. Конечно, он не повторите запрос. Но перед отправкой запроса для выполнения объект **SqlConnection** очень быстро проверит наличие соединения. Если эта быстрая проверка обнаружит проблемы с подключением, **SqlConnection** повторит операцию подключения. Если повтор завершается успешно, запрос отправляется для выполнения.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Нужно ли сочетать ConnectRetryCount с логикой повторных попыток в приложении?
Предположим, что в вашем приложении реализована надежная настраиваемая логика повторных попыток. Он может повторить попытку операции подключения четыре раза. Если вы добавите в строку подключения значения **ConnectRetryInterval** и **ConnectRetryCount** = 3, общее количество повторных попыток составит 4 * 3 = 12. Вряд ли вам действительно нужно такое количество повторных попыток.


<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Подключения к базе данных SQL
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Подключение: строка подключения
Строка подключения, необходимые для подключения к базе данных SQL немного отличается от строку, используемую для подключения к SQL Server. Строку подключения для своей базы данных вы можете скопировать на [портале Azure](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Подключение: IP-адрес
Чтобы принимать подключение от IP-адреса компьютера, на котором установлено клиентское приложение, нужно настроить сервер базы данных SQL. Настройка этой конфигурации, изменить параметры брандмауэра с помощью [портал Azure](https://portal.azure.com/).

Если вы забыли настроить IP-адрес, программа завершается с удобно сообщение о том, требуется IP-адрес.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Дополнительные сведения см. в разделе [настроить параметры брандмауэра в базе данных SQL](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Подключение: порты
Как правило необходимо убедиться, что только порт 1433 открыт для исходящей связи на компьютере, на котором размещается клиентская программа.

Например когда клиентская программа размещается на компьютере Windows, можно использовать брандмауэр Windows на узле для открытия порта 1433.

1. Откройте панель управления.

2. Выберите **все элементы панели управления** > **брандмауэра Windows** > **Дополнительные параметры** > **правила для исходящих подключений**   >  **Действия** > **новое правило**.

Если клиентская программа находится на виртуальной машине Azure (ВМ), чтения [порты за пределами 1433 для ADO.NET 4.5 и базы данных SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

Основные сведения о конфигурации портов и IP-адресов, в разделе [брандмауэр базы данных SQL Azure](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Подключение: ADO.NET 4.6.1
Если программа использует классы ADO.NET как **System.Data.SqlClient.SqlConnection** для подключения к базе данных SQL, мы рекомендуем использовать .NET Framework версии 4.6.1 или более поздней версии.

ADO.NET 4.6.1:

* Для базы данных SQL, повышение надежности при открытии соединения с помощью **SqlConnection.Open** метод. **Откройте** метод теперь включает в себя механизмы повтора наилучшим образом в ответ на временных сбоев для некоторых ошибок до истечения времени ожидания соединения.
* Организация пулов соединений поддерживается, включающее эффективный проверки, что объект соединения, он предоставляет программа работает.

При использовании объект соединения в пуле соединений, мы рекомендуем, программа временно закрывать соединение после его не сразу же используется. Снова откройте подключение невысока, но создать новое соединение.

При использовании ADO.NET 4.0 или более ранней версии, рекомендуется обновить последние ADO.NET. С ноября 2015 года доступна [версия ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Диагностика
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Диагностика: проверяет, могут ли служебные программы подключаться
Если программа не может подключиться к базе данных SQL, диагностики уже подключиться с помощью служебной программы. В идеальном случае программа устанавливает соединение с помощью той же библиотеке, используемого программой.

На компьютерах под управлением Windows можно использовать следующие служебные программы:

* SQL Server Management Studio (ssms.exe), который подключается с помощью ADO.NET
* SQLCMD.exe, который подключается с помощью [ODBC](http://msdn.microsoft.com/library/jj730308.aspx)

После подключения программы тестирования ли работает короткий запрос SELECT языка SQL.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Диагностика: проверка открытых портов
Если есть подозрение, что подключение попытки не удались из-за проблем порт, вы запуск программы на компьютере, которая сообщает о конфигурации порта.

В Linux можно попробовать следующие программы:

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * Измените значение примере ваш IP-адресом.

В Windows [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) программа может оказаться полезной. Ниже приведен пример выполнения, запрос к ситуации порта на сервере базы данных SQL и, выполненного на ноутбуке.

```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Диагностика: внесение ошибок в журнал
Эпизодические неисправности иногда лучше всего диагностировать, выявляя общий шаблон за несколько дней или недель подряд.

Клиент может помочь в диагностике. Для этого ему следует вносить в журнал все ошибки, с которыми он сталкивается. Может появиться возможность согласования записи журнала с данными ошибки, который сам регистрирует внутри базы данных SQL.

Enterprise Library 6 (EntLib60) предлагает .NET управляемые классы для помощи с ведением журнала. Дополнительные сведения см. в разделе [5 — просто не работает в журнал: используйте этот блок](http://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Диагностика: проверка системных журналов на наличие ошибок
Ниже приведены некоторые инструкции Transact-SQL SELECT, запрашивающие журналы ошибок и другие сведения.

| Запрос у журнала | ОПИСАНИЕ |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |[Sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) представление предоставляет сведения об отдельных событиях, которые включает в себя некоторые, которая может вызвать временных ошибок или сбоев подключения к.<br/><br/>В идеальном случае вы можете соотнести **start_time** или **end_time** значения со сведениями о при клиентской программе возникших проблем.<br/><br/>Необходимо подключиться к *master* базы данных для выполнения этого запроса. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |[Sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) , то есть объединенное число типов событий для дополнительной диагностики.<br/><br/>Необходимо подключиться к *master* базы данных для выполнения этого запроса. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Диагностика: поиск событий проблемы в журнале базы данных SQL
Можно выполнить поиск записей о событиях проблемы в журнале базы данных SQL. Попробуйте выполнить в базе данных *master* такую Transact-SQL-инструкцию SELECT:

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


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Несколько возвращенных строк из sys.fn_xe_telemetry_blob_target_read_file
Следующий пример показывает, как может выглядеть возвращенную строку. Отображаемые пустые значения могут не быть пустыми в других строках.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6
Enterprise Library 6 (EntLib60) — это платформа классов .NET, поможет реализовать надежную клиентов облачных служб, один из которых — это служба базы данных SQL. Найти разделы, выделенных для каждой области, в которой может помочь EntLib60 [Enterprise Library 6 — апрель 2013 г.](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)

Логику повторных попыток для обработки временных ошибок — одну область, в которой может помочь EntLib60. Дополнительные сведения см. в разделе [4 - Perseverance, секрет все triumphs: использовать блок обработки временных сбоев приложения](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> EntLib60 исходный код доступен для загрузки из [центра загрузки](http://go.microsoft.com/fwlink/p/?LinkID=290898). Корпорация Майкрософт не планирует обновлять функции и менять характер обслуживания библиотеки EntLib.
> 
> 

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Классы EntLib60 для временных ошибок и повторов
Следующие классы EntLib60 особенно полезны для логики повторных ошибок. Все эти классы находятся в или в пространстве имен **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

В пространстве имен **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

* **RetryPolicy** ;
  
  * **ExecuteAction** ;
* **ExponentialBackoff** ;
* **SqlDatabaseTransientErrorDetectionStrategy** ;
* **ReliableSqlConnection** ;
  
  * **ExecuteCommand** ;

В пространстве имен **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

* **AlwaysTransientErrorDetectionStrategy** ;
* **NeverTransientErrorDetectionStrategy** ;

Ниже приведены некоторые ссылки на информацию о EntLib60.

* Загрузка книги свободного: [руководство разработчика по Microsoft Enterprise Library, 2-е издание](http://www.microsoft.com/download/details.aspx?id=41145).
* Рекомендации: в статье [Общие рекомендации по повторным попыткам](../best-practices-retry-general.md) содержится подробное обсуждение логики повторных попыток.
* NuGet загрузки: [Enterprise Library — временные ошибки Handling Application Block 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: блок ведения журнала
* Блок ведения журнала — гибкими и можно настроить решение, которое можно использовать для:
  
  * создавать и хранить сообщения журнала в разных расположениях;
  * классифицировать и фильтровать сообщения;
  * собирать контекстную информацию, полезную для отладки, трассировки, аудита и выполнения общих требований к ведению журнала.
* Блок ведения журнала отделено функции ведения журнала и журнала назначения, чтобы код приложения согласована, независимо от того, расположение и тип ведения журнала целевого хранилища.

Дополнительные сведения см. в разделе [5 — просто не работает в журнал: используйте этот блок](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Исходный код метода EntLib60 IsTransient
Ниже приведен исходный код (на языке C#) метода **IsTransient** из класса **SqlDatabaseTransientErrorDetectionStrategy**. Исходный код уточняет, какие ошибки рассматривались как временные и заслуживает повторных попыток, по состоянию на апрель 2013 г.

```csharp
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


## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об устранении неполадок подключения базы данных SQL см. в разделе [Устранение неполадок подключения к базе данных SQL Azure](sql-database-troubleshoot-common-connection-issues.md).
* [Библиотеки подключений для Базы данных SQL и SQL Server](sql-database-libraries.md)
* [SQL Server пулы соединений (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
* [*Повторная попытка* — лицензии Apache 2.0 общего назначения, повторная попытка библиотеки, написанные на Python,](https://pypi.python.org/pypi/retrying) чтобы упростить задачу добавления характер повторения практически что угодно.


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

