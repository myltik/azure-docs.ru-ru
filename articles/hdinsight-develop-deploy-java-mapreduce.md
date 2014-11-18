<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight-Hadoop" urlDisplayName="HDInsight Tutorials" pageTitle="Разработка программ MapReduce на Java для Hadoop в HDInsight | Azure" metaKeywords="hdinsight, hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce, Java" description="Познакомьтесь с разработкой программ MapReduce на Java с помощью эмулятора HDInsight, а также с процессом их развертывания на HDInsight." services="hdinsight" title="Разработка программ MapReduce на Java для Hadoop в HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="nitinme" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="10/10/2014" ms.author="nitinme" />

# Разработка программ MapReduce на Java для Hadoop в HDInsight

В данном учебнике приводится полный цикл разработки задания для Hadoop MapReduce по подсчету количества слов на языке Java с помощью Apache Haven. Здесь же демонстрируется, как тестировать приложения на эмуляторе HDInsight, а затем развертывать их и запускать на кластере HDInsight в Azure.

**Предварительные требования:**

Перед началом работы с этим учебником необходимо выполнить следующие действия:

-   Установите эмулятор Azure HDInsight. Инструкции см. в разделе [Приступая к работе с эмулятором HDInsight][Приступая к работе с эмулятором HDInsight].
-   Установите среду Azure PowerShell на компьютере с эмулятором. Инструкции см. в разделе [Установка и настройка Azure PowerShell][Установка и настройка Azure PowerShell].
-   Установите пакет JDK 7 или выше для платформы Java на компьютере с эмулятором. Он уже есть на этом компьютере.
-   Установите и настройте [Apache Maven][Apache Maven].
-   Получите подписку Azure. Инструкции см. в разделе [Варианты приобретения][Варианты приобретения], [Предложения для участников][Предложения для участников] или [Бесплатное пробное использование][Бесплатное пробное использование].

## Содержание

-   [Использование Apache Maven при создании на языке Java программы MapReduce по подсчету количества слов][Использование Apache Maven при создании на языке Java программы MapReduce по подсчету количества слов]
-   [Тестирование программы в эмуляторе][Тестирование программы в эмуляторе]
-   [Передача файлов данных и приложения в хранилище BLOB-объектов Azure][Передача файлов данных и приложения в хранилище BLOB-объектов Azure]
-   [Выполнение программы MapReduce в Azure HDInsight][Выполнение программы MapReduce в Azure HDInsight]
-   [Извлечение результатов MapReduce][Извлечение результатов MapReduce]
-   [Дальнейшие действия][Дальнейшие действия]

## <a name="develop"></a>Использование Apache Maven для создания программы MapReduce на языке Java

Создание приложения MapReduce по подсчету количества слов. Это простое приложение, которое подсчитывает количество повторений каждого слова в заданном наборе входных данных. В этом разделе мы выполним следующие задачи:

1.  Создание проекта с помощью Apache Maven
2.  Обновление модели объекта проекта (POM)
3.  Создание для MapReduce приложения по подсчету количества слов
4.  Сборка и создание пакета приложения

**Для создания проекта с помощью Maven:**

1.  Создайте каталог \*\*C:\\Tutorials\\WordCountJava\*\*.
2.  Из командной строки вашей среды разработки перейдите во вновь созданный каталог.
3.  Используйте команду **mvn**, которая будет установлена вместе с Maven, для создания шаблона проекта.

        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    При этом в текущем каталоге будет создан новый каталог с именем, указанным в параметре **artifactID** (в данном примере это **wordcountjava**). В этом каталоге будут находиться следующие элементы:

    -   **pom.xml** — это модель объекта проекта ([POM][POM]), которая содержит информацию и подробности конфигурации, использующиеся при сборке проекта;

    -   **src** — каталог, который содержит каталог **main\\java\\org\\apache\\hadoop\\examples**, где будет авторизовано это приложение.

4.  Удалите файл **src\\test\\java\\org\\apache\\hadoop\\examples\\apptest.java**, так как он не используется в этом примере.

**Для обновления модели объекта проекта (POM):**

1.  Откройте для редактирования файл **pom.xml** и добавьте следующие строки в раздел `<dependencies>`.

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

    Тогда Maven будет знать, что для проекта требуются библиотеки (перечисленные в <artifactid\>) определенной версии (указанные в <version\>). При компиляции он будет загружено из репозитория Maven по умолчанию. Можно воспользоваться [поиском по репозиторию Maven][поиском по репозиторию Maven], чтобы получить дополнительную информацию.

