<properties title="Build an HBase application using Maven" pageTitle="Создание приложения HBase с помощью Maven" description="Узнайте, как использовать Apache Maven для создания приложения Java для Apache HBase и его последующего развертывания в Azure HDInsight" metaKeywords="Maven hbase hadoop, hbase hadoop, maven java hbase, maven java hbase hadoop, maven java hadoop, hbase hdinsight, hbase java hdinsight, maven hdinsight, maven java hdinsight, hadoop database, hdinsight database" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/20/2014" ms.author="larryfr" />

##Использование Maven для сборки приложений Java, которые используют HBase с HDInsight (Hadoop)

Узнайте, как создавать и выполнять сборку приложения [Apache HBase](http://hbase.apache.org/) на языке Java с помощью Apache Maven. Затем приложение будет использоваться с Azure HDInsight (Hadoop).

[Maven](http://maven.apache.org/) - это инструмент для управления проектами программного обеспечения и повышения их обозримости, позволяющий создавать ПО, документацию и отчеты для проектов Java. Из данной статьи вы узнаете, как использовать его для создания базового приложения Java, которое создает, формирует запросы и удаляет таблицу HBase в кластере Azure HDInsight.

##Требования

* [Пакет JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 или выше для платформы Java.

* [Maven].(http://maven.apache.org/)

* [Кластер Azure HDInsight с HBase.](/ru-ru/documentation/articles/hdinsight-hbase-get-started/#create-hbase-cluster)

##Создание проекта

1. Из командной строки вашей среды разработки измените каталоги на то место, где вы хотите создать проект. Например: `cd code\hdinsight`

2. Используйте команду mvn, которая будет установлена вместе с Maven, для создания шаблона проекта.

		mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	При этом в текущем каталоге будет создан новый каталог с именем, указанным в параметре artifactID (в данном случае **hbaseapp**). В этом каталоге будут находиться следующие элементы:

	* __pom.xml__ - модель объекта проекта ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)), которая содержит информацию и данные о конфигурации, используемые для построения проекта;

	* src - каталог, в котором содержится каталог main\java\com\microsoft\examples, в котором будет создаваться приложение.

3. Удалите файл src\test\java\com\microsoft\examples\apptest.java, так как он не используется в этом примере.

##Обновление модели объекта проекта

1. Откройте для редактирования файл __pom.xml__ и добавьте следующие строки в раздел <dependencies>.

		<dependency>
      	  <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

	Они указывают Maven, что для проекта требуется hbase-client версии 0.98.4-hadoop2. При компиляции он будет загружено из репозитория Maven по умолчанию. Вы можете воспользоваться [поиском по репозиторию Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) , чтобы просмотреть дополнительную информацию об этой зависимости.

2. Добавьте в файл __pom.xml__ следующее: Добавляемый код должен находиться в файле между тегами <project>...</project>; например, между </dependencies> и </project>.

		<build>
		  <sourceDirectory>src</sourceDirectory>
		  <resources>
	        <resource>
	          <directory>${basedir}/conf</directory>
	          <filtering>false</filtering>
	          <includes>
	            <include>hbase-site.xml</include>
	          </includes>
	        </resource>
	      </resources>
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

	При этом настраивается ресурс (conf\hbase-site.xml), который содержит информацию о конфигурации для HBase.

	> [WACOM.NOTE] Также можно настроить значения конфигурации непосредственно из кода. См. комментарии к примеру CreateTable ниже, чтобы узнать, как это сделать.

	Также будет настроен [maven-shade-plugin](http://maven.apache.org/plugins/maven-shade-plugin/), который используется для предотвращения дублирования лицензии в JAR-файле, собранном Maven. Причина - дублирующиеся файлы лицензий вызывают ошибку выполнения на кластере HDInsight. Использование maven-shade-plugin с реализацией ApacheLicenseResourceTransformer предотвращает возникновение этой ошибки.

	maven-shade-plugin также создает так называемый uberjar (или fatjar), который содержит все зависимости, требуемые приложением.

3. Сохраните файл pom.xml.

4. Создайте новый каталог с именем conf в каталоге hbaseapp. В каталоге conf создайте новый файл с именем hbase-site.xml и используйте следующее в качестве содержимого этого файла:

		<?xml version="1.0"?>
		<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
		<!--
		/**
		 * Copyright 2010 The Apache Software Foundation
		 *
		 * Licensed to the Apache Software Foundation (ASF) under one
		 * or more contributor license agreements.  See the NOTICE file
		 * distributed with this work for additional information
		 * regarding copyright ownership.  The ASF licenses this file
		 * to you under the Apache License, Version 2.0 (the
		 * "License"); you may not use this file except in compliance
		 * with the License.  You may obtain a copy of the License at
		 *
		 *     http://www.apache.org/licenses/LICENSE-2.0
		 *
		 * Unless required by applicable law or agreed to in writing, software
		 * distributed under the License is distributed on an "AS IS" BASIS,
		 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		 * See the License for the specific language governing permissions and
		 * limitations under the License.
		 */
		-->
		<configuration>
		  <property>
		    <name>hbase.cluster.distributed</name>
		    <value>true</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.quorum</name>
		    <value>zookeeper0,zookeeper1,zookeeper2</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.property.clientPort</name>
		    <value>2181</value>
		  </property>
		</configuration>

	Этот файл будет использоваться для загрузки конфигурации HBase для кластера HDInsight.

	> [WACOM.NOTE] Это минимально возможный файл hbase-site.xml, содержащий лишь самые минимальные настройки для кластера HDInsight. Чтобы найти полную версию файла конфигурации hbase-site.xml, используемого HDInsight, [подключитесь к кластеру HDInsight с помощью удаленного рабочего стола](http://azure.microsoft.com/ru-ru/documentation/articles/hdinsight-administer-use-management-portal/#rdp), и файл hbase-site.xml будет находиться в каталоге C:\apps\dist\hbase-&lt;номер версии>-hadoop2\conf. Часть пути с номером версии изменяется при обновлении версии HBase в кластере.

3. Сохраните файл hbase-site.xml.

##Создание приложения

1. Перейдите в каталог hbaseapp\src\main\java\com\microsoft\examples и переименуйте файл app.java в CreateTable.java.

2. Откройте файл CreateTable.java и замените существующее содержимое следующим:

		package com.microsoft.examples;
		import java.io.IOException;
		
		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HBaseAdmin;
		import org.apache.hadoop.hbase.HTableDescriptor;
		import org.apache.hadoop.hbase.TableName;
		import org.apache.hadoop.hbase.HColumnDescriptor;
		import org.apache.hadoop.hbase.client.HTable;
		import org.apache.hadoop.hbase.client.Put;
		import org.apache.hadoop.hbase.util.Bytes;
		
		public class CreateTable {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();
		
		    // Example of setting zookeeper values for HDInsight
			//   in code instead of an hbase-site.xml file
			//
		    // config.set("hbase.zookeeper.quorum",
		    //            "zookeepernode0,zookeepernode1,zookeepernode2");
		    //config.set("hbase.zookeeper.property.clientPort", "2181");
		    //config.set("hbase.cluster.distributed", "true");
		
		    // create an admin object using the config
		    HBaseAdmin admin = new HBaseAdmin(config);
		
		    // create the table...
		    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
		    // ... with two column families
		    tableDescriptor.addFamily(new HColumnDescriptor("name"));
		    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
		    admin.createTable(tableDescriptor);
		
		    // define some people
		    String[][] people = {
		        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
		        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
		        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
		        { "4", "Rae", "Schroeder", "rae@contoso.com" },
		        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
		        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };
		
		    HTable table = new HTable(config, "people");
		
		    // Add each person to the table
		    //   Use the `name` column family for the name
		    //   Use the `contactinfo` column family for the email
		    for (int i = 0; i< people.length; i++) {
		      Put person = new Put(Bytes.toBytes(people[i][0]));
		      person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
		      person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
		      person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
		      table.put(person);
		    }
		    // flush commits and close the table
		    table.flushCommits();
		    table.close();
		  }
		}

	Это класс CreateTable, который создает таблицу с именем people и заполняет ее некими заранее определенными пользователями.

3. Сохраните файл CreateTable.java.

4. В каталоге hbaseapp\src\main\java\com\microsoft\examples создайте новый файл с именем SearchByEmail.java. Используйте следующее в качестве содержимого этого файла:

		package com.microsoft.examples;
		import java.io.IOException;
		
		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HTable;
		import org.apache.hadoop.hbase.client.Scan;
		import org.apache.hadoop.hbase.client.ResultScanner;
		import org.apache.hadoop.hbase.client.Result;
		import org.apache.hadoop.hbase.filter.RegexStringComparator;
		import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
		import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
		import org.apache.hadoop.hbase.util.Bytes;
		import org.apache.hadoop.util.GenericOptionsParser;
		
		public class SearchByEmail {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();
		
		    // Use GenericOptionsParser to get only the parameters to the class
		    // and not all the parameters passed (when using WebHCat for example)
		    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
		    if (otherArgs.length != 1) {
		      System.out.println("usage: [regular expression]");
		      System.exit(-1);
		    }
			
			// Open the table
		    HTable table = new HTable(config, "people");
		
			// Define the family and qualifiers to be used
		    byte[] contactFamily = Bytes.toBytes("contactinfo");
		    byte[] emailQualifier = Bytes.toBytes("email");
		    byte[] nameFamily = Bytes.toBytes("name");
		    byte[] firstNameQualifier = Bytes.toBytes("first");
		    byte[] lastNameQualifier = Bytes.toBytes("last");
		
			// Create a new regex filter
		    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
			// Attach the regex filter to a filter
			//   for the email column
		    SingleColumnValueFilter filter = new SingleColumnValueFilter(
		      contactFamily,
		      emailQualifier,
		      CompareOp.EQUAL,
		      emailFilter
		    );
		
			// Create a scan and set the filter
		    Scan scan = new Scan();
		    scan.setFilter(filter);
		
			// Get the results
		    ResultScanner results = table.getScanner(scan);
			// Iterate over results and print  values
		    for (Result result : results ) {
		      String id = new String(result.getRow());
		      byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
		      String firstName = new String(firstNameObj);
		      byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
		      String lastName = new String(lastNameObj);
		      System.out.println(firstName + " " + lastName + " - ID: " + id);
			  byte[] emailObj = result.getValue(contactFamily, emailQualifier);
		      String email = new String(emailObj);
			  System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
		    }
		    results.close();
			table.close();
		  }
		}

	Класс SearchByEmail можно использовать для запроса строк по адресу электронной почты. При использовании класса можно задавать либо строку, либо регулярное выражение, так как используется фильтр регулярных выражений.

5. Сохраните файл SearchByEmail.java.

6. В каталоге hbaseapp\src\main\hava\com\microsoft\examples создайте новый файл с именем DeleteTable.java. Используйте следующее в качестве содержимого этого файла:

		package com.microsoft.examples;
		import java.io.IOException;
		
		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HBaseAdmin;
		
		public class DeleteTable {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();
		
		    // Create an admin object using the config
		    HBaseAdmin admin = new HBaseAdmin(config);
		
		    // Disable, and then delete the table
		    admin.disableTable("people");
		    admin.deleteTable("people");
		  }
		}

	Этот класс предназначен лишь для очистки данного примера путем отключения и удаления таблицы, созданной классом CreateTable.

7. Сохраните файл DeleteTable.java.

##Сборка и создание пакета приложения

1. Откройте командную строку и измените каталоги на каталог hbaseapp.

2. Выполните следующую команду, чтобы собрать файл JAR, содержащий приложение.

		mvn clean package

	При этом будут удалены остатки предыдущих сборок, загружены все неустановленные на текущий момент зависимости, затем будет произведена сборка и создание пакета приложения.

3. После завершения выполнения команды в каталоге hbaseapp\target будет находиться файл с именем hbaseapp-1.0-SNAPSHOT.jar.

	> [WACOM.NOTE] Файл hbaseapp-1.0-SNAPSHOT.jar является так называемым "uberjar" (иногда также называется fatjar), он содержит все требуемые приложением зависимости.

##Загрузка JAR и запуск задания

> [WACOM.NOTE] Существует множество способов загрузки файла в ваш кластер HDInsight, они описаны в документе [Отправка данных для заданий Hadoop в HDInsight](/ru-ru/documentation/articles/hdinsight-upload-data/). Для описанных ниже действий используется [Azure PowerShell](/ru-ru/documentation/articles/install-configure-powershell/).

1. После установки и настройки [Azure PowerShell](/ru-ru/documentation/articles/install-configure-powershell/)создайте новый файл с именем hbase-runner.psm1. Используйте следующее в качестве содержимого этого файла:

		<#
		.SYNOPSIS
		    Copies a file to the primary storage of an HDInsight cluster.
		.DESCRIPTION
		    Copies a file from a local directory to the blob container for
		    the HDInsight cluster.
		.EXAMPLE
		    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
		        -clusterName "MyHDInsightCluster"
		
		.EXAMPLE
		    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
		        -clusterName "MyHDInsightCluster"
		        -emailRegex "contoso.com"
		
		.EXAMPLE
		    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
		        -clusterName "MyHDInsightCluster"
		        -emailRegex "^r" -showErr
		#>
		
		function Start-HBaseExample {
		    [CmdletBinding(SupportsShouldProcess = $true)]
		    param(
		        #The class to run
		        [Parameter(Mandatory = $true)]
		        [String]$className,
		
		        #The name of the HDInsight cluster
		        [Parameter(Mandatory = $true)]
		        [String]$clusterName,
		
		        #Only used when using SearchByEmail
		        [Parameter(Mandatory = $false)]
		        [String]$emailRegex,
		
		        #Use if you want to see stderr output
		        [Parameter(Mandatory = $false)]
		        [Switch]$showErr
		    )
		
		    Set-StrictMode -Version 3
		
		    # Is the Azure module installed?
		    FindAzure
		
		    # The JAR
		    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
		
		    # The job definition
		    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
		      -JarFile $jarFile `
		      -ClassName $className `
		      -Arguments $emailRegex
		
		    # Get the job output
		    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		    Wait-AzureHDInsightJob -Job $job
		    if($showErr)
		    {
		        Write-Host "STDERR"
		        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		    }
		    Write-Host "Display the standard output ..." -ForegroundColor Green
		    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
		}
		
		<#
		.SYNOPSIS
		    Copies a file to the primary storage of an HDInsight cluster.
		.DESCRIPTION
		    Copies a file from a local directory to the blob container for
		    the HDInsight cluster.
		.EXAMPLE
		    Add-HDInsightFile -localPath "C:\temp\data.txt"
		        -destinationPath "example/data/data.txt"
		        -ClusterName "MyHDInsightCluster"
		.EXAMPLE
		    Add-HDInsightFile -localPath "C:\temp\data.txt"
		        -destinationPath "example/data/data.txt"
		        -ClusterName "MyHDInsightCluster"
		        -Container "MyContainer"
		#>
		
		function Add-HDInsightFile {
		    [CmdletBinding(SupportsShouldProcess = $true)]
		    param(
		        #The path to the local file.
		        [Parameter(Mandatory = $true)]
		        [String]$localPath,
		
		        #The destination path and file name, relative to the root of the container.
		        [Parameter(Mandatory = $true)]
		        [String]$destinationPath,
		
		        #The name of the HDInsight cluster
		        [Parameter(Mandatory = $true)]
		        [String]$clusterName,
		
		        #If specified, overwrites existing files without prompting
		        [Parameter(Mandatory = $false)]
		        [Switch]$force
		    )
		
		    Set-StrictMode -Version 3
		
		    # Is the Azure module installed?
		    FindAzure
		
		    # Does the local path exist?
		    if (-not (Test-Path $localPath))
		    {
		        throw "Source path '$localPath' does not exist."
		    }
		
		    # Get the primary storage container
		    $storage = GetStorage -clusterName $clusterName
		
		    # Upload file to storage, overwriting existing files if -force was used.
		    Set-AzureStorageBlobContent -File $localPath -Blob $destinationPath -force:$force `
		                                -Container $storage.container `
		                                -Context $storage.context
		}
		
		function FindAzure {
		    # Is the Azure module installed?
		    if (-not(Get-Module -ListAvailable Azure))
		    {
		        throw "Windows Azure PowerShell not found! For help, see http://www.windowsazure.com/ru-ru/documentation/articles/install-configure-powershell/"
		    }
		
		    # Is there an active Azure subscription?
		    $sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		    if(-not($sub))
		    {
		        throw "No active Azure subscription found! If you have a subscription, use the Add-AzureAccount or Import-PublishSettingsFile cmdlets to make the Azure account available to Windows PowerShell."
		    }
		}
		
		function GetStorage {
		    param(
		        [Parameter(Mandatory = $true)]
		        [String]$clusterName
		    )
		    $hdi = Get-AzureHDInsightCluster -name $clusterName
		    # Does the cluster exist?
		    if (!$hdi)
		    {
		        throw "HDInsight cluster '$clusterName' does not exist."
		    }
		    # Create a return object for context & container
		    $return = @{}
		    $storageAccounts = @{}
		    # Get the primary storage account information
		    $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
		    $storageAccountKey = $hdi.DefaultStorageAccount.StorageAccountKey
		    # Build the hash of storage account name/keys
		    $storageAccounts.Add($hdi.DefaultStorageAccount.StorageAccountName, $storageAccountKey)
		    foreach($account in $hdi.StorageAccounts)
		    {
		        $storageAccounts.Add($account.StorageAccountName, $account.StorageAccountKey)
		    }
		    # Get the storage context, as we can't depend
		    # on using the default storage context
		    $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		    # Get the container, so we know where to
		    # find/store blobs
		    $return.container = $hdi.DefaultStorageAccount.StorageContainerName
		    # Return storage accounts to support finding all accounts for
		    # a cluster
		    $return.storageAccounts = $storageAccounts
		
		    return $return
		}
		# Only export the verb-phrase things
		export-modulemember *-*

	Этот файл содержит два модуля:

	* Add-HDInsightFile - используется для передачи файлов в HDInsight;

	* Start-HBaseExample - используется для запуска классов, созданных ранее.

2. Сохраните файл hbase-runner.psm1.

3. Откройте окно Azure PowerShell, измените каталоги на каталог hbaseapp, затем выполните следующую команду.

		PS C:\ Import-Module c:\path\to\hbase-runner.psm1

	Измените путь на место расположения созданного ранее файла hbase-runner.psm1. При этой модуль будет зарегистрирован для текущего сеанса PowerShell.

2. Воспользуйтесь следующей командой, чтобы скачать hbaseapp-1.0-SNAPSHOT.jar на ваш кластер HDInsight.

		Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

	Замените hdinsightclustername на имя своего кластера HDInsight. Затем команда передаст hbaseapp-1.0-SNAPSHOT.jar в каталог example/jars, расположенный в основном хранилище для вашего кластера HDInsight.

3. После передачи файлов используете следующую команду для создания новой таблицы с hbaseapp.

		Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

	Замените hdinsightclustername на имя своего кластера HDInsight.

	Эта команда создаст новую таблицу с именем people в кластере HDInsight. Эта команда не отображает какие-либо выходные данные в окне консоли.

2. Для осуществления поиска по записям таблицы используйте следующую команду.

		Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

	Замените hdinsightclustername на имя своего кластера HDInsight.

	Будет использован класс SearchByEmail для поиска всех строк, в которых семейство столбцов contactinformation, столбец email, содержит строку contoso.com. Вы получите следующие результаты:

		Franklin Holtz - ID: 2
		Franklin Holtz - franklin@contoso.com - ID: 2
		Rae Schroeder - ID: 4
		Rae Schroeder - rae@contoso.com - ID: 4
		Gabriela Ingram - ID: 6
		Gabriela Ingram - gabriela@contoso.com - ID: 6

	Использование fabrikam.com для значения -emailRegex вернет список пользователей, у которых имеется строка fabrikam.com в поле электронной почты. Так как поиск реализован на базе фильтра регулярных выражений, также можно использовать регулярные выражения, например ^r, в этом случае будут возвращены записи, у которых электронная почта начинается с буквы "r".

##Удаление таблицы

После завершения работы с примером используйте следующую команду в сеансе PowerShell для удаления таблицы people, использовавшейся в данном примере.

	Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Замените hdinsightclustername на имя своего кластера HDInsight.

##Устранение неполадок

###При использовании Start-HBaseExample результаты отсутствуют или не получено каких-либо неожиданных результатов.

Используйте параметр -showErr для просмотра STDERR, выдаваемого при выполнении задания.

<!--HONumber=35.1-->
