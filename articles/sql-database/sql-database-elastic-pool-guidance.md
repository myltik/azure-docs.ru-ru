<properties 
	pageTitle="Вопросы цены и производительности для пулов эластичных баз данных Базы данных SQL Azure" 
	description="Эластичный пул баз данных — это набор доступных ресурсов, совместно используемых группой эластичных баз данных. В этом документе представлены рекомендации, которые помогут оценить целесообразность использования пула эластичных баз данных для группы баз данных." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/26/2016" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Вопросы цены и производительности для пула эластичных баз данных


Оцените, является ли использование пула эластичных баз данных экономически эффективным, учитывая закономерности использования и ценовые различия между пулом эластичных баз данных и отдельными базами данных. Чтобы определить текущий размер пула, необходимый для существующего набора баз данных SQL, также предоставляются дополнительные рекомендации.

- Общие сведения о пулах эластичных баз данных см. в разделе [Пулы эластичных баз данных для баз данных SQL](sql-database-elastic-pool.md).
- Дополнительные сведения о пулах эластичных баз данных см. в разделе [Справочник по пулу эластичных баз данных для баз данных SQL](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE] Сейчас пулы эластичных баз данных предоставляются в виде предварительной версии, которая доступна только с серверами Базы данных SQL версии 12.

## Пулы эластичных баз данных

Независимые поставщики программных продуктов SaaS разрабатывают приложения, созданные на основе крупных уровней данных, состоящих из нескольких баз данных. Общий шаблон приложения предоставляет каждому клиенту его собственную базу данных. Но разные клиенты по-разному и непредсказуемо используют приложения, в связи с этим трудно спрогнозировать требования к ресурсам каждого пользователя базы данных. Поэтому независимый поставщик программных продуктов может закладывать избыточные ресурсы, значительно увеличив расходы, чтобы обеспечить подходящую пропускную способность и время отклика для всех баз данных. Иначе независимый поставщик программных продуктов может сэкономить и подвергнуть риску производительность своих клиентов.

Пулы эластичных баз данных в Базе данных SQL Azure позволяют независимым поставщикам программных продуктов SaaS оптимизировать соотношение цены и производительности для группы баз данных с назначенным бюджетом и обеспечить эластичность производительности для каждой базы данных. Пулы эластичных баз данных позволяют независимым поставщикам программного обеспечения покупать единицы транзакций эластичных баз данных (eDTU) для пула, который совместно используется несколькими базами данных, чтобы обеспечить работу отдельных баз данных в непрогнозируемые периоды использования. Требование к DTU для пула определяется совокупным использованием его баз данных. Количество единиц DTU, доступных для пула, ограничено бюджетом независимого поставщика программных продуктов. Благодаря пулам эластичных баз данных независимому поставщику программных продуктов проще обосновать влияние бюджета на производительность их пула и наоборот. Независимый поставщик программных продуктов просто добавляет базы данных в пул, задает необходимые гарантии DTU или ограничения для баз данных, затем задает единицы DTU для пула на основе своего бюджета. Используя пулы эластичных баз данных, независимые поставщики программных продуктов переводят качество своих услуг с уровня небольшой новой компании на уровень состоявшегося бизнеса с возможностями роста.
  


## Когда необходимо рассматривать вариант с пулом эластичных баз данных

Пулы эластичных баз данных хорошо подходят для большого числа баз данных с конкретными закономерностями использования. Для заданной базы данных эта закономерность характеризуется низким средним использованием с относительно редкими пиками использования.

Чем больше баз данных можно добавить в пул, тем больше экономия. Но в зависимости от закономерности использования приложения можно увидеть экономию уже для 2 баз данных S3.

В разделах ниже содержатся сведения, которые помогут вам понять, как оценить преимущества использования пула эластичных баз данных для определенного набора баз данных. В примерах используются пулы эластичных баз данных Standard, однако эти же принципы применимы к пулам Basic и Premium.

### Оценка закономерностей использования баз данных