2.  Добавьте в файл **pom.xml** следующее: Эти строки должны находиться в файле внутри тегов `<project>...</project>`; например, между тегами `</dependencies>` и `</project>`.

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

    Также будет настроен подключаемый модуль[maven-shade-plugin][maven-shade-plugin], который используется для предотвращения дублирования лицензии в файле JAR, собранном Maven. Причина — дублирующиеся файлы лицензий вызывают ошибку выполнения на кластере HDInsight. Использование maven-shade-plugin с реализацией `ApacheLicenseResourceTransformer` предотвращает возникновение этой ошибки.

    maven-shade-plugin также создает так называемый uberjar (или fatjar), который содержит все зависимости, требуемые приложением.

3.  Сохраните файл **pom.xml**.

**Для создания приложения по подсчету слов:**

1.  Перейдите в каталог **wordcountjava\\src\\main\\java\\org\\apache\\hadoop\\examples** и переименуйте файл **app.java** в **WordCount.java**.
2.  Откройте Блокнот.
3.  Скопируйте следующую программу и вставьте ее в Блокнот.

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

4.  Сохраните файл.

**Для сборки и создания пакета приложения:**

1.  Откройте командную строку и перейдите в каталог **wordcountjava**.

2.  Выполните следующую команду, чтобы собрать файл JAR, содержащий приложение.

        mvn clean package

    При этом будут удалены остатки предыдущих сборок, загружены все неустановленные на текущий момент зависимости, затем будет произведена сборка и создание пакета приложения.

3.  После завершения выполнения команды в каталоге **wordcountjava\\target** будет находиться файл с именем **wordcountjava-1.0-SNAPSHOT.jar**.

    > [WACOM.NOTE] Файл **wordcountjava-1.0-SNAPSHOT.jar** относится к типу "uberjar" (другое название "fatjar") и содержит все зависимости, необходимые для нормальной работы приложения.

## <a name="test"></a>Тестирование программы MapReduce в эмуляторе

Тестирование задания MapReduce в эмуляторе HDInsight включает следующие процедуры:

1.  Отправка файлов данных в HDFS на эмуляторе
2.  Создание локальной группы пользователей
3.  Выполнение задания по подсчету количества слов в MapReduce
4.  Извлеките результаты задания.

В эмуляторе HDInsight используется HDFS в качестве файловой системы по умолчанию. При желании вы можете настроить эмулятор HDInsight на использование хранилища BLOB-объектов Azure. Подробные сведения см. в разделе [Приступая к работе с эмулятором HDInsight][1].

В этом учебнике для передачи файлов данных в HDFS будет использоваться команда HDFS *copyFromLocal*. В следующем разделе показано, как с помощью Azure PowerShell передавать файлы в хранилище BLOB-объектов Azure. Сведения о других способах передачи файлов в хранилище BLOB-объектов Azure см. в разделе [Передача данных в HDInsight][Передача данных в HDInsight].

Для работы с этим учебником используется следующая структура папок HDFS:

|---------------------------|-----------------------------------------------------|
| Папка                     | Примечание.                                         |
| /WordCount                | Корневая папка для проекта счетчика слов.           |
| /WordCount/Apps           | Папка исполняемых файлов программ mapper и reducer. |
| /WordCount/Input          | Папка исходных файлов MapReduce.                    |
| /WordCount/Output         | Папка выходных файлов MapReduce.                    |
| /WordCount/MRStatusOutput | Выходная папка заданий.                             |

В этом учебнике в качестве файлов данных используются текстовые файлы .txt, расположенные в каталоге %hadoop\_home%.

> [WACOM.NOTE] В командах Hadoop HDFS учитывается регистр.

**Копирование файлов данных в HDFS эмулятора**

1.  Откройте командную строку Hadoop на вашем рабочем столе. Командную строку Hadoop устанавливает программа установки эмулятора.
2.  В окне командной строки Hadoop выполните следующую команду, чтобы создать каталог для входных файлов:

        hadoop fs -mkdir /WordCount/Input

    Здесь используется относительный путь. Эта запись аналогична следующей:

        hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3.  Выполните следующую команду, чтобы скопировать некоторые текстовые файлы во входную папку в HDFS:

        hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\*.txt /WordCount/Input

    Задание MapReduce будет подсчитывать слова в этих файлах.

4.  Выполните следующую команду, чтобы получить список переданных файлов и проверить его:

        hadoop fs -ls /WordCount/Input

**Для создания локальной группы пользователей:**

