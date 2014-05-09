<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Приступая к работе" pageTitle="Приступая к работе с HDInsight | Windows Azure" metaKeywords="" description="Приступая к работе с HDInsight, решением для обработки больших объемов данных. Узнайте, как подготовить кластеры, выполнить задания MapReduce и передать данные в Microsoft Excel для анализа." metaCanonical="" services="hdinsight" documentationCenter="" title="Приступая к работе с Windows Azure HDInsight" authors=""  solutions="" writer="jgao" manager="paulettm" editor="cgronlun"  />




# Приступая к работе с Windows Azure HDInsight

HDInsight делает библиотеку [Apache Hadoop][apache-hadoop] доступной в виде службы в облаке. Благодаря этому платформа программного обеспечения MapReduce доступна в более простой, более масштабируемой и экономически эффективной среде Windows Azure. Кроме того, HDInsight предоставляет экономически эффективный подход к управлению данными и их хранению с использованием хранилища BLOB-объектов Windows Azure. 

В этом учебнике вы будете заниматься подготовкой кластера HDInsight с помощью портала управления Windows Azure, отправкой задания Hadoop MapReduce с использованием PowerShell и импортом выходных данных задания MapReduce в Excel для изучения.

> [WACOM.NOTE] В этом учебнике описывается использование кластеров Hadoop 1.2 в HDInsight. Учебник по использованию кластеров Hadoop 2.2 в HDInsight (предварительный выпуск) см. в руководстве [Приступая к работе с кластерами Hadoop 2.2 в HDInsight (предварительный выпуск)][hdinsight-get-started-30].

Наряду с выпуском общедоступной версии Windows Azure HDInsight корпорация Майкрософт выпустила также эмулятор HDInsight для Windows Azure, ранее известный как Microsoft HDInsight Developer Preview. Этот продукт предназначен для сценариев разработки и поэтому поддерживает только развертывания на одном узле. Сведения об использовании эмулятора HDInsight см. в руководстве [Приступая к работе с эмулятором HDInsight][hdinsight-emulator].

**Предварительные требования:**

Перед началом работы с этим учебником необходимо иметь следующее:


- Подписка на Windows Azure. Дополнительные сведения о получении подписки см. в разделах [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].
- Компьютер под управлением Windows 8, Windows 7, Windows Server 2012 или Windows Server 2008 R2. Этот компьютер будет использоваться для отправки заданий MapReduce.
- Office 2013 профессиональный плюс, Office 365 профессиональный плюс, Excel 2013 автономный или Office 2010 профессиональный плюс.

**Предполагаемое время выполнения:** 30 минут

##В этом учебнике рассматриваются следующие темы

