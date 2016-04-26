<properties
	pageTitle="Анализ данных Twitter с помощью Hadoop в HDInsight | Microsoft Azure"
	description="Узнайте, как использовать Hive для анализа данных Twitter с помощью Hadoop в HDInsight, чтобы определить частоту употребления конкретного слова."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="jgao"/>

# Анализ данных Twitter с помощью Hive в HDInsight

Социальные веб-сайты являются одной из основных движущих сил для внедрения данных большого размера. Общедоступные API, предоставляемые сайтами, такими как Twitter, — полезный источник данных для анализа и понимания популярных тенденций. В этом учебнике вы будете получать твиты с помощью API потоковой передачи Twitter, а затем с помощью Apache Hive в Azure HDInsight будете получать список пользователей Twitter, отправивших большинство твитов, которые содержат определенное слово.

> [AZURE.NOTE] Действия, описанные в этом документе, требуют наличия кластера HDInsight на основе Windows. Инструкции по работе с кластерами для Linux см. в статье [Анализ данных Twitter с помощью Hive в HDInsight \(Linux\)](hdinsight-analyze-twitter-data-linux.md).



> [AZURE.TIP] Аналогичный пример имеется в коллекции примеров HDInsight. Смотрите видео Channel 9: <a href="http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Analyze-Twitter-trend-using-Apache-Hive-in-HDInsight" target="_blank">Анализ тенденций Twitter с помощью Apache Hive в HDInsight</a>.

###Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Рабочая станция**, на которой установлена и настроена среда Azure PowerShell. См. [Установка и использование Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Для выполнения скриптов Windows PowerShell необходимо запустить Azure PowerShell с правами администратора и задать политику выполнения *RemoteSigned*. См. раздел [Выполнение скриптов Windows PowerShell][powershell-script].

	Перед выполнением скриптов Windows PowerShell убедитесь, что вы подключены к подписке Azure, с помощью следующего командлета:

		Login-AzureRmAccount

	При наличии нескольких подписок Azure используется следующий командлет для установки текущей подписки:

		Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>


- **Кластер Azure HDInsight**. Инструкции по подготовке кластеров см. в разделе [Приступая к работе с HDInsight][hdinsight-get-started] или [Подготовка кластеров HDInsight][hdinsight-provision]. Имя кластера потребуется позже в данном учебнике.

В следующей таблице перечислены файлы, используемые в этом учебнике:

Файлы|Описание
---|---
/tutorials/twitter/data/tweets.txt|Исходные данные для задания Hive.
/tutorials/twitter/output|Выходная папка для задания Hive. Имя выходного файла задания Hive по умолчанию — **000000\_0**.
tutorials/twitter/twitter.hql|Файл скрипта HiveQL.
/tutorials/twitter/jobstatus|Состояние задания Hadoop.


##Получение канала Twitter

В этом учебнике будут использоваться [интерфейсы API потоковой передачи Twitter][twitter-streaming-api]. В частности, вы будете использовать API [statuses/filter][twitter-statuses-filter].

>[AZURE.NOTE] Файл, содержащий 10 000 твитов, и файл сценария \(рассматривается в следующем разделе\) были переданы в общедоступный контейнер BLOB-объектов. Вы можете пропустить этот раздел, если хотите использовать переданные файлы.