Для выполнения заданий MapReduce на кластере необходимо создать локальную группу пользователей с названием "hdfs". К этой группе также следует добавить пользователя "hadoop", а также локального пользователя, по учетным данным которого осуществляется вход в эмулятор. Выполните следующие команды в окне команд с повышенными привилегиями:

        # Add a user group called hdfs      
        net localgroup hdfs /add

        # Adds a user called hadoop to the group
        net localgroup hdfs hadoop /add

        # Adds the local user to the group
        net localgroup hdfs <username> /add

**Запуск задания MapReduce из командной строки Hadoop**

1.  Откройте командную строку Hadoop на вашем рабочем столе.
2.  Выполните следующую команду, чтобы удалить структуру папок /WordCount/Output из HDFS. /WordCount/Output — выходная папка задания MapReduce для счетчика слов. Если папка уже есть, задание MapReduce завершится ошибкой. Этот шаг необходим, если вы запускаете задание во второй раз.

        hadoop fs -rm - r /WordCount/Output

3.  Выполните следующую команду:

        hadoop jar C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

    При успешном завершении задания вы должны получить выходные данные следующего вида:

    ![HDI.EMulator.WordCount.Run][HDI.EMulator.WordCount.Run]

    На снимке экрана видно, что процедуры map и reduce завершены на 100 %. Кроме того, здесь перечислены идентификаторы заданий. Этот отчет можно также получить, открыв ярлык **статуса Hadoop MapReduce** на своем настольном компьютере и отыскав тот же идентификатор задания.

Для запуска задания MapReduce можно также использовать Azure PowerShell. Инструкции см. в разделе [Приступая к работе с эмулятором HDInsight][Приступая к работе с эмулятором HDInsight].

**Отображение выходных данных из HDFS**

1.  Откройте командную строку Hadoop.
2.  Выполните следующие команды, чтобы отобразить выходные данные:

        hadoop fs -ls /WordCount/Output/
        hadoop fs -cat /WordCount/Output/part-r-00000

    Чтобы получить страничное представление, добавьте в конце команды "|more". Можно также найти строковый шаблон с помощью команды findstr.

        hadoop fs -cat /WordCount/Output/part-r-00000 | findstr "there"

На данный момент вы разработали задание MapReduce для счетчика слов и успешно его протестировали в эмуляторе. Следующим действием будет развертывание и выполнение задания в Azure HDInsight.

## <span id="upload"></span></a>Передача данных и приложения в хранилище BLOB-объектов Azure

Azure HDInsight использует для хранения данных хранилище BLOB-объектов Azure. При подготовке кластера HDInsight контейнер хранилища BLOB-объектов Azure используется для хранения системных файлов. Для хранения файлов данных можно использовать этот контейнер по умолчанию или другой контейнер (в той же учетной записи хранения Azure или в другой учетной записи хранения в том же центре обработки данных, где находится кластер).

В этом учебнике вы создадите контейнер для файлов данных и приложения MapReduce в отдельной учетной записи хранения. Файлы данных представляют собой текстовые файлы, которые находятся в каталоге **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common** на рабочей станции с эмулятором.

**Создание хранилища BLOB-объектов и контейнера**

1.  Откройте Azure PowerShell.
2.  Задайте переменные, а затем выполните команды:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<AzureStorageAccountName>"  
        $containerName_Data = "<ContainerName>"
        $location = "<MicrosoftDataCenter>"  # For example, "East US"

    **$subscripionName** относится к вашей подписке Azure. Необходимо указать имя для **$storageAccountName\_Data** и **$containerName\_Data**. Ограничения именования см. в разделе [Присвоение имен и ссылки на контейнеры, BLOB-объекты и метаданные][Присвоение имен и ссылки на контейнеры, BLOB-объекты и метаданные].

3.  Выполните следующие команды, чтобы создать учетную запись хранения и контейнер хранилища BLOB-объектов в учетной записи

        # Select Azure subscription
        Select-AzureSubscription $subscriptionName

        # Create a storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location

        # Create a Blob storage container
        $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
        New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4.  Выполните следующие команды, чтобы проверить учетную запись хранения и контейнер:

        Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
        Get-AzureStorageContainer -Context $destContext

**Передача файлов данных**

1.  Откройте Azure PowerShell.
2.  Задайте первые три переменные, а затем выполните команды:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<AzureStorageAccountName>"  
        $containerName_Data = "<ContainerName>"

        $localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\"
        $destFolder = "WordCount/Input"

    **$storageAccountName\_Data** и **$containerName\_Data** — те же, что вы определили в последней процедуре.

    Заметьте, что исходной является папка **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT**, а целевой — папка **WordCount/Input**.

