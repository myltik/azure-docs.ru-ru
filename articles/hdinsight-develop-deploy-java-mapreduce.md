<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight" urlDisplayName="Учебники по HDInsight" pageTitle="Разработка программ MapReduce на Java для HDInsight | Azure" metaKeywords="hdinsight, разработка hdinsight, разработка hadoop, развертывание hdinsight, разработка, развертывание, учебник, MapReduce, Java" description="Узнайте, как разрабатывать Java-программы MapReduce в эмуляторе HDInsight и развертывать их в HDInsight." services="hdinsight" title="Разработка программ MapReduce на Java для HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="jgao" />

# Разработка программ MapReduce на Java для HDInsight
В этом учебнике рассматривается комплексный сценарий от разработки и тестирования задания MapReduce для счетчика слов в эмуляторе HDInsight до его развертывания и выполнения в Azure HDInsight.

**Предварительные требования:**

Перед началом работы с этим учебником необходимо иметь следующее:

- Установленный эмулятор Azure HDInsight. Инструкции см. в разделе [Приступая к работе с эмулятором HDInsight][hdinsight-emulator-get-started].
- Установленная среда Azure PowerShell на компьютере с эмулятором. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].
- Полученная подписка Azure. Инструкции см. в разделе [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].

##В этой статье