На следующем рисунке показан пример базы данных, которая большую часть времени находится в режиме ожидания, но также у нее периодически возникают пики активности. Такая закономерность использования хорошо подходит для пула эластичных баз данных:
 
   ![одна база данных][1]

Для одночасового периода времени, показанного на рисунке выше, пики базы данных DB1 достигают 90 DTU, но общее среднее использование составляет менее 5 DTU. Для обработки такой рабочей нагрузки в отдельной базе данных требуется уровень производительности S3. Однако большая часть ресурсов остается неиспользованной в периоды низкой активности.

Пул эластичных баз данных позволяет совместно использовать эти незадействованные единицы DTU между несколькими базами данных, сокращая тем самым общее число необходимых единиц DTU и уменьшая расходы.

На основе предыдущего примера предположим, что существуют дополнительные базы данных с закономерностями использования, схожими с закономерностью использования базы данных DB1. На следующих двух рисунках использование 4 и 20 баз данных представлено на одном графике, чтобы показать, что их использование не накладывается во времени:

   ![четыре базы данных][2]

   ![двадцать баз данных][3]

Совокупное использование DTU для всех 20 баз данных показано черной линией на рисунке выше. На рисунке видно, что совокупное использование DTU никогда не превышает 100 DTU, и показано, что 20 баз данных могут совместно использовать 100 eDTU в этом периоде времени. Это приводит к 20-кратному сокращению количества единиц DTU и 13-кратному снижению цены по сравнению с размещением каждой базы данных на уровнях производительности S3 для отдельных баз данных.


Этот пример является идеальным по следующим причинам.

- Существует большая разница между пиковым использованием и средним использованием для каждой базы данных.  
- Пиковое использование для каждой базы данных возникает в разное время. 
- Единицы eDTU совместно используются большим числом баз данных.


Цена пула эластичных баз данных зависит от количества единиц eDTU пула и количества баз данных в нем. Хотя цена единицы eDTU для пула в 1,5 раза выше цены единицы DTU для отдельной базы данных, **единицы eDTU пула могут совместно использовать множество баз данных, поэтому во многих случаях требуется меньшее общее количество единиц eDTU**. Эти различия в цене и совместное использование единиц eDTU является основой возможности экономии, которую может предоставить пул.

<br>

Следующие практические правила, относящиеся к количеству баз данных и использованию баз данных, помогут обеспечить уменьшение цены пула эластичных баз данных по сравнению с использованием уровней производительности для отдельных баз данных.


### Минимальное число баз данных