3.  Выполните следующие команды, чтобы получить список TXT-файлов в исходной папке:

        # Get a list of the txt files
        $filesAll = Get-ChildItem $localFolder
        $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  Выполните следующие команды, чтобы создать объект контекста хранилища:

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5.  Выполните следующие команды, чтобы скопировать файлы:

        # Copy the file from local workstation to the Blob container        
        foreach ($file in $filesTxt){

            $fileName = "$localFolder\$file"
            $blobName = "$destFolder/$file"

            write-host "Copying $fileName to $blobName"

            Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
        }

6.  Выполните следующую команду, чтобы получить список переданных файлов:

        # List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
        Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

    Должны появиться сведения о загруженных текстовых файлах данных.

**Передача приложения счетчика слов**

1.  Откройте Azure PowerShell.
2.  Задайте первые три переменные, а затем выполните команды:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<AzureStorageAccountName>"  
        $containerName_Data = "<ContainerName>"

        $jarFile = "C:\Tutorials\WordCountJava\WordCount.jar"
        $blobFolder = "WordCount/jars"

    **$storageAccountName\_Data** и **$containerName\_Data** — те же, что вы определили в последней процедуре, то есть файл данных и приложение будут переданы в тот же контейнер в той же учетной записи хранения.

    Заметьте, целевой является папка **WordCount/jars**.

3.  Выполните следующие команды, чтобы создать объект контекста хранилища:

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

4.  Выполните следующие команды, чтобы скопировать приложения:

        Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

5.  Выполните следующую команду, чтобы получить список переданных файлов:

        # List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
        Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

    Должен появиться указанный JAR-файл.

## <a name="run"></a>Выполнение задания MapReduce в Azure HDInsight

В данном разделе вы научитесь создавать скрипты PowerShell, предназначенные для выполнения следующих задач:

1.  Подготовка кластера HDInsight

    1.  Создание учетной записи хранения, которая будет использоваться в качестве файловой системы по умолчанию для кластера HDInsight
    2.  Создание контейнера хранилища BLOB-объектов
    3.  Создание кластера HDInsight

2.  Отправка задания MapReduce

    1.  Создание определения задания MapReduce
    2.  Отправка задания MapReduce
    3.  Ожидание завершения задания
    4.  Отображение стандартной ошибки
    5.  Отображение стандартного вывода

3.  Удаление кластера

    1.  Удаление кластера HDInsight
    2.  Удаление учетной записи хранения, которая использовалась в качестве файловой системы по умолчанию для кластера HDInsight

**Запуск сценария PowerShell**

1.  Откройте Блокнот.
2.  Скопируйте следующий код и вставьте в Блокнот:

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

3.  Задайте первые шесть переменных скрипта: **$stringPrefix** используется в качестве префикса указанной строки в имени кластера HDInsight, имени аккаунта хранилища и имени контейнера хранилища BLOD-объектов. Поскольку количество символов в названии должно быть больше 3 и меньше 24, следите за тем, чтобы общее количество символов в строке и именах, используемых сценарием, не превышало максимального значения. В параметре **$stringPrefix** следует использовать только символы нижнего регистра.

    **$storageAccountName\_Data** and **$containerName\_Data** — это учетная запись хранения и контейнер, которые используются для хранения файлов данных и приложений. Значение **$location** должно соответствовать расположению учетной записи хранения данных.

4.  Просмотрите остальные переменные.
5.  Сохраните файл скрипта.
6.  Откройте Azure PowerShell.
7.  Выполните следующую команду, чтобы задать политику выполнения remotesigned:

        PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  При появлении запроса введите имя пользователя и пароль для кластера HDInsight. Поскольку в конце сценария кластер будет удален, а имя пользователя и пароль больше не потребуются, в качестве имени пользователя и пароля можно использовать любые строки. Если вы не хотите, чтобы появлялся запрос на ввод учетных данных, см. раздел [Работа с паролями, защищенными строками и учетными данными в Windows PowerShell][Работа с паролями, защищенными строками и учетными данными в Windows PowerShell]

## <a name="retrieve"></a>Извлечение выходных данных задания MapReduce

В этом разделе показано, как загружать и отображать выходные данные. Сведения об отображении результатов в Excel см. в разделах [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC] и [Подключение Excel к HDInsight с помощью Power Query][Подключение Excel к HDInsight с помощью Power Query].

