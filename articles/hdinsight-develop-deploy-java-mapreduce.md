<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight-Hadoop" urlDisplayName="HDInsight Tutorials" pageTitle="Разработка программ MapReduce на Java для Hadoop в HDInsight | Azure" metaKeywords="hdinsight, hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce, Java" description="Learn how to develop Java MapReduce programs on HDInsight emulator, how to deploy them to HDInsight." services="hdinsight" title="Develop Java MapReduce programs for Hadoop in HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="nitinme" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="10/10/2014" ms.author="nitinme" />

# Разработка программ MapReduce на Java для Hadoop в HDInsight
В данном учебнике приводится полный цикл разработки задания для Hadoop MapReduce по подсчету количества слов на языке Java с помощью Apache Maven. Здесь же демонстрируется, как тестировать приложения на эмуляторе HDInsight, а затем развертывать их и запускать на кластере HDInsight в Azure.

**Предварительные требования**

Перед началом работы с этим учебником необходимо выполнить следующие действия:

- Установите эмулятор Azure HDInsight. Указания см. в статье [Приступая к работе с эмулятором HDInsight][hdinsight-emulator].
- Установите среду Azure PowerShell на компьютере с эмулятором. Указания см. в статье [Установка и настройка Azure PowerShell][powershell-install-configure].
- Установите пакет JDK 7 или выше для платформы Java на компьютере с эмулятором. Он уже есть на этом компьютере.
- Установите и настройте [Apache Maven](http://maven.apache.org/).
- Получите подписку Azure. Указания см. в разделе [Варианты приобретения][azure-purchase-options], [Предложения для участников][azure-member-offers] или [Бесплатная пробная версия][azure-free-trial].

##Содержание

- [Использование Apache Maven при создании программы MapReduce по подсчету количества слов на языке Java](#develop)
- [Тестирование программы в эмуляторе](#test)
- [Передача файлов данных и приложения в хранилище больших двоичных объектов Azure](#upload)
- [Выполнение программы MapReduce в Azure HDInsight](#run)
- [Извлечение результатов MapReduce](#retrieve)
- [Дальнейшие действия](#nextsteps)

##<a name="develop"></a>Использование Apache Maven для создания программы MapReduce на языке Java

Создание приложения MapReduce по подсчету количества слов. Это простое приложение, которое подсчитывает количество повторений каждого слова в заданном наборе входных данных. В этом разделе мы выполним следующие задачи:

1. Создание проекта с помощью Apache Maven
2. Обновление модели объекта проекта (POM)
3. Создание для MapReduce приложения по подсчету количества слов
4. Сборка и создание пакета приложения

**Создание проекта с помощью Maven**

1. Создайте каталог **C:\Tutorials\WordCountJava\**.
2. Из командной строки вашей среды разработки перейдите во вновь созданный каталог.
3. Используйте команду mvn, которая установлена вместе с Maven, чтобы сформировать шаблон проекта.

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	В результате в текущем каталоге будет создан новый каталог с именем, указанным в параметре __artifactID__ (в данном примере это **wordcountjava**). В этом каталоге будут находиться следующие элементы:

	* __pom.xml__ - модель объекта проекта ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)), которая содержит информацию и данные о конфигурации, используемые для построения проекта;

	* __src__ - каталог, содержащий каталог __main\java\org\apache\hadoop\examples__, в котором будет создаваться приложение.
3. Удалите файл __src\test\java\org\apache\hadoop\examples\apptest.java__, так как он не используется в этом примере.

**Обновление модели объекта проекта (POM)**

1. Откройте для редактирования файл __pom.xml__ и добавьте следующие строки в раздел <dependencies>.

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

	Тогда Maven будет знать, что для проекта требуются библиотеки (перечисленные внутри тегов <artifactId\>) определенной версии (указанной внутри тегов <version\>). При компиляции он будет загружено из репозитория Maven по умолчанию. Вы можете воспользоваться [поиском по репозиторию Maven],(http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) чтобы получить дополнительную информацию.

2. Добавьте в файл __pom.xml__ следующее. Добавляемый код должен находиться в файле между тегами <project>...</project>, например, между тегами </dependencies> и </project>.

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

	В результате будет настроен подключаемый модуль [maven-shade-plugin],(http://maven.apache.org/plugins/maven-shade-plugin/)который используется для предотвращения дублирования лицензии в JAR-файле, собранном Maven. Причина - дублирующиеся файлы лицензий вызывают ошибку выполнения на кластере HDInsight. Использование maven-shade-plugin с реализацией ApacheLicenseResourceTransformer предотвращает возникновение этой ошибки.

	maven-shade-plugin также создает так называемый uberjar (или fatjar), который содержит все зависимости, требуемые приложением.

3. Сохраните файл __pom.xml__.

**Создание приложения по подсчету слов**

1. Перейдите к каталогу __wordcountjava\src\main\java\org\apache\hadoop\examples__ и переименуйте файл __app.java__ в __WordCount.java__.
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

	Обратите внимание, что имя пакета - **org.apache.hadoop.examples**, а имя класса - **WordCount**. Эти имена будут использоваться при отправке задания MapReduce.
	
3. Сохраните файл.

**Сборка приложения и создание его пакета**

1. Откройте командную строку и измените каталоги на каталог __wordcountjava__.

2. Выполните следующую команду, чтобы собрать файл JAR, содержащий приложение.

		mvn clean package

	При этом будут удалены остатки предыдущих сборок, загружены все неустановленные на текущий момент зависимости, затем будет произведена сборка и создание пакета приложения.

3. После завершения выполнения команды каталог __wordcountjava\target__ будет содержать файл __wordcountjava-1.0-SNAPSHOT.jar__.

	> [WACOM.NOTE] Файл __wordcountjava-1.0-SNAPSHOT.jar__ относится к типу uberjar (другое название - fatjar) и содержит все зависимости, необходимые для работы приложения.


##<a name="test"></a>Тестирование программы в эмуляторе

Тестирование задания MapReduce в эмуляторе HDInsight включает следующие процедуры:

1. Отправка файлов данных в HDFS на эмуляторе
2. Создание локальной группы пользователей
3. Выполнение задания по подсчету количества слов в MapReduce
4. Извлеките результаты задания.

В эмуляторе HDInsight используется HDFS в качестве файловой системы по умолчанию.  При желании вы можете настроить эмулятор HDInsight на использование хранилища BLOB-объектов Azure. Дополнительную информацию см. в статье [Приступая к работе с эмулятором HDInsight][hdinsight-emulator-wasb]. 

В этом учебнике для передачи файлов данных в HDFS будет использоваться команда HDFS copyFromLocal. В следующем разделе показано, как с помощью Azure PowerShell передавать файлы в хранилище BLOB-объектов Azure. Дополнительную информацию о других способах передачи файлов в хранилище больших двоичных объектов Azure см. в статье [Отправка данных в HDInsight][hdinsight-upload-data].

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

> [WACOM.NOTE] В командах Hadoop HDFS учитывается регистр.

**Копирование файлов данных в HDFS эмулятора**

0. Откройте командную строку Hadoop на вашем рабочем столе. Командную строку Hadoop устанавливает программа установки эмулятора.
1. В окне командной строки Hadoop выполните следующую команду, чтобы создать каталог для входных файлов:

		hadoop fs -mkdir /WordCount/Input

	Здесь используется относительный путь. Эта запись аналогична следующей:

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2. Выполните следующую команду, чтобы скопировать некоторые текстовые файлы во входную папку в HDFS:

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\*.txt /WordCount/Input

	Задание MapReduce будет подсчитывать слова в этих файлах.

3. Выполните следующую команду, чтобы получить список переданных файлов и проверить его:

		hadoop fs -ls /WordCount/Input

**Создания локальной группы пользователей**

Для выполнения заданий MapReduce на кластере необходимо создать локальную группу пользователей с названием "hdfs". К этой группе также следует добавить пользователя "hadoop", а также локального пользователя, по учетным данным которого осуществляется вход в эмулятор. Выполните следующие команды в окне команд с повышенными привилегиями:

		# Add a user group called hdfs		
		net localgroup hdfs /add

		# Adds a user called hadoop to the group
		net localgroup hdfs hadoop /add

		# Adds the local user to the group
		net localgroup hdfs <username> /add

**Запуск задания MapReduce из командной строки Hadoop**

1. Откройте командную строку Hadoop на вашем рабочем столе.
2. Выполните следующую команду, чтобы удалить структуру папок /WordCount/Output из HDFS.  /WordCount/Output - выходная папка задания MapReduce по подсчету слов. Если папка уже есть, задание MapReduce завершится ошибкой. Этот шаг необходим, если вы запускаете задание во второй раз.

		hadoop fs -rm - r /WordCount/Output

2. Выполните следующую команду:

		hadoop jar C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

	При успешном завершении задания вы должны получить выходные данные следующего вида:

	![HDI.EMulator.WordCount.Run][image-emulator-wordcount-run]

	На снимке экрана видно, что процедуры map и reduce завершены на 100 %. Здесь также указывается идентификатор задания. Чтобы получить этот же отчет, можно также открыть ярлык **Состояние Hadoop MapReduce** на вашем рабочем столе и найти идентификатор этого задания.

Для запуска задания MapReduce можно также использовать Azure PowerShell. Указания см. в статье [Приступая к работе с эмулятором HDInsight][hdinsight-emulator].

**Отображение выходных данных из HDFS**

1. Откройте командную строку Hadoop.
2. Выполните следующие команды, чтобы отобразить выходные данные:

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-r-00000

	Чтобы получить страничное представление, добавьте в конце команды "|more". Можно также найти строковый шаблон с помощью команды findstr.

		hadoop fs -cat /WordCount/Output/part-r-00000 | findstr "there"

На данный момент вы разработали задание MapReduce для счетчика слов и успешно его протестировали в эмуляторе.  Следующим действием будет развертывание и выполнение задания в Azure HDInsight.



##<a id="upload"></a>Передача данных и приложения в хранилище больших двоичных объектов Azure
Azure HDInsight использует для хранения данных хранилище BLOB-объектов Azure. При подготовке кластера HDInsight контейнер хранилища BLOB-объектов Azure используется для хранения системных файлов. Для хранения файлов данных можно использовать этот контейнер по умолчанию или другой контейнер (в той же учетной записи хранения Azure или в другой учетной записи хранения в том же центре обработки данных, где находится кластер). 

В этом учебнике вы создадите контейнер для файлов данных и приложения MapReduce в отдельной учетной записи хранения. Файлы данных представляют собой текстовые файлы, которые находятся в каталоге **C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common** на рабочей станции с эмулятором.

**Создание хранилища больших двоичных объектов и контейнера**

1. Откройте Azure PowerShell.
2. Задайте переменные, а затем выполните команды:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

	Значение переменной **$subscripionName** связано с вашей подпиской Azure. Вам необходимо присвоить имена переменным **$storageAccountName\_Data** и **$containerName\_Data**. Ограничения именования см. в разделе [Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них](http://msdn.microsoft.com/ru-ru/library/windowsazure/dd135715.aspx). 

3. Выполните следующие команды, чтобы создать учетную запись хранения и контейнер хранилища BLOB-объектов в учетной записи

		# Select Azure subscription
		Select-AzureSubscription $subscriptionName
		
		# Create a storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location
				
		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageAccountKey  
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

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\"
		$destFolder = "WordCount/Input"

	Значения **$storageAccountName\_Data** и **$containerName\_Data** - те же, что вы определили в последней процедуре.

	Обратите внимание, что исходной является папка **c:\Hadoop\hadoop-1.1.0-SNAPSHOT**, а целевой - папка **WordCount/Input**.

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

	Должны появиться сведения о загруженных текстовых файлах данных.

**Отправка приложения по подсчету слов**

1. Откройте Azure PowerShell.
2. Задайте первые три переменные, а затем выполните команды:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$jarFile = "C:\Tutorials\WordCountJava\WordCount.jar"
		$blobFolder = "WordCount/jars"

	Значения **$storageAccountName\_Data** и **$containerName\_Data** - те же, что вы определили в последней процедуре, то есть файл данных и приложение будут переданы в тот же контейнер в той же учетной записи хранения.

	Обратите внимание, что целевой является папка **WordCount/jars**.

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

В данном разделе вы научитесь создавать скрипты PowerShell, предназначенные для выполнения следующих задач:

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
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"
		$location = "<MicrosoftDataCenter>"     ### must match the data storage account location
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
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default
		
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

3. Задайте первые шесть переменных скрипта: **$stringPrefix** используется в качестве префикса указанной строки в имени кластера HDInsight, имени учетной записи хранения и имени контейнера хранилища больших двоичных объектов. Поскольку количество символов в названии должно быть больше 3 и меньше 24, следите за тем, чтобы общее количество символов в строке и именах, используемых сценарием, не превышало максимального значения. Значения параметра **$stringPrefix** указываются только в нижнем регистре. 
 
	**$storageAccountName\_Data** и **$containerName\_Data** - это учетная запись хранения и контейнер, которые используются для хранения файлов данных и приложений. Значение **$location** должно соответствовать расположению учетной записи хранения данных.

4. Просмотрите остальные переменные.
5. Сохраните файл скрипта.
6. Откройте Azure PowerShell.
7. Выполните следующую команду, чтобы задать политику выполнения remotesigned:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. При появлении запроса введите имя пользователя и пароль для кластера HDInsight. Поскольку в конце сценария кластер будет удален, а имя пользователя и пароль больше не потребуются, в качестве имени пользователя и пароля можно использовать любые строки. Информацию о том, что необходимо сделать, чтобы запрос на ввод учетных данных не появлялся, см. в статье [Working with Passwords, Secure Strings and Credentials in Windows PowerShell][powershell-PSCredential] (Работа с паролями, защищенными строками и учетными данными в Windows PowerShell)


##<a name="retrieve"></a>Извлечение выходных данных задания MapReduce
В этом разделе показано, как загружать и отображать выходные данные.  Дополнительную информацию об отображении результатов в Excel см. в статьях [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][hdinsight-ODBC] и [Подключение Excel к HDInsight с помощью Power Query][hdinsight-power-query].


**Получение выходных данных**

1. Откройте окно Azure PowerShell.
2. Измените каталог на **C:\Tutorials\WordCountJava**. Папка Azure PowerShell по умолчанию - **C:\Windows\System32\WindowsPowerShell\v1.0**. Выполняемые командлеты загрузят выходной файл в текущую папку.  У вас нет разрешений для загрузки файлов в системные папки.
2. Выполните следующие команды, чтобы задать значения:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-r-00000"
	
3. Выполните следующие команды, чтобы создать объект контекста хранилища Azure: 
		
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageAccountKey  

4. Выполните следующие команды, чтобы загрузить и отобразить выходные данные:

		Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

После завершения задания вы можете экспортировать данные в базу данных SQL Server или SQL Azure с помощью [Sqoop][hdinsight-use-sqoop] либо экспортировать данные в Excel.  

##<a id="nextsteps"></a>Дальнейшие действия
В этом учебнике вы узнали, как разработать задание MapReduce на Java, протестировать приложение в эмуляторе HDInsight, написать сценарий PowerShell для подготовки кластера HDInsight и выполнения MapReduce в кластере. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

- [Разработка программ MapReduce потоковой передачи Hadoop на C# для HDInsight][hdinsight-develop-streaming]
- [Приступая к работе с Azure HDInsight][hdinsight-get-started]
- [Приступая к работе с эмулятором HDInsight][hdinsight-emulator]
- [Использование хранилища BLOB-объектов Azure с HDInsight][hdinsight-storage]
- [Администрирование HDInsight с использованием PowerShell][hdinsight-admin-powershell]
- [Отправка данных в HDInsight][hdinsight-upload-data]
- [Использование Hive с HDInsight][hdinsight-use-hive]
- [Использование Pig с HDInsight][hdinsight-use-pig]
- [Подключение Excel к HDInsight с помощью Power Query][hdinsight-power-query]
- [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][hdinsight-ODBC]

[azure-purchase-options]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/ru-ru/pricing/free-trial/

[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/

[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/

[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator/#blobstorage
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/s
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[Powershell-install-configure]: ../install-configure-powershell/



[image-emulator-wordcount-compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
[image-emulator-wordcount-run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png


<!--HONumber=35.1-->