* [Настройка локальной среды для выполнения PowerShell](#setup)
* [Подготовка кластера HDInsight](#provision)
* [Запуск программы WordCount MapReduce](#sample)
* [Подключение к средствам бизнес-аналитики Microsoft](#powerquery)
* [Следующие шаги](#nextsteps)



##<a id="setup"></a> Настройка локальной среды для выполнения PowerShell

Существует несколько способов отправки заданий MapReduce в HDInsight. В этом учебнике будет использоваться Windows Azure PowerShell. Чтобы установить Windows Azure PowerShell, запустите [установщик веб-платформы Майкрософт][powershell-download]. В ответ на запрос щелкните **Запуск**, щелкните **Установить** и следуйте инструкциям. Дополнительные сведения см. в разделе [Установка и настройка Windows Azure PowerShell][powershell-install-configure].

Командлетам PowerShell требуются сведения о подписке для управления вашими службами.

**Чтобы подключиться к подписке с помощью Windows Azure AD, выполните следующие действия:**

1. Откройте консоль Windows Azure PowerShell, как описано в разделе [Практическое руководство. Установка Windows Azure PowerShell][powershell-open].
2. Выполните следующую команду:

		Add-AzureAccount

3. В окне введите адрес электронной почты и пароль, связанные с вашей учетной записью. Windows Azure выполняет проверку подлинности и сохраняет учетные данные, а затем закрывает это окно.

Другой способ подключения к подписке заключается в использовании метода сертификатов. Инструкции см. в разделе [Установка и настройка Windows Azure PowerShell][powershell-install-configure].
	
##<a name="provision"></a>Подготовка кластера HDInsight

Процесс подготовки HDInsight требует использования учетной записи хранения Windows Azure в качестве файловой системы по умолчанию. Учетная запись хранения должна находиться в том же центре обработки данных, что и вычислительные ресурсы HDInsight. В настоящее время можно подготовить только кластеры HDInsight в следующих центрах обработки данных:

- Юго-Восточная Азия
- Северная Европа
- Западная Европа
- Восток США
- Запад США

Необходимо выбрать один из этих пяти центров обработки данных для вашей учетной записи хранения Windows Azure.

**Чтобы создать учетную запись хранения Windows Azure, выполните следующие действия:**

1. Выполните вход на [портал управления Windows Azure][azure-management-portal].
2. Щелкните **СОЗДАТЬ** в левом нижнем углу, выберите **СЛУЖБЫ ДАННЫХ**, **ХРАНИЛИЩЕ**, а затем щелкните **БЫСТРО СОЗДАТЬ**.

	![HDI.StorageAccount.QuickCreate][image-hdi-storageaccount-quickcreate]

3. Введите сведения в поля **URL-АДРЕС**, **РАСПОЛОЖЕНИЕ** и **РЕПЛИКАЦИЯ**, а затем щелкните **СОЗДАТЬ УЧЕТНУЮ ЗАПИСЬ ХРАНЕНИЯ**. Территориальные группы не поддерживаются. Вы увидите новую учетную запись хранения в списке хранилищ. 
4. Дождитесь, пока **СОСТОЯНИЕ** новой учетной записи хранения не изменится на **В сети**.
5. Щелкните новую учетную запись хранения в списке, чтобы выбрать ее.
6. Щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ ДОСТУПА** в нижней части страницы.
7. Запишите **ИМЯ УЧЕТНОЙ ЗАПИСИ ХРАНЕНИЯ** и **ПЕРВИЧНЫЙ КЛЮЧ ДОСТУПА**.  Они потребуются позже в данном руководстве.


Подробные инструкции см. в разделах
[Как создать учетную запись хранения][azure-create-storageaccount] и [Использование хранилища BLOB-объектов Windows Azure с HDInsight][hdinsight-storage].




















**Чтобы подготовить кластер HDInsight, выполните следующие действия:** 

1. Выполните вход на [портал управления Windows Azure][azure-management-portal]. 

2. Щелкните **HDInsight** слева, чтобы просмотреть состояние кластеров в вашей учетной записи. На следующем снимке экрана кластер HDInsight отсутствует.

	![HDI.ClusterStatus][image-hdi-clusterstatus]

3. Щелкните **СОЗДАТЬ** в левой нижней части, выберите **Службы данных**, **HDInsight**, а затем щелкните **Быстро создать**.

	![HDI.QuickCreateCluster][image-hdi-quickcreatecluster]

4. Введите или выберите следующие значения:

	<table border="1">
	<tr><th>Имя</th><th>Значение</th></tr>
	<tr><td>Имя кластера</td><td>Имя этого кластера</td></tr>
	<tr><td>Размер кластера</td><td>Число узлов данных, которые требуется развернуть. Значение по умолчанию — 4. Но кластеры с 8, 16 и 32 узлами данных также доступны в раскрывающемся списке. При использовании варианта <strong>Настраиваемое создание</strong> можно указать любое число узлов данных. Доступны сведения о стоимости использования для различных размеров кластеров. Щелкните символ <strong>?</strong> над раскрывающимся списком и перейдите по ссылке во всплывающей подсказке.</td></tr>
	<tr><td>Пароль (администратор кластера)</td><td>Пароль для учетной записи <i>администратора</i>. При использовании варианта "Быстро создать" имя пользователя кластера по умолчанию указано как "admin". Это можно изменить только с помощью мастера <strong>Настраиваемое создание</strong>. Поле пароля должно содержать не менее 10 знаков, среди которых должны быть прописная буква, строчная буква, число и специальный знак.</td></tr>
	<tr><td>Учетная запись хранения</td><td>Выберите созданную вами учетную запись хранения в раскрывающемся списке. <br/>

	> [WACOM.NOTE]
        > После выбора учетной записи хранения изменить ее невозможно. Если удалить учетную запись хранения, кластер будет недоступен для использования.

	Расположение кластера HDInsight будет совпадать с расположением учетной записи хранения.
	</td></tr>
	</table>


5. Щелкните **Создать кластер HDInsight** в правом нижнем углу. После завершения процесса подготовки в столбце состояния будет отображаться **Выполняется**.

Сведения об использовании варианта **НАСТРАИВАЕМОЕ СОЗДАНИЕ** см. в разделе [Подготовка кластеров HDInsight][hdinsight-provision].













##<a name="sample"></a>Запуск задания WordCount MapReduce

Теперь у вас есть подготовленный кластер HDInsight. Следующим шагом является выполнение задания MapReduce для подсчета слов в текстовом файле. 

Для выполнения задания MapReduce требуются следующие элементы:

* Программа MapReduce. В этом учебнике будет использоваться пример WordCount, поставляемый с дистрибутивом кластера HDInsight, поэтому вам не придется писать собственное задание. Этот пример находится в файле */example/jars/hadoop-examples.jar*. Инструкции по написанию собственного задания MapReduce см. в разделе [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-MapReduce].

* Входной файл. В качестве входного файла будет использоваться */example/data/gutenberg/davinci.txt*. Сведения об отправке файлов см. в разделе [Отправка данных в HDInsight][hdinsight-upload-data].
* Папка для выходного файла. В качестве папки для выходного файла будет использоваться */example/data/WordCountOutput*. Система создаст эту папку, если она не существует.

Схема URI для доступа к файлам в хранилище BLOB-объектов:

	wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] По умолчанию контейнер BLOB-объектов, используемый для файловой системы по умолчанию, имеет имя, совпадающее с именем кластера HDInsight.

Эта схема URI предоставляет как незашифрованный доступ с префиксом *wasb:*, так и доступ с использованием SSL-шифрования с WASBS. Мы рекомендуем использовать wasbs везде, где это возможно, даже в случае доступа к данным, расположенным внутри того же центра обработки данных Windows Azure.

Поскольку HDInsight использует контейнер хранилища BLOB-объектов в качестве файловой системы по умолчанию, вы можете ссылаться на файлы и каталоги в файловой системе по умолчанию с помощью относительных или абсолютных путей.

Например, для доступа к hadoop-examples.jar можно использовать один из следующих вариантов:

	â—_ wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-examples.jar
	â—_ wasb:///example/jars/hadoop-examples.jar
	â—_ /example/jars/hadoop-examples.jar
				
Использование префикса *wasb: / /* в путях к этим файлам. Это необходимо для того, чтобы указать, что хранилище BLOB-объектов Azure используется для ввода и вывода файлов. Выходной каталог предполагает, что используемый по умолчанию путь задается относительно папки *wasb:///user/&lt;имя_пользователя&gt;*. 

Дополнительные сведения см. в разделе [Использование хранилища BLOB-объектов Windows Azure с HDInsight][hdinsight-storage].





















**Чтобы запустить пример WordCount, выполните следующие действия:**

1. Откройте **Windows Azure PowerShell**. Инструкции по открытию окна консоли Windows Azure PowerShell см. в разделе [Установка и настройка Windows Azure PowerShell][powershell-install-configure].

3. Выполните следующие команды, чтобы задать значения переменных.  
		
		$subscriptionName = "<SubscriptionName>" 
		$clusterName = "<HDInsightClusterName>"        
		
5. Выполните следующие команды, чтобы создать определение задания MapReduce:

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	Файл hadoop-examples.jar поставляется с дистрибутивом кластера HDInsight. Существует два аргумента для задания MapReduce. Первый из них — имя исходного файла, а второй — путь выходного файла. Исходный файл поставляется с дистрибутивом кластера HDInsight, а путь выходного файла будет создан во время выполнения.

6. Выполните следующую команду, чтобы отправить задание MapReduce:

		# Отправка задания
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 
		
	Помимо определения задания MapReduce необходимо также указать имя кластера HDInsight, где требуется выполнить это задание MapReduce. 

	*Start-AzureHDInsightJob* представляет собой асинхронный вызов.  Для проверки выполнения задания используйте командлет *Wait-AzureHDInsightJob*.

6. Выполните следующую команду для проверки выполнения задания MapReduce:

		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 
		
8. Выполните следующую команду для проверки любых ошибок при выполнении задания MapReduce:	
	
		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError
		
	На следующем снимке экрана показан результат успешного выполнения. В противном случае вы увидите сообщения об ошибках.

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]
































**Чтобы извлечь результаты задания MapReduce, выполните следующие действия:**

1. Откройте **Windows Azure PowerShell**.
2. Выполните следующие команды, чтобы создать папку C:\Tutorials и перейти в нее:

		mkdir \Tutorials
		cd \Tutorials
	
	Используемым по умолчанию каталогом Windows Azure Powershell является *C:\Windows\System32\WindowsPowerShell\v1.0*. По умолчанию у вас нет разрешения на запись для этой папки. Необходимо изменить каталог на папку, для которой вы имеете разрешение на запись.
	
2. Задайте три переменных в следующих командах, а затем выполните их:

		$subscriptionName = "<SubscriptionName>"       
		$storageAccountName = "<StorageAccountName>"   
		$containerName = "<ContainerName>"			   

	Учетная запись хранения Windows Azure — та, которую вы создали ранее в этом учебнике. Эта учетная запись хранения используется для размещения контейнера BLOB-объектов, используемого в качестве файловой системы по умолчанию кластера HDInsight.  Имя контейнера хранилища BLOB-объектов обычно совпадает с именем кластера HDInsight, если при подготовке кластера не указано другое имя.

3. Выполните следующие команды для создания объекта контекста хранилища Windows Azure:
		
		# Создание объекта контекста учетной записи хранения
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	*Select-AzureSubscription* используется для задания текущей подписки в случае, если имеется несколько подписок и должна использоваться подписка, отличная от подписки по умолчанию. 

4. Выполните следующую команду, чтобы загрузить результаты задания MapReduce из контейнера BLOB-объектов на рабочую станцию:

		# Загрузка результатов задания на рабочую станцию
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	Папка *example/data/WordCountOutput* — выходная папка, указанная при запуске задания MapReduce. *part-r-00000* — имя файла по умолчанию для выходных данных задания MapReduce.  Этот файл будет загружен в ту же структуру папок в локальной папке. Например, на следующем снимке экрана текущая папка — корневая папка C. Файл будет загружен в папку *C:\example\data\WordCountOutput &#92;*.

5. Выполните следующую команду, чтобы напечатать выходной файл задания MapReduce:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	Задание MapReduce создает файл с именем *part-r-00000*, содержащий слова и их количество.  В этом сценарии используется команда findstr для получения списка всех слов, содержащих *"there"*.


> [WACOM.NOTE] Если открыть файл <i>./example/data/WordCountOutput/part-r-00000</i> (многострочные выходные данные задания MapReduce) в блокноте, вы заметите, что разрывы строки отображаются неправильно. Это ожидалось.


	
##<a name="powerquery"></a>Подключение к средствам бизнес-аналитики Microsoft 

С помощью надстройки Power Query для Excel можно экспортировать выходные данные из HDInsight в Excel, где для их дальнейшей обработки и графического представления результатов могут использоваться средства бизнес-аналитики Microsoft (Business Intelligence — BI). При создании кластера HDInsight используемый по умолчанию контейнер с именем, совпадающим с именем кластера, был создан в учетной записи хранения, связанной с ним при создании. Он автоматически заполняется набором файлов. Один из этих файлов является примером таблицы Hive. В этом разделе будет показано, как импортировать данные, содержащиеся в этой таблице, в Microsoft Excel для просмотра и дополнительной обработки.

Для выполнения действий из этой части учебника требуется Excel 2010 или Excel 2013. Здесь мы будем импортировать используемую по умолчанию таблицу Hive, входящую в комплект HDInsight.

**Чтобы загрузить Microsoft PowerQuery для Excel, выполните следующие действия:**

- Загрузите Microsoft Power Query для Excel из [Центра загрузки Майкрософт](http://www.microsoft.com/ru-ru/download/details.aspx?id=39379) и установите.

**Чтобы импортировать данные HDInsight, выполните следующие действия:**

1. Откройте Excel и создайте новую пустую книгу.
3. Щелкните меню **Power Query**, выберите **Из других источников**, а затем щелкните **Из Windows Azure HDInsight**.

	![HDI.GettingStarted.PowerQuery.ImportData][image-hdi-gettingstarted-powerquery-importdata]

3. Введите **Имя учетной записи** учетной записи хранилища BLOB-объектов Azure, связанной с вашим кластером, и нажмите кнопку **ОК**. Это учетная запись хранения, созданная вами ранее в этом учебнике.
4. Введите **Ключ учетной записи** для учетной записи хранилища BLOB-объектов Azure, а затем нажмите кнопку **Сохранить**. 
5. В области навигатора в правой части окна дважды щелкните имя контейнера хранилища BLOB-объектов. По умолчанию имя контейнера совпадает с именем кластера. 

6. Найдите **part-r-00000** в столбце **Имя** (путь — *.../example/data/WordCountOutput*), а затем щелкните **Двоичный** слева от **part-r-00000**.

	![HDI.GettingStarted.PowerQuery.ImportData2][image-hdi-gettingstarted-powerquery-importdata2]

8. Щелкните правой кнопкой мыши **Столбец1.1**, а затем выберите **Переименовать**.
9. Измените имя на **Слово**.
10. Повторите этот процесс, чтобы переименовать **Столбец1.2** в **Количество**.

	![HDI.GettingStarted.PowerQuery.ImportData3][image-hdi-gettingstarted-powerquery-importdata3]

9. Щелкните **Применить и закрыть** в левом верхнем углу. Этот запрос импортирует таблицу Hive в Excel.


##<a name="nextsteps"></a>Дальнейшие действия
Из этого учебника вы узнали, как подготовить кластер с помощью HDInsight, запустить на нем задание MapReduce и импортировать результаты в Excel для дальнейшей обработки и отображения с использованием средств бизнес-аналитики. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Приступая к работе с кластерами Hadoop 2.2 с HDInsight (предварительный выпуск)][hdinsight-get-started-30]
- [Приступая к работе с эмулятором HDInsight][hdinsight-emulator]
- [Использование хранилища BLOB-объектов Windows Azure с HDInsight][hdinsight-storage]
- [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]
- [Отправка данных в HDInsight][hdinsight-upload-data]
- [Использование MapReduce с HDInsight][hdinsight-mapreduce]
- [Использование Hive с HDInsight][hdinsight-hive]
- [Использование Pig с HDInsight][hdinsight-pig]
- [Использование Oozie с HDInsight][hdinsight-oozie]
- [Разработка программ потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-streaming]
- [Разработка программ MapReduce на Java для HDInsight][hdinsight-develop-mapreduce]


[hdinsight-get-started-30]: /ru-ru/documentation/articles/hdinsight-get-started-30/
[hdinsight-provision]: /ru-ru/documentation/articles/hdinsight-provision-clusters/
[hdinsight-admin-powershell]: /ru-ru/documentation/articles/hdinsight-administer-use-powershell/
[hdinsight-upload-data]: /ru-ru/documentation/articles/hdinsight-upload-data/
[hdinsight-mapreduce]: /ru-ru/documentation/articles/hdinsight-use-mapreduce
[hdinsight-hive]: /ru-ru/documentation/articles/hdinsight-use-hive/
[hdinsight-pig]: /ru-ru/documentation/articles/hdinsight-use-pig/
[hdinsight-oozie]: /ru-ru/documentation/articles/hdinsight-use-oozie/
[hdinsight-storage]: /ru-ru/documentation/articles/hdinsight-use-blob-storage/
[hdinsight-emulator]: /ru-ru/documentation/articles/hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: /ru-ru/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: /ru-ru/documentation/articles/hdinsight-develop-deploy-java-mapreduce/

[azure-purchase-options]: https://www.windowsazure.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/ru-ru/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/ru-ru/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: /ru-ru/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: /ru-ru/documentation/articles/install-configure-powershell/
[powershell-open]: /ru-ru/documentation/articles/install-configure-powershell/#install

[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
[image-hdi-wordcountdiagram]: ./media/hdinsight-get-started/HDI.WordCountDiagram.gif
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-get-started/HDI.GettingStarted.MRJobOutput.png
[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-get-started/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
[image-hdi-gettingstarted-powerquery-importdata3]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData3.png