**Извлечение результатов**

1.  Откройте окно Azure PowerShell.
2.  Перейдите в каталог **c:\\Tutorials\\WordCountJava**. Папка Azure PowerShell по умолчанию: **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**. Выполняемые командлеты загрузят выходной файл в текущую папку. У вас нет разрешений для загрузки файлов в системные папки.
3.  Выполните следующие команды, чтобы задать значения:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"
        $blobName = "WordCount/Output/part-r-00000"

4.  Выполните следующие команды, чтобы создать объект контекста хранилища Azure:

        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
        $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

5.  Выполните следующие команды, чтобы загрузить и отобразить выходные данные:

        Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
        cat "./$blobName" | findstr "there"

После завершения задания можно экспортировать данные в базу данных SQL Server или Azure SQL с помощью [Sqoop][Sqoop] либо экспортировать данные в Excel.

## <span id="nextsteps"></span></a>Дальнейшие действия

В этом учебнике вы узнали, как разработать задание MapReduce на Java, протестировать приложение в эмуляторе HDInsight, написать сценарий PowerShell для подготовки кластера HDInsight и выполнения MapReduce в кластере. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

-   [Разработка программ MapReduce потоковой передачи Hadoop на C\# для HDInsight][Разработка программ MapReduce потоковой передачи Hadoop на C\# для HDInsight]
-   [Приступая к работе с Azure HDInsight][Приступая к работе с Azure HDInsight]
-   [Приступая к работе с эмулятором HDInsight][Приступая к работе с эмулятором HDInsight]
-   [Использование хранилища BLOB-объектов Azure с HDInsight][Использование хранилища BLOB-объектов Azure с HDInsight]
-   [Администрирование HDInsight с использованием PowerShell][Администрирование HDInsight с использованием PowerShell]
-   [Отправка данных в HDInsight][Передача данных в HDInsight]
-   [Использование Hive с HDInsight][Использование Hive с HDInsight]
-   [Использование Pig с HDInsight][Использование Pig с HDInsight]
-   [Подключение Excel к HDInsight с помощью Power Query][Подключение Excel к HDInsight с помощью Power Query]
-   [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC][Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC]

  [Приступая к работе с эмулятором HDInsight]: ../hdinsight-get-started-emulator/
  [Установка и настройка Azure PowerShell]: ../install-configure-powershell/
  [Apache Maven]: http://maven.apache.org/
  [Варианты приобретения]: http://azure.microsoft.com/ru-ru/pricing/purchase-options/
  [Предложения для участников]: http://azure.microsoft.com/ru-ru/pricing/member-offers/
  [Бесплатное пробное использование]: http://azure.microsoft.com/ru-ru/pricing/free-trial/
  [Использование Apache Maven при создании на языке Java программы MapReduce по подсчету количества слов]: #develop
  [Тестирование программы в эмуляторе]: #test
  [Передача файлов данных и приложения в хранилище BLOB-объектов Azure]: #upload
  [Выполнение программы MapReduce в Azure HDInsight]: #run
  [Извлечение результатов MapReduce]: #retrieve
  [Дальнейшие действия]: #nextsteps
  [POM]: http://maven.apache.org/guides/introduction/introduction-to-the-pom.html
  [поиском по репозиторию Maven]: http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar
  [maven-shade-plugin]: http://maven.apache.org/plugins/maven-shade-plugin/
  [1]: ../hdinsight-get-started-emulator/#blobstorage
  [Передача данных в HDInsight]: ../hdinsight-upload-data/
  [HDI.EMulator.WordCount.Run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png
  [Присвоение имен и ссылки на контейнеры, BLOB-объекты и метаданные]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd135715.aspx
  [Работа с паролями, защищенными строками и учетными данными в Windows PowerShell]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
  [Подключение Excel к HDInsight с помощью драйвера Microsoft Hive ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Подключение Excel к HDInsight с помощью Power Query]: ../hdinsight-connect-excel-power-query/
  [Sqoop]: ../hdinsight-use-sqoop/
  [Разработка программ MapReduce потоковой передачи Hadoop на C\# для HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Приступая к работе с Azure HDInsight]: ../hdinsight-get-started/
  [Использование хранилища BLOB-объектов Azure с HDInsight]: ../hdinsight-use-blob-storage/
  [Администрирование HDInsight с использованием PowerShell]: ../hdinsight-administer-use-powershell/
  [Использование Hive с HDInsight]: ../hdinsight-use-hive/
  [Использование Pig с HDInsight]: ../hdinsight-use-pig/
