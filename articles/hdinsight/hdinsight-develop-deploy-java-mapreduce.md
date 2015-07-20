<properties
	pageTitle="Разработка программ MapReduce на Java для Hadoop | Microsoft Azure"
	description="Познакомьтесь с разработкой программ MapReduce на Java с помощью эмулятора HDInsight, а также с процессом их развертывания на HDInsight."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="nitinme"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="04/01/2015"
	ms.author="nitinme"/>

# Разработка программ MapReduce на Java для Hadoop в HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-maven-mapreduce-selector.md)]

В данном учебнике приводится полный цикл разработки задания для Hadoop MapReduce по подсчету количества слов на языке Java с помощью Apache Maven. Здесь же демонстрируется, как тестировать приложения на эмуляторе HDInsight для Azure, а затем развертывать их и запускать в кластере HDInsight под управлением Windows.

##<a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим учебником необходимо выполнить следующие действия:

- Установка эмулятора HDInsight Инструкции см. в разделе [Приступая к работе с эмулятором HDInsight][hdinsight-emulator]. Убедитесь, что все необходимые службы запущены. На компьютере с установленным эмулятором HDInsight запустите командную строку Hadoop из ярлыка на рабочем столе, перейдите в каталог **C:\\hdp** и выполните команду **start_local_hdp_services.cmd**.
- Установите среду Azure PowerShell на компьютере с эмулятором. Инструкции см. в разделе [Установка и настройка Azure PowerShell][powershell-install-configure].
- Установите пакет JDK 7 или выше для платформы Java на компьютере с эмулятором. Он уже есть на этом компьютере.
- Установите и настройте [Apache Maven](http://maven.apache.org/).
- Получите подписку Azure. Инструкции см. в разделе [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатное пробное использование][azure-free-trial].


##<a name="develop"></a>Использование Apache Maven для создания программы MapReduce на языке Java

Создание приложения MapReduce по подсчету количества слов. Это простое приложение, которое подсчитывает количество повторений каждого слова в заданном наборе входных данных. В этом разделе мы выполним следующие задачи:

1. Создание проекта с помощью Apache Maven
2. Обновление модели объекта проекта (POM)
3. Создание приложения MapReduce по подсчету количества слов.
4. Сборка и создание пакета приложения

**Создание проекта с помощью Maven**

1. Создайте каталог **C:\\Tutorials\\WordCountJava**.2. В командной строке в вашей среде разработки перейдите во вновь созданный каталог.
3. Используйте команду __mvn__, которая будет установлена вместе с Maven, для создания шаблона проекта.

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	При этом в текущем каталоге будет создан новый каталог с именем, указанным в параметре __artifactID__ (в данном примере это **wordcountjava**). В этом каталоге будут находиться следующие элементы.

	* __pom.xml__ — это [модель объекта проекта (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html), которая содержит информацию и подробности конфигурации, использующиеся при сборке проекта;

	* __src__ — каталог, содержащий каталог __main\\java\\org\\apache\\hadoop\\examples__, в котором будет разрабатываться приложение.
3. Удалите файл __src\\test\\java\\org\\apache\\hadoop\\examples\\apptest.java__, так как он не используется в этом примере.

**Обновление POM**

1. Отредактируйте файл __pom.xml__ и добавьте в раздел `<dependencies>` следующее:

		<dependency>
		  <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
        </dependency>
    	<dependency>
      	  <groupId>org.apache.hadoop</groupId>
      	  <artifactId>hadoop-mapreduce-client-common</artifactId>
      	  <version>2.5.1</version>
    	</dependency>
    	<dependency>
      	  <groupId>org.apache.hadoop</groupId>
      	  <artifactId>hadoop-common</artifactId>
      	  <version>2.5.1</version>
    	</dependency>

	Это указывает Maven, что для проекта требуются библиотеки (перечисленные в параметре <artifactId>) определенной версии (указанной в параметре <version>). При компиляции он будет загружено из репозитория Maven по умолчанию. Можно воспользоваться [поиском по репозиторию Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar), чтобы получить дополнительную информацию.

2. Добавьте в файл __pom.xml__ следующее: Эти строки должны находиться в файле внутри тегов `<project>...</project>`; например между `</dependencies>` и `</project>`.

		<build>
  		  <plugins>
    		<plugin>
      		  <groupId>org.apache.maven.plugins</groupId>
      		  <artifactId>maven-shade-plugin</artifactId>
      		  <version>2.3</version>
      		  <configuration>
        		<transformers>
          		  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		          </transformer>
        		</transformers>
      		  </configuration>
      		  <executions>
        		<execution>
          		  <phase>package</phase>
          			<goals>
            		  <goal>shade</goal>
          			</goals>
        	    </execution>
      		  </executions>
      	    </plugin>
  		  </plugins>
	    </build>

	Это также настраивает [подключаемый модуль Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/), который используется для предотвращения дублирования лицензии в файле JAR, собранном Maven. Причина — дублирующиеся файлы лицензий вызывают ошибку выполнения на кластере HDInsight. Использование подключаемого модуля Maven Shade с реализацией `ApacheLicenseResourceTransformer` предотвращает возникновение этой ошибки.

	Подключаемый модуль Maven Shade также создает uberjar, который содержит все зависимости, требуемые приложением.

3. Сохраните файл __pom.xml__.

**Создание приложения по подсчету слов**

1. Перейдите в каталог __wordcountjava\\src\\main\\java\\org\\apache\\hadoop\\examples__ и переименуйте файл __app.java__ в __WordCount.java__.
2. Откройте Блокнот.
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

3. Сохраните файл.

**Сборка и создание пакета приложения**

1. Откройте командную строку и перейдите в каталог __wordcountjava__.

2. Выполните следующую команду, чтобы собрать файл JAR, содержащий приложение.

		mvn clean package

	При этом будут удалены остатки предыдущих сборок, загружены все неустановленные на текущий момент зависимости, затем будет произведена сборка и создание пакета приложения.

3. После завершения выполнения команды в каталоге __wordcountjava\\target__ будет находиться файл с именем __wordcountjava-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE]Файл __wordcountjava-1.0-SNAPSHOT.jar__ является uberjar.


##<a name="test"></a>Тестирование программы в эмуляторе

Тестирование задания MapReduce в эмуляторе HDInsight включает следующие процедуры:

1. Передача файлов данных в распределенную файловую систему Hadoop (HDFS) в эмуляторе
2. Создание локальной группы пользователей
3. Выполнение задания MapReduce по подсчету количества слов
4. Извлеките результаты задания.

По умолчанию в качестве файловой системы эмулятор HDInsight использует HDFS. При желании вы можете настроить эмулятор HDInsight на использование хранилища BLOB-объектов Azure. Подробные сведения см. в разделе [Приступая к работе с эмулятором HDInsight][hdinsight-emulator-wasb].

В этом учебнике для передачи файлов данных в HDFS будет использоваться команда HDFS **copyFromLocal**. В следующем разделе показано, как отправлять файлы в хранилище больших двоичных объектов Azure с помощью Azure PowerShell. Сведения о других способах передачи файлов в хранилище BLOB-объектов Azure см. в разделе [Передача данных в HDInsight][hdinsight-upload-data].

Для работы с этим учебником используется следующая структура папок HDFS:

<table border="1">
<tr><td>Папка</td><td>Примечание.</td></tr>
<tr><td>/WordCount</td><td>Корневая папка для проекта счетчика слов. </td></tr>
<tr><td>/WordCount/Apps</td><td>Папка исполняемых файлов программ mapper и reducer.</td></tr>
<tr><td>/WordCount/Input</td><td>Папка исходных файлов MapReduce.</td></tr>
<tr><td>/WordCount/Output</td><td>Папка выходных файлов MapReduce.</td></tr>
<tr><td>/WordCount/MRStatusOutput</td><td>Выходная папка заданий.</td></tr>
</table>

В этом учебнике в качестве файлов данных используются текстовые файлы .txt, расположенные в каталоге %hadoop_home%.

> [AZURE.NOTE]В командах Hadoop HDFS учитывается регистр.

**Копирование файлов данных в эмулятор HDFS**

1. Откройте командную строку Hadoop на вашем рабочем столе. Командная строка Hadoop устанавливается программой установки эмулятора.
2. В окне командной строки Hadoop выполните следующую команду, чтобы создать каталог для входных файлов:

		hadoop fs -mkdir /WordCount
		hadoop fs -mkdir /WordCount/Input

	Здесь используется относительный путь. Эта запись аналогична следующей:

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3. Выполните следующую команду, чтобы скопировать некоторые текстовые файлы во входную папку в HDFS:

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /WordCount/Input

	Задание MapReduce будет подсчитывать слова в этих файлах.

4. Выполните следующую команду, чтобы получить список переданных файлов и проверить его:

		hadoop fs -ls /WordCount/Input

**Создание локальной группы пользователей**

Для успешного выполнения задания MapReduce в кластере необходимо создать группу пользователей с именем hdfs. К этой группе также следует добавить пользователя hadoop, а также локального пользователя, под именем которого вы будете входить в эмулятор. Выполните следующие команды в окне команд с повышенными привилегиями:

		# Add a user group called hdfs
		net localgroup hdfs /add

		# Adds a user called hadoop to the group
		net localgroup hdfs hadoop /add

		# Adds the local user to the group
		net localgroup hdfs <username> /add

**Запуск задания MapReduce с помощью командной строки Hadoop**

1. Откройте командную строку Hadoop на вашем рабочем столе.
2. Выполните следующую команду, чтобы удалить структуру папок /WordCount/Output из HDFS. /WordCount/Output — это выходная папка задания MapReduce по подсчету слов. Если папка уже есть, задание MapReduce завершится ошибкой. Этот шаг необходим, если вы запускаете задание во второй раз.

		hadoop fs -rm - r /WordCount/Output

2. Выполните следующую команду:

		hadoop jar C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

	При успешном завершении задания вы должны получить выходные данные следующего вида:

	![HDI.EMulator.WordCount.Run][image-emulator-wordcount-run]

	На снимке экрана видно, что процедуры map и reduce завершены на 100 %. Здесь также указывается идентификатор задания. Для получения того же отчета можно открыть ярлык **Состояние Hadoop MapReduce** на вашем рабочем столе и найти идентификатор задания.

Для запуска задания MapReduce можно также использовать Azure PowerShell. Инструкции см. в разделе [Приступая к работе с эмулятором HDInsight][hdinsight-emulator].

**Отображение выходных данных из HDFS**

1. Откройте командную строку Hadoop.
2. Выполните следующие команды, чтобы отобразить выходные данные:

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-r-00000

	Чтобы получить страничное представление, добавьте в конце команды "|more". Можно также найти строковый шаблон с помощью команды **findstr**.

		hadoop fs -cat /WordCount/Output/part-r-00000 | findstr "there"

Теперь вы разработали задание MapReduce по подсчету слов и успешно протестировали его в эмуляторе. Следующим действием будет развертывание и выполнение задания в Azure HDInsight.



##<a id="upload"></a>Отправка данных и приложения в хранилище больших двоичных объектов Azure
Azure HDInsight использует для хранения данных хранилище BLOB-объектов Azure. При подготовке кластера HDInsight контейнер хранилища BLOB-объектов Azure используется для хранения системных файлов. Для хранения файлов данных можно использовать этот контейнер по умолчанию или другой контейнер (в той же учетной записи хранения Azure или в другой учетной записи хранения в том же центре обработки данных, где находится кластер).

В этом учебнике вы создадите контейнер для файлов данных и приложения MapReduce в отдельной учетной записи хранения. Файлы данных представляют собой текстовые файлы, которые находятся в каталоге **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common** на рабочей станции с эмулятором.

**Создание учетной записи хранения и контейнера больших двоичных объектов**

1. Откройте Azure PowerShell.
2. Задайте переменные, а затем выполните команды:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

	Переменная **$subscripionName** связана с вашей подпиской Azure. Необходимо указать имя **$storageAccountName_Data** и **$containerName_Data**. Ограничения именования см. в разделе [Присвоение имен и ссылки на контейнеры, BLOB-объекты и метаданные](http://msdn.microsoft.com/library/windowsazure/dd135715.aspx).

3. Выполните следующие команды, чтобы создать учетную запись хранения и контейнер хранилища больших двоичных объектов в этой учетной записи:

		# Select an Azure subscription
		Select-AzureSubscription $subscriptionName

		# Create a Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location

		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4. Выполните следующие команды, чтобы проверить учетную запись хранения и контейнер:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
		Get-AzureStorageContainer -Context $destContext

**Отправка файлов данных**

1. Откройте Azure PowerShell.
2. Задайте первые три переменные, а затем выполните команды:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	Переменные **$storageAccountName_Data** и **$containerName_Data** — те же, что были определены в последней процедуре.

	Заметьте, что исходной является папка **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT**, а целевой — папка **WordCount/Input**.

3. Чтобы получить список TXT-файлов в исходной папке, выполните следующие команды:

		# Get a list of the .txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4. Выполните следующие команды, чтобы создать объект контекста хранилища:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Выполните следующие команды, чтобы скопировать файлы:

		# Copy the files from the local workstation to the Blob container
		foreach ($file in $filesTxt){

		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"

		    write-host "Copying $fileName to $blobName"

		    Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
		}

6. Выполните следующие команды, чтобы получить список отправленных файлов:

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

	Должны появиться сведения о загруженных текстовых файлах данных.

**Отправка приложения для подсчета слов**

1. Откройте Azure PowerShell.
2. Задайте первые три переменные, а затем выполните команды:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$jarFile = "C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar"
		$blobFolder = "WordCount/jars"

	Значения переменных **$storageAccountName\\_Data** и **$containerName\\_Data** — те же, что вы определили в последней процедуре, то есть файл данных и приложение будут переданы в тот же контейнер в той же учетной записи хранения.

	Заметьте, целевой является папка **WordCount/jars**.

4. Выполните следующие команды, чтобы создать объект контекста хранилища:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Выполните следующую команду, чтобы скопировать приложения:

		Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

6. Выполните следующие команды, чтобы получить список отправленных файлов:

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

	Должен появиться указанный JAR-файл.

##<a name="run"></a>Выполнение задания MapReduce в Azure HDInsight

В этом разделе вы будете создавать скрипты Azure PowerShell, предназначенные для выполнения следующих задач:

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
	2. Удаление учетной записи хранения, которая использовалась в качестве файловой системы по умолчанию для кластера HDInsight.


**Запуск скрипта Azure PowerShell**

1. Откройте Блокнот.
2. Скопируйте следующий код и вставьте в Блокнот:

		# The Storage account and the HDInsight cluster variables
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"
		$location = "<MicrosoftDataCenter>"     ### Must match the data Storage account location
		$clusterNodes = <NumberOFNodesInTheCluster>

		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"

		$clusterName = $stringPrefix + "hdicluster"

		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# The MapReduce job variables
		$jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
		$className = "org.apache.hadoop.examples.WordCount"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

		# Create a PSCredential object. The user name and password are hardcoded here. You can change them if you want.
		$password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ("Admin", $password)

		Select-AzureSubscription $subscriptionName

		#=============================
		# Create a Storage account used as the default file system
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

		#=============================
		# Create a Blob storage container used as the default file system
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

		# Delete the default file system Storage account
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Задайте первые шесть переменных скрипта: Переменная **$stringPrefix** используется для добавления префикса к имени кластера HDInsight, имени учетной записи хранения и имени контейнера хранилища больших двоичных объектов. Так как количество символов в этих именах должно быть больше 3 и меньше 24, следите за тем, чтобы общее количество символов в строке и именах, используемых сценарием, не превышало это ограничение. Для **$stringPrefix** необходимо использовать только строчные буквы.

	Переменные **$storageAccountName_Data** и **$containerName_Data** — это учетная запись хранения и контейнер, которые используются для хранения файлов данных и приложения. Переменная **$location** должна соответствовать расположению учетной записи хранения данных.

4. Просмотрите остальные переменные.
5. Сохраните файл скрипта.
6. Откройте Azure PowerShell.
7. Выполните следующую команду, чтобы задать политику выполнения RemoteSigned:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. При появлении запроса введите имя пользователя и пароль для кластера HDInsight. Поскольку в конце сценария кластер будет удален, а имя пользователя и пароль больше не потребуются, в качестве имени пользователя и пароля можно использовать любые строки. Если вы не хотите, чтобы появлялся запрос на ввод учетных данных, см. раздел [Работа с паролями, защищенными строками и учетными данными в Windows PowerShell][powershell-PSCredential]


##<a name="retrieve"></a>Извлечение выходных данных задания MapReduce
В этом разделе показано, как загружать и отображать выходные данные. Сведения об отображении результатов в Excel см. в разделах [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][hdinsight-ODBC] и [Подключение Excel к HDInsight с помощью Power Query][hdinsight-power-query].


**Извлечение результатов**

1. Откройте окно Azure PowerShell.
2. Перейдите в каталог **C:\\Tutorials\\WordCountJava**. Папка Azure PowerShell по умолчанию: **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**. Выполняемые командлеты загрузят выходной файл в текущую папку. У вас нет разрешений для загрузки файлов в системные папки.
2. Выполните следующие команды, чтобы задать значения:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-r-00000"

3. Выполните следующие команды для создания объекта контекста хранилища Azure:

		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

4. Выполните следующие команды, чтобы загрузить и отобразить выходные данные:

		Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

После завершения задания можно экспортировать данные в SQL Server или в базу данных SQL Azure с помощью [Sqoop][hdinsight-use-sqoop] либо экспортировать данные в Excel.

##<a id="nextsteps"></a>Дальнейшие действия
В этом учебнике вы узнали, как разработать задание MapReduce на языке Java, протестировать приложение в эмуляторе HDInsight и написать скрипт Azure PowerShell для подготовки кластера HDInsight и выполнения задания MapReduce в кластере. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Разработка программ MapReduce с потоковой передачей C# Hadoop для HDInsight][hdinsight-develop-streaming]
- [Начало работы с Azure HDInsight][hdinsight-get-started]
- [Приступая к работе с эмулятором HDInsight][hdinsight-emulator]
- [Использование хранилища больших двоичных объектов Azure с HDInsight][hdinsight-storage]
- [Администрирование HDInsight с использованием Azure PowerShell][hdinsight-admin-powershell]
- [Отправка данных в HDInsight][hdinsight-upload-data]
- [Использование Hive с HDInsight][hdinsight-use-hive]
- [Использование Pig с HDInsight][hdinsight-use-pig]
- [Подключение Excel к HDInsight с помощью Power Query][hdinsight-power-query]
- [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][hdinsight-ODBC]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install-configure]: ../install-configure-powershell.md



[image-emulator-wordcount-compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
[image-emulator-wordcount-run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png

<!---HONumber=July15_HO2-->