Если сумма DTU уровней производительности для одной базы данных превышает 1,5 единицы eDTU, необходимых для пула, использовать эластичный пул будет более экономично. Доступные размеры см. в разделе [eDTU и размеры хранилища для эластичных пулов и эластичных баз данных](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


***Пример.***<br> Не менее 2 баз данных S3 или 15 баз данных S0 необходимо для пула эластичных баз данных со 100 единицами eDTU, чтобы этот вариант был более экономичным, чем использование уровней производительности для отдельных баз данных.



### Максимальное число баз данных с одновременными пиками

Из-за совместного использования единиц eDTU не все базы данных в пуле могут одновременно использовать предельные значения единиц eDTU, которые доступны при использовании уровней производительности для отдельных баз данных. Чем меньше баз данных с одновременными пиками, тем меньше значение для единиц eDTU можно задавать, и тем более экономичным становится пул. Как правило, в пуле должно быть не более 2/3 (или 67 %) от всего числа баз данных с одновременным пиком до предельных значений единиц eDTU.

***Пример.***<br> Чтобы сократить затраты для 3 баз данных S3 в пуле с 200 единицами eDTU, одновременно испытывать пиковые нагрузки могут не более 2 баз данных в пуле. В противном случае, если пиковую нагрузку одновременно испытывают более 2 из этих 4 баз данных S3, для пула необходимо выбрать размер более 200 eDTU. И если размер пула увеличится до значения единиц eDTU более 200, потребуется добавить больше баз данных S3, чтобы цена была ниже, чем у уровней производительности для отдельных баз данных.


Обратите внимание, что в данном примере не учитывается использование других баз данных в пуле. Если все базы данных используются в любой заданной временной точке, тогда одновременно испытывать пиковую нагрузку могут менее 2/3 (или 67 %) от числа баз данных.


### Использование единиц DTU на одну базу данных

Большая разница между пиковым и средним использованием базы данных указывает на продолжительные периоды низкого уровня использования и короткие периоды высокого уровня использования. Эта закономерность использования идеально подходит для совместного использования ресурсов несколькими базами данных. Следует рассматривать помещение базы данных в пул, если ее загрузка пикового использования в 1,5 раза больше загрузки среднего использования.

    
***Пример.***<br> База данных S3 с пиковым использованием 100 единиц DTU и средним использованием 67 единиц DTU или менее хорошо подходит для совместного использования единиц eDTU в пуле эластичных баз данных. Кроме того, база данных S1 с пиковым использованием 20 единиц DTU и средним использованием 13 единиц DTU или меньше хорошо подходит для пула эластичных баз данных.
    

## Алгоритм сравнения разницы цен между пулом эластичных баз данных и отдельными базами данных 

Следующий алгоритм может помочь оценить, какой вариант является более экономичным, пул эластичных баз данных или отдельные базы данных.

1. Оцените количество единиц eDTU, необходимых для пула, следующим образом:
    
    Макс.(*Общее число баз данных* * *среднее использование единиц DTU на базу данных*, *число баз данных с одновременными пиками* * *пиковое использование единиц DTU на базу данных*)

2. Выберите наименьшее доступное значение единиц eDTU для пула, которое превосходит оценку из действия 1. Для доступных вариантов eDTU изучите действительные значения для единиц eDTU, перечисленных здесь: [eDTU и размеры хранилища для пулов эластичных баз данных и эластичных баз данных](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


3. Рассчитайте цену за пул следующим образом:

    Цена пула = *количество единиц в пуле eDTU* * *цена за единицу пула eDTU*

    Информацию о ценах см. в разделе [База данных SQL. Цены](https://azure.microsoft.com/pricing/details/sql-database/).


4. Сравните цену пула из действия 3 с ценой использования подходящих уровней производительности отдельных баз данных.



## Определение наилучшего размера eDTU пула для существующих баз данных SQL 

Наилучший размер для пула эластичных баз данных зависит от совокупного числа единиц eDTU и ресурсов хранилища, необходимых для всех баз данных в пуле. Для этого необходимо определить наибольшую из следующих двух величин:

* Максимальное число единиц DTU, используемых всеми базами данных в пуле.
* Максимальное число байтов памяти, используемых всеми базами данных в пуле. 

Доступные размеры см. в разделе [eDTU и размеры хранилища для эластичных баз данных и пулов эластичных баз данных](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases).


### Используйте советник по уровням служб (STA) и динамические административные представления (DMVs) для получения рекомендаций о размере   

В STA и DMV предоставлены различные средства и возможности для определения размера пула эластичных баз данных. Независимо от использованного средства оценку размера следует использовать только для первоначального анализа и создания пулов эластичных баз данных. После создания пула использование его ресурсов следует точно отслеживать и регулировать настройки пула соответствующим образом.

**STA**<br>STA — это встроенное средство [портала Azure](https://portal.azure.com), которое автоматически оценивает использование ресурсов баз данных на существующем сервере базы данных SQL за прошедший период времени и рекомендует подходящую конфигурацию пула эластичных баз данных. Дополнительные сведения см. в разделе [Рекомендации по выбору ценовой категории для пула эластичных баз данных](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations).

**Средство определения размера динамических административных представлений**<br>Средство определения размера динамических административных представлений предоставляется в виде сценария PowerShell и позволяет настраивать оценки размера пула эластичных баз данных для существующих баз данных на сервере.

### Выбор между средствами STA и DMV 

Выберите средство, которое подходит для анализа вашего конкретного приложения. В следующей таблице приведены различия между этими 2 средствами для определения размера:

| Функция | STA | DMV |
| :--- | :--- | :--- |
| Степень детализации образцов данных, используемых в анализе. | 15 секунд | 15 секунд |
| Учет различий в цене между пулом и уровнями производительности для отдельных баз данных. | Да | Нет |
| Возможность настройки списка баз данных, проанализированных на сервере. | Нет | Да |
| Возможность настройки периода времени, используемого в анализе. | Нет | Да |


### Оценка размера пула эластичных БД с помощью STA  

STA оценивает историю использования баз данных и рекомендует пул эластичных баз данных, когда это более экономично по сравнению с использованием уровней производительности отдельных баз данных. Если рекомендуется использовать пул, средство предоставляет список рекомендованных баз данных, а также рекомендованное число единиц eDTU пула, минимальные и максимальные настройки единиц eDTU для каждой эластичной базы данных. Чтобы база данных рассматривалась для включения в пул, она должна существовать не менее 7 дней.

Средство STA доступно на портале при добавлении пула эластичных баз данных на существующий сервер. Если рекомендации для пула эластичных баз данных доступны для этого сервера, они отображаются на странице создания "Пул эластичных баз данных". Клиенты могут в любое время изменить рекомендованные конфигурации для создания своих групп пулов эластичных баз данных.

Дополнительные сведения см. в разделе [Рекомендации по выбору ценовой категории для пула эластичных баз данных](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations).

### Оценка размера эластичных БД с помощью динамических административных представлений (DMV) 

Средство динамических административных представлений [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) измеряет использование ресурсов отдельной базы данных. Это средство DMV предоставляет данные о загрузке ЦП, операциях ввода-вывода, ведении журнала и журнал использования базы данных в виде процента от ограничения уровня производительности базы данных. Эти данные можно использовать для расчета использования единиц DTU базы данных в любом заданном 15-секундном интервале.

Совокупное использование единиц DTU для пула эластичных баз данных в 15-секундном интервале можно оценить, сложив использование единиц eDTU для всех рассматриваемых баз данных за этот период времени. В зависимости от конкретных целей для производительности может понадобиться отбросить небольшой процент образцовых данных. Например, значение 99-го процентиля совокупных единиц eDTU за все временные интервалы можно применить для исключения выбросов и обеспечения того, что единицы eDTU пула эластичных баз данных соответствуют 99 % от образцовых временных интервалов.

## Сценарий PowerShell для оценки совокупного использования единиц eDTU ваших баз данных

Здесь представлен образец сценария PowerShell для оценки совокупных значений eDTU для баз данных пользователя на сервере Базы данных SQL.

Сценарий собирает данные, только когда запущен. Для получения типичной производственной нагрузки следует, чтобы сценарий работал не менее дня, хотя для получения более точной оценки требуется работа в течение недели или даже больше. Запустите сценарий на время, которое позволит отразить типичную рабочую нагрузку ваших баз данных.

> [AZURE.IMPORTANT] Окно PowerShell должно быть открыто во время работы сценария. Не закрывайте окно PowerShell, пока сценарий не проработает достаточное количество времени и собранных данных не будет хватать для отражения типичной рабочей нагрузки с периодами нормального и пикового использования.

### Предварительные требования сценария 

Перед запуском сценария установите следующие элементы:

- Новые [средства командной строки Powershell](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
- [Пакет дополнительных компонентов SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=42295).


### Сведения о сценарии


Можно запустить сценарий с локального компьютера или виртуальной машины в облаке. Если запустить сценарий на локальном компьютере, с вас могут взять плату за передачу данных, поскольку сценарию необходимо загружать данные из целевых баз данных. Ниже показана оценка объема данных на основе числа целевых баз данных и продолжительности работы сценария. Цены на передачу данных Azure см. в разделе [Сведения о ценах — передача данных](https://azure.microsoft.com/pricing/details/data-transfers/).
       
 -     1 база данных в час = 38 КБ
 -     1 база данных в день = 900 КБ
 -     1 база данных в неделю = 6 МБ
 -     100 баз данных в день = 90 МБ
 -     500 баз данных в неделю = 3 ГБ

Сценарий исключает определенные базы данных, которые не подходят для текущего предложения по общедоступной предварительной версии для уровня Standard пулов эластичных БД. Если необходимо исключить дополнительные базы данных с целевого сервера, можно изменить сценарий для соответствия вашим критериям. По умолчанию сценарий не собирает информацию для следующих баз данных:

* Эластичных баз данных (баз данных, которые уже находятся в пуле эластичных БД).
* Главной базы данных сервера.

Сценарию требуется выходная база данных, чтобы хранить промежуточные данные для анализа. Можно использовать новую или существующую базу данных. Хотя это не является техническим требованием, необходимым для работы средства, выходная база данных должна располагаться на другом сервере во избежание влияния на результат анализа. Предложите уровень производительности выходной базы данных, соответствующий S0 или выше. При сборе данных в течение продолжительного промежутка времени для большого числа баз данных можно рассмотреть вариант увеличения уровня производительности выходной базы данных.

Для сценария необходимо предоставить учетные данные для подключения к целевому серверу (кандидату на включение в пул эластичных баз данных) с указанием полного имени сервера, например abcdef.database.windows.net. В настоящее время сценарий не поддерживает одновременный анализ нескольких серверов.


После отправки значений для первоначальной настройки параметров предлагается войти в учетную запись Azure. Это делается для выполнения входа на целевом сервере, а не на сервере выходной базы данных.
	
Если во время работы сценария отображаются следующие предупреждения, их можно игнорировать:

- ВНИМАНИЕ! Командлет Switch-AzureMode является устаревшим.
- ВНИМАНИЕ! Не удалось получить информацию о службе SQL Server. Сбой попытки подключиться к WMI в Microsoft.Azure.Commands.Sql.dll со следующей ошибкой: сервер RPC недоступен.

Когда сценарий завершает работу, он выдает оценочное число единиц eDTU, необходимых для пула эластичных баз данных, чтобы содержать все рассматриваемые базы данных на целевом сервере. Оценку числа единиц eDTU можно использовать для создания и конфигурации пула эластичных баз данных для этих баз данных. После создания пула и перемещения баз данных в пул следует внимательно следить за пулом в течение нескольких дней и при необходимости вносить изменения в конфигурацию eDTU пула.

> [AZURE.IMPORTANT] Этот сценарий содержит команды для Azure PowerShell версии вплоть до 1.0, *но не включая* саму версию 1.0 и более поздние версии. Используемую версию Azure PowerShell можно проверить с помощью команды **Get-Module azure | format-table version**. Дополнительные сведения см. в разделе [Устаревший командлет Switch-AzureMode в Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).


    
    param (
    [Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
    [Parameter(Mandatory=$true)][string]$username, # user name
    [Parameter(Mandatory=$true)][string]$serverPassword, # password
    [Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
    [Parameter(Mandatory=$true)][string]$outputdatabaseName,
    [Parameter(Mandatory=$true)][string]$outputDBUsername,
    [Parameter(Mandatory=$true)][string]$outputDBpassword,
    [Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
    )
    
    Add-AzureAccount 
    Select-AzureSubscription $AzureSubscriptionName
    Switch-AzureMode AzureResourceManager
    
    $server = Get-AzureSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    
    # Check version/upgrade status of the server
    $upgradestatus = Get-AzureSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    $version = ""
    if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
    {
    $version = $upgradestatus.Status
    }
    else
    {
    $version = $server.ServerVersion
    }
    
    # For Elastic database pool candidates, we exclude master, and any databases that are already in a pool. You may add more databases to the excluded list below as needed
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")}
    
    $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
    $destinationTableName = "resource_stats_output"
    
    # Create a table in output database for metrics collection
    $sql = "
    IF  NOT EXISTS (SELECT * FROM sys.objects 
    WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
    
    BEGIN
    Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
    Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
    END
    TRUNCATE TABLE $($destinationTableName);
    "
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 
    
    # waittime (minutes) is interval between data collection queries in the loop below.
    $Waittime = 10
    $end_Time = [DateTime]::UtcNow
    $start_time = $end_time.AddMinutes(-$Waittime)
    $finish_time = $end_Time.AddMinutes($duration_minutes)
    
    While ($end_time -lt $finish_time)
    {
    Write-Host "Collecting metrics..." 
    foreach ($db in $ListOfDBs)
    {
    if ($version -in ("12.0", "Completed")) # for V12 databases 
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'S3' THEN 100
    WHEN 'P1' THEN 125
    WHEN 'P2' THEN 250
    WHEN 'P3' THEN 1000
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    else
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'P1' THEN 100
    WHEN 'P2' THEN 200
    WHEN 'P3' THEN 800
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    
    $result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 3600 
    #bulk copy the metrics to output database
    $bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
    $bulkCopy.BulkCopyTimeout = 600
    $bulkCopy.DestinationTableName = "$destinationTableName";
    $bulkCopy.WriteToServer($result);
    
    }
    
    $start_time = $start_time.AddMinutes($Waittime)
    $end_time = $end_time.AddMinutes($Waittime)
    Write-Host $start_time
    Write-Host $end_time
    do {
    Start-Sleep 1
       }
    until (([DateTime]::UtcNow) -ge $end_time)
    }
    
    Write-Host "Analyzing the collected metrics...."
    # Analysis query that does aggregation of the resource metrics to calculate pool size.
    $sql1 = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
    WITH group_stats AS
    (
    SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
    FROM resource_stats_output 
    WHERE slo LIKE '
    
    $sql2 = '
    GROUP BY end_time
    )
    -- calculate aggregate storage and DTUs for all DBs in the group
    , group_DTU AS
    (
    SELECT end_time, avg_group_Storage, 
    (SELECT Max(v)
       FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
    FROM group_stats
    )
    -- Get top 1 percent of the storage and DTU utilization samples.
    , top1_percent AS (
    SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
    )
    
    -- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
    --SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
    SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
    IF @DTU_Storage > @DTU_Perf_99 
    SELECT ''Total number of DTUs dominated by storage: '' + convert(varchar(100), @DTU_Storage)
    ELSE 
    SELECT ''Total number of DTUs dominated by resource consumption: '' + convert(varchar(100), @DTU_Perf_99)'
    
    #check if there are any web/biz edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Shared*")
    {
    write-host "`nWeb/Business edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any basic edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'Basic%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Basic*")
    {
    write-host "`nBasic edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Basic%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]} 
    }
    
    #check if there are any standard edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'S%' AND slo NOT LIKE 'Shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "S*")
    {
    write-host "`nStandard edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'S%' AND slo NOT LIKE 'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any premium edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'P%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "P*")
    {
    write-host "`nPremium edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'P%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
        

## Сводка

Не все отдельные базы данных являются оптимальными кандидатами на включение в пулы эластичных баз данных. Базы данных с закономерностями использования, которые характеризуются низким средним уровнем использования и относительно редкими пиками использования, прекрасно подходят для включения в пул эластичных баз данных. Закономерности использования приложений являются динамическими, поэтому используйте информацию и инструменты, описанные в данной статье, чтобы выполнить первоначальную оценку и понять, является ли пул эластичных баз данных хорошим вариантом для некоторых или всех ваших баз данных. В этой статье представлены лишь начальные данные, которые помогут вам принять решение, подходит вам или нет пул эластичных баз данных. Помните, что следует постоянно отслеживать использование ресурсов за прошедшие периоды времени (с помощью STA или DMVs) и выполнять переоценку уровней производительности всех ваших баз данных. Не забывайте, что можно с легкостью переместить базы данных в пул эластичных баз данных и из него, и, если у вас очень много баз данных, можно иметь несколько пулов различного размера, между которыми можно разделить свои базы данных.



<!--Image references-->
[1]: ./media/sql-database-elastic-pool-guidance/one-database.png
[2]: ./media/sql-database-elastic-pool-guidance/four-databases.png
[3]: ./media/sql-database-elastic-pool-guidance/twenty-databases.png

<!---HONumber=AcomDC_0302_2016-->