- [Разработка программы счетчика слов MapReduce на Java](#develop)
- [Тестирование программы в эмуляторе](#test)
- [Передача файлов данных и приложения в хранилище BLOB-объектов Azure](#upload)
- [Выполнение программы MapReduce в Azure HDInsight](#run)
- [Извлечение результатов MapReduce](#retrieve)
- [Следующие шаги](#nextsteps)

##<a name="develop"></a>Разработка программы счетчика слов MapReduce на Java

Счетчик слов — это простое приложение, которое подсчитывает вхождения каждого слова в заданном входном наборе данных. 

**Создание задания MapReduce для счетчика слов на Java**

1. Откройте Блокнот.
2. Скопируйте следующую программу и вставьте ее в Блокнот.

		package org.apache.hadoop.examples;
		
		import java.io.IOException;
		import java.util.StringTokenizer;
		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.fs.Path;
		import org.apache.hadoop.io.IntWritable;
		import org.apache.hadoop.io.Text;
		import org.apache.hadoop.mapreduce.Job;
		import org.apache.hadoop.mapreduce.Mapper;
		import org.apache.hadoop.mapreduce.Reducer;
		import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
		import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
		import org.apache.hadoop.util.GenericOptionsParser;
		
		public class WordCount {
		
		  public static class TokenizerMapper 
		       extends Mapper<Object, Text, Text, IntWritable>{
		    
		    private final static IntWritable one = new IntWritable(1);
		    private Text word = new Text();
		      
		    public void map(Object key, Text value, Context context
		                    ) throws IOException, InterruptedException {
		      StringTokenizer itr = new StringTokenizer(value.toString());
		      while (itr.hasMoreTokens()) {
		        word.set(itr.nextToken());
		        context.write(word, one);
		      }
		    }
		  }
		  
		  public static class IntSumReducer 
		       extends Reducer<Text,IntWritable,Text,IntWritable> {
		    private IntWritable result = new IntWritable();
		
		    public void reduce(Text key, Iterable<IntWritable> values, 
		                       Context context
		                       ) throws IOException, InterruptedException {
		      int sum = 0;
		      for (IntWritable val : values) {
		        sum += val.get();
		      }
		      result.set(sum);
		      context.write(key, result);
		    }
		  }
		
		  public static void main(String[] args) throws Exception {
		    Configuration conf = new Configuration();
		    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
		    if (otherArgs.length != 2) {
		      System.err.println("Usage: wordcount <in> <out>");
		      System.exit(2);
		    }
		    Job job = new Job(conf, "word count");
		    job.setJarByClass(WordCount.class);
		    job.setMapperClass(TokenizerMapper.class);
		    job.setCombinerClass(IntSumReducer.class);
		    job.setReducerClass(IntSumReducer.class);
		    job.setOutputKeyClass(Text.class);
		    job.setOutputValueClass(IntWritable.class);
		    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
		    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
		    System.exit(job.waitForCompletion(true) ? 0 : 1);
		  }
		}

	Обратите внимание, что пакет имеет имя **org.apache.hadoop.examples**, а класс — имя **WordCount**. Эти имена будут использоваться при отправке задания MapReduce.
	
3. Сохраните файл как **c:\Tutorials\WordCountJava\WordCount.java**. Если структуры папок не существует, создайте ее.

Эмулятор HDInsight поставляется вместе с компилятором *javac*.

**Компиляция программы MapReduce**

1. Откройте окно командной строки.
2. Перейдите в каталог **c:\Tutorials\WordCountJava**.  Это папка для программы счетчика слов MapReduce.
3. Выполните следующую команду, чтобы проверить наличие двух JAR-файлов:

		dir %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar
		dir %hadoop_home%\lib\commons-cli-1.2.jar

4. Выполните следующую команду, чтобы скомпилировать программу:

		C:\Hadoop\java\bin\javac -classpath %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar;%hadoop_home%\lib\commons-cli-1.2.jar WordCount.java

	javac находится в папке C:\Hadoop\java\bin. Последний параметр является java-программой, которая находится в текущей папке. Компилятор создает 3 файла классов в текущей папке.

5. Выполните следующую команду, чтобы создать JAR-файл:

		C:\Hadoop\java\bin\jar -cvf WordCount.jar *.class

	Команда создает файл WordCount.jar в текущей папке.

	![HDI.EMulator.WordCount.Compile][image-emulator-wordcount-compile]
































##<a name="test"></a>Тестирование программы в эмуляторе

Тестирование задания MapReduce в эмуляторе включает следующие процедуры:

1. Передача файлов данных в HDFS эмулятора
3. Отправка задания MapReduce для счетчика слов
4. Проверка состояния задания
5. Извлечение результатов задания

В эмуляторе HDInsight используется HDFS в качестве файловой системы по умолчанию.  При желании вы можете настроить эмулятор HDInsight на использование хранилища BLOB-объектов Azure. Подробные сведения см. в разделе [Приступая к работе с эмулятором HDInsight][hdinsight-emulator-wasb]. 

В этом учебнике для передачи файлов данных в HDFS будет использоваться команда HDFS *copyFromLocal*. В следующем разделе показано, как с помощью Azure PowerShell передавать файлы в хранилище BLOB-объектов Azure. Сведения о других способах передачи файлов в хранилище BLOB-объектов Azure см. в разделе [Передача данных в HDInsight][hdinsight-upload-data].

Для работы с этим учебником используется следующая структура папок HDFS:

<table border="1">
<tr><td>Папка</td><td>Примечание</td></tr>
<tr><td>/WordCount</td><td>Корневая папка для проекта счетчика слов. </td></tr>
<tr><td>/WordCount/Apps</td><td>Папка исполняемых файлов программ mapper и reducer.</td></tr>
<tr><td>/WordCount/Input</td><td>Папка исходных файлов MapReduce.</td></tr>
<tr><td>/WordCount/Output</td><td>Папка выходных файлов MapReduce.</td></tr>
<tr><td>/WordCount/MRStatusOutput</td><td>Выходная папка заданий.</td></tr>
</table>

В этом учебнике в качестве файлов данных используются TXT-файлы, расположенные в каталоге %hadoop_home%.

> [WACOM.NOTE] В командах Hadoop HDFS учитывается регистр.

**Копирование файлов данных в HDFS эмулятора**

0. Откройте командную строку Hadoop на вашем рабочем столе. Командную строку Hadoop устанавливает программа установки эмулятора.
1. В окне командной строки Hadoop выполните следующую команду, чтобы создать каталог для входных файлов:

		hadoop fs -mkdir /WordCount/Input

	Здесь используется относительный путь. Эта запись аналогична следующей:

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2. Выполните следующую команду, чтобы скопировать некоторые текстовые файлы во входную папку в HDFS:

		hadoop fs -copyFromLocal %hadoop_home%\*.txt /WordCount/Input

	Задание MapReduce будет подсчитывать слова в этих файлах.

3. Выполните следующую команду, чтобы получить список переданных файлов и проверить его:

		hadoop fs -ls /WordCount/Input

	Должны появиться восемь TXT-файлов.


**Запуск задания MapReduce из командной строки Hadoop**

1. Откройте командную строку Hadoop на вашем рабочем столе.
2. Выполните следующую команду, чтобы удалить структуру папок /WordCount/Output из HDFS.  /WordCount/Output — выходная папка задания MapReduce для счетчика слов. Если папка уже есть, задание MapReduce завершится ошибкой. Этот шаг необходим, если вы запускаете задание во второй раз.

		hadoop fs -rmr /WordCount/Output

2. Выполните следующую команду:

		hadoop jar c:\Tutorials\WordCountJava\wordcount\target\wordcount-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

	При успешном завершении задания вы должны получить выходные данные следующего вида:

	![HDI.EMulator.WordCount.Run][image-emulator-wordcount-run]

	На снимке экрана видно, что процедуры map и reduce завершены на 100 %. Здесь также указан идентификатор задания: job_201312092021_0002. Для получения того же отчета можно открыть ярлык **Состояние Hadoop MapReduce** на вашем рабочем столе и найти идентификатор задания.

Для запуска задания MapReduce можно также использовать Azure PowerShell. Инструкции см. в разделе [Приступая к работе с эмулятором HDInsight][hdinsight-emulator-get-started].

**Отображение выходных данных из HDFS**

1. Откройте командную строку Hadoop.
2. Выполните следующие команды, чтобы отобразить выходные данные:

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-00000

	Чтобы получить страничное представление, добавьте в конце команды "|more". Можно также найти строковый шаблон с помощью команды findstr.

		hadoop fs -cat /WordCount/Output/part-00000 | findstr "there"

На данный момент вы разработали задание MapReduce для счетчика слов и успешно его протестировали в эмуляторе.  Следующим действием будет развертывание и выполнение задания в Azure HDInsight.






































##<a id="upload"></a>Передача данных в хранилище BLOB-объектов Azure
Azure HDInsight использует хранилище BLOB-объектов Azure для хранения данных. При подготовке кластера HDInsight контейнер хранилища BLOB-объектов Azure используется для хранения системных файлов. Для хранения файлов данных можно использовать этот контейнер по умолчанию или другой контейнер (в той же учетной записи хранения Azure или в другой учетной записи хранения в том же центре обработки данных, где находится кластер). 

В этом учебнике вы создадите контейнер для файлов данных и приложения MapReduce в отдельной учетной записи хранения. В качестве файлов данных используются текстовые файлы в каталоге %hadoop_home% на рабочей станции.

**Создание хранилища BLOB-объектов и контейнера**

1. Откройте Azure PowerShell.
2. Задайте переменные, а затем выполните команды:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

	**$subscripionName** относится к вашей подписке Azure. Необходимо указать имя для **$storageAccountName_Data** и **$containerName_Data**. Ограничения именования см. в разделе [Присвоение имен и ссылки на контейнеры, BLOB-объекты и метаданные](http://msdn.microsoft.com/ru-ru/library/windowsazure/dd135715.aspx). 

3. Выполните следующие команды, чтобы создать учетную запись хранения и контейнер хранилища BLOB-объектов в учетной записи:

		# Select Azure subscription
		Select-AzureSubscription $subscriptionName
		
		# Create a storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location
				
		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$destContext = New-AzureStorageContext â€“StorageAccountName $storageAccountName_Data â€“StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4. Выполните следующие команды, чтобы проверить учетную запись хранения и контейнер:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
		Get-AzureStorageContainer -Context $destContext

**Передача файлов данных**

1. Откройте Azure PowerShell.
2. Задайте первые три переменные, а затем выполните команды:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
		$destFolder = "WordCount/Input"

	**$storageAccountName_Data** и **$containerName_Data** — те же, что вы определили в последней процедуре.

	Заметьте, что исходной является папка **c:\Hadoop\hadoop-1.1.0-SNAPSHOT**, а целевой — папка **WordCount/Input**.

3. Выполните следующие команды, чтобы получить список TXT-файлов в исходной папке:

		# Get a list of the txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}
		
4. Выполните следующие команды, чтобы создать объект контекста хранилища:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Выполните следующие команды, чтобы скопировать файлы:

		# Copy the file from local workstation to the Blob container        
		foreach ($file in $filesTxt){
		 
		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"
		
		    write-host "Copying $fileName to $blobName"
		
		    Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
		}

6. Выполните следующую команду, чтобы получить список переданных файлов:

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

	Должны появиться сведения о 8 текстовых файлах данных.

**Передача приложения счетчика слов**

1. Откройте Azure PowerShell.
2. Задайте первые три переменные, а затем выполните команды:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$jarFile = "C:\Tutorials\WordCountJava\WordCount.jar"
		$blobFolder = "WordCount/jars"

	**$storageAccountName_Data** и **$containerName_Data** — те же, что вы определили в последней процедуре, то есть файл данных и приложение будут переданы в тот же контейнер в той же учетной записи хранения.

	Заметьте, целевой является папка **WordCount/jars**.

4. Выполните следующие команды, чтобы создать объект контекста хранилища:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Выполните следующие команды, чтобы скопировать приложения:

		Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

6. Выполните следующую команду, чтобы получить список переданных файлов:

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

	Должен появиться указанный JAR-файл.

##<a name="run"></a>Выполнение задания MapReduce в Azure HDInsight

Приведенный здесь сценарий PowerShell выполняет следующие задачи:

1. Подготовка кластера HDInsight
	
	1. Создание учетной записи хранения, которая будет использоваться в качестве файловой системы по умолчанию для кластера HDInsight
	2. Создание контейнера хранилища BLOB-объектов 
	3. Создание кластера HDInsight

2. Отправка задания MapReduce

	1. Создание определения задания MapReduce
	2. Отправка задания MapReduce
	3. Ожидание завершения задания
	4. Отображение стандартной ошибки
	5. Отображение стандартного вывода

3. Удаление кластера

	1. Удаление кластера HDInsight
	2. Удаление учетной записи хранения, которая использовалась в качестве файловой системы по умолчанию для кластера HDInsight


**Запуск сценария PowerShell**

1. Откройте Блокнот.
2. Скопируйте следующий код и вставьте в Блокнот:
		
		# The storage account and the HDInsight cluster variables
		$subscriptionName = "<WindowsAzureSubscriptionName>"
		$serviceNameToken = "<ServiceNameTokenString>"
		$location = "<MicrosoftDataCenter>"     ### must match the data storage account location
		$clusterNodes = <NumberOFNodesInTheCluster>

		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		
		$clusterName = $serviceNameToken + "hdicluster"
		
		$storageAccountName_Default = $serviceNameToken + "hdistore"
		$containerName_Default =  $serviceNameToken + "hdicluster"

		# The MapReduce job variables
		$jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
		$className = "org.apache.hadoop.examples.WordCount"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
		
		# Create a PSCredential object. The username and password are hardcoded here.  You can change them if you want.
		$password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 
		
		Select-AzureSubscription $subscriptionName
		
		#=============================
		# Create a storage account used as the default file system
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
		
		#=============================
		# Create a Blob storage container used as teh default file system
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default
		
		New-AzureStorageContainer -Name $containerName_Default -Context $destContext
		
		#=============================
		# Create an HDInsight cluster
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
		
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config
		
		#=============================
		# Create a MapReduce job definition
		Write-Host "Create a MapReduce job definition" -ForegroundColor Green
		$mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus
		
		#=============================
		# Run the MapReduce job
		Write-Host "Run the MapReduce job" -ForegroundColor Green
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
		
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
				
		#=============================
		# Delete the HDInsight cluster
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Remove-AzureHDInsightCluster -Name $clusterName  
		
		# Delete the default file system storage account
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Задайте первые шесть переменных в сценарии. **$serviceNameToken** будет использоваться для имени кластера HDInsight, имени учетной записи хранения по умолчанию и имени контейнера хранилища BLOB-объектов по умолчанию.  Поскольку имя службы должно содержать от 3 до 24 символов, а сценарий добавляет к именам строку длиной до 10 символов, необходимо ограничить длину строки до 14 символов. Кроме того, для $serviceNameToken необходимо использовать нижний регистр. **$storageAccountName_Data** и **$containerName_Data** — это учетная запись хранения и контейнер, которые используются для хранения файлов данных и приложения. Значение **$location** должно соответствовать расположению учетной записи хранения данных.
4. Просмотрите остальные переменные.
5. Сохраните файл сценария.
6. Откройте Azure PowerShell.
7. Выполните следующую команду, чтобы задать политику выполнения remotesigned:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. При появлении запроса введите имя пользователя и пароль для кластера HDInsight. Поскольку в конце сценария кластер будет удален, а имя пользователя и пароль больше не потребуются, в качестве имени пользователя и пароля можно использовать любые строки. Если вы не хотите, чтобы появлялся запрос на ввод учетных данных, см. раздел [Работа с паролями, защищенными строками и учетными данными в Windows PowerShell][powershell-PSCredential]


##<a name="retrieve"></a>Извлечение выходных данных задания MapReduce
В этом разделе показано, как загружать и отображать выходные данные.  Сведения об отображении результатов в Excel см. в разделах [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][hdinsight-excel] и [Подключение Excel к HDInsight с помощью Power Query][hdinsight-powerquery].


**Извлечение результатов**

1. Откройте окно Azure PowerShell.
2. Перейдите в каталог **C:\Tutorials\WordCountJava**. Папка Azure PowerShell по умолчанию: **C:\Windows\System32\WindowsPowerShell\v1.0**. Выполняемые командлеты загрузят выходной файл в текущую папку.  У вас нет разрешений для загрузки файлов в системные папки.
2. Выполните следующие команды, чтобы задать значения:

		$subscriptionName = "<WindowsAzureSubscriptionName>"
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-r-00000"
	
3. Выполните следующие команды, чтобы создать объект контекста хранилища Azure: 
		
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

4. Выполните следующие команды, чтобы загрузить и отобразить выходные данные:

		Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

После завершения задания можно экспортировать данные в базу данных SQL Server или Azure SQL с помощью [Sqoop][hdinsight-sqoop] либо экспортировать данные в Excel.  

##<a id="nextsteps"></a>Дальнейшие действия
В этом учебнике вы узнали, как разработать задание MapReduce на Java, протестировать приложение в эмуляторе HDInsight, написать сценарий PowerShell для подготовки кластера HDInsight и выполнения MapReduce в кластере. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Разработка программ MapReduce потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-streaming]
- [Приступая к работе с Azure HDInsight](/ru-ru/manage/services/hdinsight/get-started-hdinsight/)
- [Приступая к работе с эмулятором HDInsight][hdinsight-emulator]
- [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]
- [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]
- [Отправка данных в HDInsight][hdinsight-upload-data]
- [Использование Hive с HDInsight][hdinsight-hive]
- [Использование Pig с HDInsight][hdinsight-pig]
- [Подключение Excel к HDInsight с помощью Power Query][hdinsight-power-query]
- [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][hdinsight-hive-odbc]

[azure-purchase-options]: https://www.windowsazure.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/ru-ru/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/ru-ru/pricing/free-trial/

[hdinsight-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-power-query]: /ru-ru/documentation/articles/hdinsight-connect-excel-power-query/

[hdinsight-develop-streaming]: /ru-ru/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/


[hdinsight-emulator-get-started]: /ru-ru/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/
[hdinsight-emulator-wasb]: /ru-ru/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/#blobstorage
[hdinsight-upload-data]: /ru-ru/manage/services/hdinsight/howto-upload-data-to-hdinsight/
[hdinsight-emulator]: /ru-ru/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/
[hdinsight-storage]: /ru-ru/manage/services/hdinsight/howto-blob-store/
[hdinsight-admin-powershell]: /ru-ru/manage/services/hdinsight/administer-hdinsight-using-powershell/
[hdinsight-hive]:/ru-ru/manage/services/hdinsight/using-hive-with-hdinsight/
[hdinsight-pig]: /ru-ru/manage/services/hdinsight/using-pig-with-hdinsight/
[hdinsight-excel]: /ru-ru/manage/services/hdinsight/connect-excel-with-hive-ODBC/
[hdinsight-powerquery]:/ru-ru/manage/services/hdinsight/connect-excel-with-power-query/

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[Powershell-install-configure]: /ru-ru/documentation/articles/install-configure-powershell/



[image-emulator-wordcount-compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
[image-emulator-wordcount-run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png