[Данные твитов](https://dev.twitter.com/docs/platform-objects/tweets) хранятся в формате JSON в виде сложных вложенных структур. Вместо того чтобы писать много строк кода с использованием обычного языка программирования, эту вложенную структуру можно преобразовать в таблицу Hive, чтобы к ней можно было отправлять запросы на SQL-подобном языке — HiveQL.

Twitter использует протокол OAuth для обеспечения авторизованного доступа к API. OAuth — это протокол проверки подлинности, с помощью которого пользователи разрешают приложению действовать от их имени без предоставления их паролей. Дополнительные сведения можно найти на веб-сайте [oauth.net](http://oauth.net/) или в превосходном [руководстве по OAuth для начинающих](http://hueniverse.com/oauth/) от Hueniverse.

Первый шаг для начала использования OAuth состоит в создании нового приложения на сайте разработчиков Twitter.

**Создание приложения Twitter**

1. Войдите на веб-сайт [https://apps.twitter.com/](https://apps.twitter.com/). Щелкните ссылку **Войти сейчас**, если у вас нет учетной записи Twitter.
2. Щелкните **Создать новое приложение**.
3. Введите **Имя**, **Описание**, **Веб-сайт**. В поле **Веб-сайт** можно использовать URL-адрес. В следующей таблице приведены некоторые примеры значений:

Поле|Значение
---|---
Имя|MyHDInsightApp
Описание|MyHDInsightApp
Веб-сайт|http://www.myhdinsightapp.com

4. Установите значок **Да, я согласен** и нажмите кнопку **Создать приложение Twitter**.
5. Выберите вкладку **Разрешения**. Разрешение по умолчанию: **Только для чтения**. Этого разрешения достаточно для данного учебника.
6. Перейдите на вкладку **Ключи и токены доступа**.
7. Нажмите кнопку **Создать маркер доступа**.
8. Нажмите **Проверить OAuth** в правом верхнем углу страницы.
9. Запишите **ключ клиента**, **Секрет клиента**, **Маркер доступа** и **Секрет маркера доступа**. Они потребуются позже в данном руководстве.

В этом учебнике для вызова веб-службы будет использоваться Windows PowerShell. Пример .NET C\# см. в разделе [Анализ мнений пользователей Twitter в режиме реального времени с использованием HBase в HDInsight][hdinsight-hbase-twitter-sentiment]. Также можно использовать другое популярное средство [*Curl*][curl]. Curl можно загрузить [здесь][curl-download].

>[AZURE.NOTE] При использовании команды Curl в Windows указывайте значения параметров в двойных кавычках вместо одинарных.

**Получение твитов**

1. Откройте интегрированную среду сценариев \(ISE\) Windows PowerShell. \(На начальном экране Windows 8 введите **PowerShell\_ISE**, а затем щелкните элемент **Windows PowerShell ISE**. См. раздел [Запуск Windows PowerShell в Windows 8 и Windows][powershell-start].\)

2. Скопируйте следующий скрипт в область скриптов:

		#region - variables and constants
		$clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

		# Enter the OAuth information for your Twitter application
		$oauth_consumer_key = "<TwitterAppConsumerKey>";
		$oauth_consumer_secret = "<TwitterAppConsumerSecret>";
		$oauth_token = "<TwitterAppAccessToken>";
		$oauth_token_secret = "<TwitterAppAccessTokenSecret>";

		$destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

		$trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
		$track = [System.Uri]::EscapeDataString($trackString);
		$lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
		#endregion

		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Login-AzureRmAccount
		#endregion

		#region - Create a block blob object for writing tweets into Blob storage
		Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
		$myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
		$resourceGroupName = $myCluster.ResourceGroup
		$storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
		$containerName = $myCluster.DefaultStorageContainer
		Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
		Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

		Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
		$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName |  %{ $_.Key1 }
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

		Write-Host "Create block blob object ..." -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		#end region

		# region - Format OAuth strings
		Write-Host "Format oauth strings ..." -ForegroundColor Green
		$oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
		$ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
		$oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

		$signature = "POST&";
		$signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
		$signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
		$signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
		$signature += [System.Uri]::EscapeDataString("track=" + $track);

		$signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

		$hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
		$hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
		$oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

		$oauth_authorization = 'OAuth ';
		$oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
		$oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
		$oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
		$oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
		$oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
		$oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
		$oauth_authorization += 'oauth_version="1.0"';

		$post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
		#endregion

		#region - Read tweets
		Write-Host "Create HTTP web request ..." -ForegroundColor Green
		[System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
		$request.Method = "POST";
		$request.Headers.Add("Authorization", $oauth_authorization);
		$request.ContentType = "application/x-www-form-urlencoded";
		$body = $request.GetRequestStream();

		$body.write($post_body, 0, $post_body.length);
		$body.flush();
		$body.close();
		$response = $request.GetResponse() ;

		Write-Host "Start stream reading ..." -ForegroundColor Green

		Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream

		$sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

		$inrec = $sReader.ReadLine()
		$count = 0
		while (($inrec -ne $null) -and ($count -le $lineMax))
		{
			if ($inrec -ne "")
			{
				Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

				$writeStream.WriteLine($inrec)
				$count ++
			}

			$inrec=$sReader.ReadLine()
		}
		#endregion

		#region - Write tweets to Blob storage
		Write-Host "Write to the destination blob ..." -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream)

		$sReader.close()
		#endregion

		Write-Host "Completed!" -ForegroundColor Green

3. Задайте первые 5-8 переменных сценария:


Переменная|Описание
---|---
$clusterName|Это имя кластера HDInsight, в котором вы хотите запустить приложение.
$oauth\_consumer\_key|Это **ключ клиента** приложения Twitter, который вы записали ранее при создании приложения.
$oauth\_consumer\_secret|Это **секрет клиента** приложения Twitter, который вы записали ранее.
$oauth\_token|Это **маркер доступа** приложения Twitter, который вы записали ранее.
$oauth\_token\_secret|Это **секрет маркер доступа** приложения Twitter, который вы записали ранее.
$destBlobName|Это имя выходной папки. Значение по умолчанию: **tutorials/twitter/data/tweets.txt**. Если вы изменяете значение по умолчанию, то нужно будет соответствующим образом обновить скрипты Windows PowerShell.
$trackString|Веб-служба будет возвращать твиты, связанные с этими ключевыми словами. Значение по умолчанию: **Azure, Cloud, HDInsight**. Если вы изменяете значение по умолчанию, то должны соответствующим образом обновить скрипты Windows PowerShell.
$lineMax|Это значение определяет, сколько твитов будет считывать скрипт. Чтение 100 твитов занимает около трех минут. Можно задать большее число, но для загрузки потребуется больше времени.

5. Нажмите клавишу **F5** для запуска скрипта. Если у вас возникли проблемы, попробуйте выбрать все строки, а затем нажмите клавишу **F8**.
6. В конце выходных данных будет показана строка "Complete!". Все сообщения об ошибке выделяются красным цветом.

В качестве проверки можно изучить выходной файл **/tutorials/twitter/data/tweets.txt** в хранилище больших двоичных объектов Azure с помощью проводника хранилища Azure или Azure PowerShell. Пример скрипта Windows PowerShell для перечисления файлов см. в разделе [Использование хранилища больших двоичных объектов с HDInsight][hdinsight-storage-powershell].



##Создание скрипта HiveQL

С помощью Azure PowerShell можно одновременно запустить несколько инструкций HiveQL по одной за раз или упаковать инструкцию HiveQL в файл скрипта. В этом учебнике будет создан скрипт HiveQL. Этот файл скрипта необходимо отправить в хранилище больших двоичных объектов Azure. В следующем разделе вы будете выполнять файл скрипта с помощью Azure PowerShell.

>[AZURE.NOTE] Файл сценария и файл, содержащий 10 000 твитов, были переданы в общедоступный контейнер BLOB-объектов. Вы можете пропустить этот раздел, если хотите использовать переданные файлы.

Скрипт HiveQL выполнит следующее:

1. **Удаляет таблицу tweets\_raw**, если она уже существует.
2. **Создает таблицу Hive tweets\_raw**. Эта временная структурированная таблица Hive содержит данные для дальнейшей обработки операций ETL \(извлечения, преобразования и загрузки\). Сведения о разделах см. в [учебнике по Hive][apache-hive-tutorial]  
3. **Загрузит данные** из исходной папки /tutorials/twitter/data. Большой набор данных tweets во вложенном формате JSON теперь преобразован во временную табличную структуру Hive.
3. **Удаляет таблицу tweets**, если она уже существует.
4. **Создает таблицу tweets**. Прежде чем выполнять запросы в набор данных tweets с помощью Hive, необходимо запустить другой процесс ETL. Он определяет более подробную схему таблицы для данных, которые хранятся в таблице twitter\_raw.  
5. **Вставляет таблицу для перезаписи**. Этот сложный скрипт Hive запускает ряд длительных заданий MapReduce в кластере Hadoop. В зависимости от набора данных и размера кластера процесс может занять около 10 минут.
6. **Вставляет каталог для перезаписи**. Выполняет запрос и выводит набор данных в файл. Этот запрос возвращает список пользователей Twitter, которые отправили больше всего твитов со словом Azure.

**Создание скрипта Hive и его отправка в Azure**

1. Откройте интегрированную среду сценариев Windows PowerShell.
2. Скопируйте следующий скрипт в область скриптов:

		#region - variables and constants
		$clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
		$subscriptionID = "<Azure Subscription ID>"
		
		$sourceDataPath = "/tutorials/twitter/data"
		$outputPath = "/tutorials/twitter/output"
		$hqlScriptFile = "tutorials/twitter/twitter.hql"
		
		$hqlStatements = @"
		set hive.exec.dynamic.partition = true;
		set hive.exec.dynamic.partition.mode = nonstrict;
		
		DROP TABLE tweets_raw;
		CREATE EXTERNAL TABLE tweets_raw (
			json_response STRING
		)
		STORED AS TEXTFILE LOCATION '$sourceDataPath';
		
		DROP TABLE tweets;
		CREATE TABLE tweets
		(
			id BIGINT,
			created_at STRING,
			created_at_date STRING,
			created_at_year STRING,
			created_at_month STRING,
			created_at_day STRING,
			created_at_time STRING,
			in_reply_to_user_id_str STRING,
			text STRING,
			contributors STRING,
			retweeted STRING,
			truncated STRING,
			coordinates STRING,
			source STRING,
			retweet_count INT,
			url STRING,
			hashtags array<STRING>,
			user_mentions array<STRING>,
			first_hashtag STRING,
			first_user_mention STRING,
			screen_name STRING,
			name STRING,
			followers_count INT,
			listed_count INT,
			friends_count INT,
			lang STRING,
			user_location STRING,
			time_zone STRING,
			profile_image_url STRING,
			json_response STRING
		);
		
		FROM tweets_raw
		INSERT OVERWRITE TABLE tweets
		SELECT
			cast(get_json_object(json_response, '$.id_str') as BIGINT),
			get_json_object(json_response, '$.created_at'),
			concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
			substr (get_json_object(json_response, '$.created_at'),27,4)),
			substr (get_json_object(json_response, '$.created_at'),27,4),
			case substr (get_json_object(json_response, '$.created_at'),5,3)
				when "Jan" then "01"
				when "Feb" then "02"
				when "Mar" then "03"
				when "Apr" then "04"
				when "May" then "05"
				when "Jun" then "06"
				when "Jul" then "07"
				when "Aug" then "08"
				when "Sep" then "09"
				when "Oct" then "10"
				when "Nov" then "11"
				when "Dec" then "12" end,
			substr (get_json_object(json_response, '$.created_at'),9,2),
			substr (get_json_object(json_response, '$.created_at'),12,8),
			get_json_object(json_response, '$.in_reply_to_user_id_str'),
			get_json_object(json_response, '$.text'),
			get_json_object(json_response, '$.contributors'),
			get_json_object(json_response, '$.retweeted'),
			get_json_object(json_response, '$.truncated'),
			get_json_object(json_response, '$.coordinates'),
			get_json_object(json_response, '$.source'),
			cast (get_json_object(json_response, '$.retweet_count') as INT),
			get_json_object(json_response, '$.entities.display_url'),
			array(
				trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
			array(
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
			trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
			trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
			get_json_object(json_response, '$.user.screen_name'),
			get_json_object(json_response, '$.user.name'),
			cast (get_json_object(json_response, '$.user.followers_count') as INT),
			cast (get_json_object(json_response, '$.user.listed_count') as INT),
			cast (get_json_object(json_response, '$.user.friends_count') as INT),
			get_json_object(json_response, '$.user.lang'),
			get_json_object(json_response, '$.user.location'),
			get_json_object(json_response, '$.user.time_zone'),
			get_json_object(json_response, '$.user.profile_image_url'),
			json_response
		WHERE (length(json_response) > 500);
		
		INSERT OVERWRITE DIRECTORY '$outputPath'
		SELECT name, screen_name, count(1) as cc
			FROM tweets
			WHERE text like "%Azure%"
			GROUP BY name,screen_name
			ORDER BY cc DESC LIMIT 10;
		"@
		#endregion
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		
		Try{
			Get-AzureRmSubscription
		}
		Catch{
			Login-AzureRmAccount
		}
		
		Select-AzureRmSubscription -SubscriptionId $subscriptionID
		
		#endregion
		
		#region - Create a block blob object for writing the Hive script file
		Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
		$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
		$resourceGroupName = $myCluster.ResourceGroup
		$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
		$defaultBlobContainerName = $myCluster.DefaultStorageContainer
		Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
		Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
		
		Write-Host "Define the connection string ..." -ForegroundColor Green
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName | %{$_.key1}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
		
		Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
		$hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
		
		Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		$writeStream.Writeline($hqlStatements)
		#endregion
		
		#region - Write the Hive script file to Blob storage
		Write-Host "Write to the destination blob ... " -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$hqlScriptBlob.UploadFromStream($memStream)
		#endregion
		
		Write-Host "Completed!" -ForegroundColor Green

		

4. Задайте первые две переменные скрипта:

Переменная|Описание
---|---
$clusterName|Введите имя кластера HDInsight, в котором вы хотите запустить приложение.
$subscriptionID|Введите идентификатор подписки Azure.
$sourceDataPath|Расположение хранилища больших двоичных объектов Azure, из которого запросы Hive будут считывать данные. Нет необходимости изменять эту переменную.
$outputPath|Расположение хранилища больших двоичных объектов Azure, куда запросы Hive будут выводить результаты. Нет необходимости изменять эту переменную.
$hqlScriptFile|Расположение и имя файла скрипта HiveQL. Нет необходимости изменять эту переменную.

5. Нажмите клавишу **F5** для запуска скрипта. Если у вас возникли проблемы, попробуйте выбрать все строки, а затем нажмите клавишу **F8**.
6. В конце выходных данных будет показана строка "Complete!". Все сообщения об ошибке выделяются красным цветом.

В качестве проверки можно изучить выходной файл **/tutorials/twitter/twitter.hql** в хранилище больших двоичных объектов Azure с помощью проводника хранилища Azure или Azure PowerShell. Пример скрипта Windows PowerShell для перечисления файлов см. в разделе [Использование хранилища больших двоичных объектов с HDInsight][hdinsight-storage-powershell].


##Обработка данных Twitter с помощью Hive

Подготовительная работа завершена. Теперь можно вызвать скрипт Hive и проверить результаты.

### Отправка задания Hive

Используйте приведенный ниже скрипт Windows PowerShell для запуска скрипта Hive. Вам потребуется задать первую переменную.

>[AZURE.NOTE] Чтобы использовать tweets и скрипт HiveQL, загруженный в последних двух разделах, установите для переменной $hqlScriptFile значение /tutorials/twitter/twitter.hql. Чтобы использовать те, которые были отправлены в общедоступный большой двоичный объект автоматически, установите для переменной $hqlScriptFile значение wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql.

	#region variables and constants
	$clusterName = "<Existing Azure HDInsight Cluster Name>"
	$httpUserName = "admin"
	$httpUserPassword = "<HDInsight Cluster HTTP User Password>"
	
	#use one of the following
	$hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
	$hqlScriptFile = "/tutorials/twitter/twitter.hql"
	
	$statusFolder = "/tutorials/twitter/jobstatus"
	#endregion
	
	$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $myCluster.ResourceGroup
	$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
	
	$defaultBlobContainerName = $myCluster.DefaultStorageContainer
	
	
	#region - Invoke Hive
	Write-Host "Invoke Hive ... " -ForegroundColor Green
	
	# Create the HDInsight cluster
	$pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
	$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
	
	Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential 
	$response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable
	
	Write-Host "Display the standard error log ... " -ForegroundColor Green
	$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
	Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
	#endregion

### Проверка результатов

Используйте приведенный ниже скрипт Windows PowerShell для проверки результатов выполнения задания Hive. Вам потребуется задать две первые переменные.

	#region variables and constants
	$clusterName = "<Existing Azure HDInsight Cluster Name>"
	
	$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
	#engregion
	
	#region - Create an Azure storage context object
	Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
	$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $myCluster.ResourceGroup
	$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
	$defaultBlobContainerName = $myCluster.DefaultStorageContainer
	
	Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
	Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey  
	#endregion
	
	#region - Download blob and display blob
	Write-Host "Download the blob ..." -ForegroundColor Green
	cd $HOME
	Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "Display the output ..." -ForegroundColor Green
	Write-Host "==================================" -ForegroundColor Green
	cat "./$blob"
	Write-Host "==================================" -ForegroundColor Green
	#end region

> [AZURE.NOTE] Таблица Hive использует в качестве разделителя полей \\001. Разделитель не отображается в выходных данных.

После размещения результатов анализа в хранилище больших двоичных объектов Azure можно экспортировать данные в базу данных SQL Azure или на сервер SQL Server, экспортировать данные в Excel с помощью Power Query или подключить приложение к данным с помощью драйвера Hive ODBC. Дополнительные сведения см. в статьях [Использование Sqoop с HDInsight][hdinsight-use-sqoop], [Анализ данных о задержке рейсов с помощью HDInsight][hdinsight-analyze-flight-delay-data], [Подключение Excel к HDInsight с помощью Power Query][hdinsight-power-query] и [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][hdinsight-hive-odbc].

##Дальнейшие действия

В этом учебнике мы рассмотрели, как преобразовать неструктурированный набор данных JSON в структурированную таблицу Hive для запроса, исследования и анализа данных из Twitter с помощью HDInsight в Azure. Дополнительные сведения см. на следующих ресурсах:

- [Приступая к работе с HDInsight][hdinsight-get-started]
- [Анализ мнений пользователей Twitter в режиме реального времени с использованием HBase в HDInsight][hdinsight-hbase-twitter-sentiment]
- [Анализ данных о задержке рейсов с помощью HDInsight][hdinsight-analyze-flight-delay-data]
- [Подключение Excel к HDInsight с помощью Power Query][hdinsight-power-query]
- [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][hdinsight-hive-odbc]
- [Использование Sqoop с HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

<!---HONumber=AcomDC_0413_2016-->