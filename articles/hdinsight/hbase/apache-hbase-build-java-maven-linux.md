---
title: Клиент Java HBase для Azure HDInsight | Документация Майкрософт
description: Сведения об использовании Apache Maven для создания приложения Java для Apache HBase и его последующем развертывании в HBase в Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: ''
ms.assetid: 1d1ed180-e0f4-4d1c-b5ea-72e0eda643bc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: larryfr
ms.openlocfilehash: 564bfb9c80fe835bd5defb4321607e0970c9f0aa
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311494"
---
# <a name="build-java-applications-for-apache-hbase"></a>Создание приложений Java для Apache HBase

Узнайте, как создать приложение [Apache HBase](http://hbase.apache.org/) в среде Java. Затем вы будете использовать приложение с HBase в Azure HDInsight.

В этом руководстве используется [Maven](http://maven.apache.org/) для создания и сборки проекта. Maven — это инструмент для управления и повышения обозримости проектов программного обеспечения, позволяющий создавать ПО, документацию и отчеты для проектов Java.

> [!NOTE]
> Действия, описанные в этом документе, в последний раз были протестированы с помощью HDInsight 3.6.

> [!IMPORTANT]
> Для выполнения действий, описанных в этом документе, необходим кластер HDInsight под управлением Linux. Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="requirements"></a>Требования

* [Пакет JDK для платформы Java](http://www.oracle.com/technetwork/java/javase/downloads/index.html) версии 8 или более поздней.

    > [!NOTE]
    > Для HDInsight 3.5 и более поздних версий требуется Java 8. Для работы более ранних версий HDInsight требуется Java 7.

* [Maven](http://maven.apache.org/)

* [Кластер Azure HDInsight под управлением Linux с HBase.](apache-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

## <a name="create-the-project"></a>Создание проекта

1. Из командной строки вашей среды разработки измените каталоги на расположение, где вы хотите создать проект. Например, `cd code\hbase`.

2. Используйте команду **mvn** , которая будет установлена вместе с Maven, для создания шаблона проекта.

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [!NOTE]
    > При использовании PowerShell параметры `-D` необходимо заключить в кавычки.
    >
    > `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=hbaseapp" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    При этом будет создан каталог, имя которого будет совпадать с именем параметра **artifactID** (в нашем случае **hbaseapp**). Этот каталог содержит следующие элементы:

   * **pom.xml** — это модель объекта проекта ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)), которая содержит информацию и подробности конфигурации, учитывающиеся при сборке проекта;
   * **src** — каталог, содержащий каталог **main/java/com/microsoft/examples**, в котором создается приложение.

3. Удалите файл `src/test/java/com/microsoft/examples/apptest.java`. Он не используется в этом примере.

## <a name="update-the-project-object-model"></a>Обновление модели объекта проекта

1. Измените файл `pom.xml`, добавив в раздел `<dependencies>` следующий код:

   ```xml
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-client</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.phoenix</groupId>
        <artifactId>phoenix-core</artifactId>
        <version>4.4.0-HBase-1.1</version>
    </dependency>
   ```

    В этом разделе показано, что для проекта требуются компоненты **hbase-client** и **phoenix-core**. При компиляции эти зависимости скачиваются из репозитория Maven по умолчанию. Можно воспользоваться [поиском в центральном репозитории Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) , чтобы получить дополнительную информацию об этой зависимости.

   > [!IMPORTANT]
   > Номер версии hbase-client должен соответствовать версии HBase, которая поставляется с кластером HDInsight. Воспользуйтесь следующей таблицей, чтобы найти правильный номер версии.

   | Версия кластера HDInsight | Используемая версия HBase |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3, 3.4, 3.5 и 3.6 |1.1.2 |

    Дополнительные сведения о версиях и компонентах HDInsight см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](../hdinsight-component-versioning.md)

3. Добавьте в файл **pom.xml** следующий код. Эти строки должны находиться в файле внутри тегов `<project>...</project>` (например, между тегами `</dependencies>` и `</project>`).

   ```xml
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
            <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
            </plugin>
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
   ```

    В этом разделе настраивается ресурс (`conf/hbase-site.xml`), который содержит информацию о конфигурации для HBase.

   > [!NOTE]
   > Также можно настроить значения конфигурации непосредственно из кода. Ознакомьтесь с комментариями к примеру `CreateTable`.

    В разделе также будут настроены подключаемые модули [компилятора Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) и[Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/). Подключаемый модуль компилятора используется для компиляции топологии. Подключаемый модуль shade используется для предотвращения дублирования лицензии в JAR-файле, собранном Maven. Этот подключаемый модуль используется для предотвращения ошибки с дублированием файлов лицензий, которая появляется во время выполнения на кластере HDInsight. Использование maven-shade-plugin с реализацией `ApacheLicenseResourceTransformer` позволяет избежать этой ошибки.

    maven-shade-plugin также создает так называемый uber jar, который содержит все зависимости, требуемые для приложения.

4. Сохраните файл `pom.xml`.

5. Создайте каталог с именем `conf` в каталоге `hbaseapp`. Этот каталог будет использоваться для хранения сведений о конфигурации для подключения к HBase.

6. Для копирования конфигурации HBase из кластера HBase в каталог `conf` используйте следующую команду. Замените `USERNAME` именем пользователя SSH, а `CLUSTERNAME` — именем кластера HDInsight:

    ```bash
    scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
    ```

   Дополнительные сведения об использовании `ssh` и `scp` см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-application"></a>Создание приложения

1. Перейдите к каталогу `hbaseapp/src/main/java/com/microsoft/examples` и переименуйте файл app.java в `CreateTable.java`.

2. Откройте файл `CreateTable.java` и замените имеющееся содержимое следующим текстом:

   ```java
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
        // in code instead of an hbase-site.xml file
        //
        // config.set("hbase.zookeeper.quorum",
        //            "zookeepernode0,zookeepernode1,zookeepernode2");
        //config.set("hbase.zookeeper.property.clientPort", "2181");
        //config.set("hbase.cluster.distributed", "true");
        //
        //NOTE: Actual zookeeper host names can be found using Ambari:
        //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

        //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
        config.set("zookeeper.znode.parent","/hbase-unsecure");

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
   ```

    Это код класса **CreateTable**, который создает таблицу с именем **people** и заполняет ее заранее определенными пользователями.

3. Сохраните файл `CreateTable.java`.

4. В каталоге `hbaseapp/src/main/java/com/microsoft/examples` создайте файл с именем `SearchByEmail.java`. В качестве содержимого файла добавьте следующий текст:

   ```java
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

        // Create a regex filter
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
   ```

    Класс **SearchByEmail** можно использовать для запроса строк по адресу электронной почты. При использовании класса можно задавать либо строку, либо регулярное выражение, так как используется фильтр регулярных выражений.

5. Сохраните файл `SearchByEmail.java`.

6. В каталоге `hbaseapp/src/main/hava/com/microsoft/examples` создайте файл с именем `DeleteTable.java`. В качестве содержимого файла добавьте следующий текст:

   ```java
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
   ```

    Этот класс предназначен, только чтобы очистить таблицы HBase, созданные в данном примере, отключив и удалив таблицу, созданную классом `CreateTable`.

7. Сохраните файл `DeleteTable.java`.

## <a name="build-and-package-the-application"></a>Сборка и создание пакета приложения

1. Выполните следующую команду из каталога `hbaseapp`, чтобы собрать JAR-файл, содержащий приложение:

    ```bash
    mvn clean package
    ```

    Эта команда создает и упаковывает приложение в JAR-файл.

2. После выполнения команды каталог `hbaseapp/target` будет содержать файл с именем `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]
   > Файл `hbaseapp-1.0-SNAPSHOT.jar` относится к типу uber jar. Он содержит все зависимости, необходимые для запуска приложения.


## <a name="upload-the-jar-and-run-jobs-ssh"></a>Передача JAR-файла и запуск заданий (SSH)

В следующих действиях используется команда `scp` для копирования JAR-файла в головной узел HBase в кластере HDInsight. С помощью команды `ssh` выполняется подключение к кластеру; пример запускается непосредственно на головном узле.

1. Чтобы отправить JAR-файл в кластер, используйте следующую команду:

    ```bash
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

    Замените `USERNAME` именем пользователя SSH, а `CLUSTERNAME` — именем кластера HDInsight.

2. Чтобы подключиться к кластеру HBase, используйте следующую команду:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Замените `USERNAME` именем для входа SSH, а `CLUSTERNAME` — именем кластера HDInsight.

3. Чтобы создать таблицу HBase с помощью приложения Java, воспользуйтесь следующей командой:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Эта команда создает таблицу HBase с именем **people** и заполняет ее данными.

4. Для поиска адресов электронной почты, хранящихся в этой таблице, воспользуйтесь следующей командой:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Вы получите следующие результаты:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. Чтобы удалить таблицу, используйте следующую команду:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Передача JAR-файла и запуск заданий (PowerShell)

Далее используется Azure PowerShell для передачи JAR-файла в хранилище по умолчанию для кластера HBase. Затем командлеты HDInsight используются для удаленного запуска примеров.

1. После установки и настройки Azure PowerShell создайте файл с именем `hbase-runner.psm1`. В качестве содержимого файла добавьте следующий текст:

   ```powershell
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

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
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

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzureStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            throw "No active Azure subscription found! If you have a subscription, use the Connect-AzureRmAccount cmdlet to login to your subscription."
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Этот файл содержит два модуля:

   * **Add-HDInsightFile** — используется для загрузки файлов в кластер;
   * **Start-HBaseExample** — используется для запуска классов, созданных ранее.

2. Сохраните файл `hbase-runner.psm1`.

3. Откройте окно Azure PowerShell, измените каталоги на каталог `hbaseapp`, а затем выполните следующую команду:

    ```powershell
    PS C:\ Import-Module c:\path\to\hbase-runner.psm1
    ```

    Измените путь на место расположения созданного ранее файла `hbase-runner.psm1`. Эта команда регистрирует модуль в Azure PowerShell.

4. Воспользуйтесь следующей командой, чтобы отправить файл `hbaseapp-1.0-SNAPSHOT.jar` в ваш кластер.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername
    ```

    Замените `hdinsightclustername` именем кластера. При появлении запроса введите имя для входа и пароль администратора кластера. Команда отправляет файл `hbaseapp-1.0-SNAPSHOT.jar` в расположение `example/jars` в главном хранилище кластера.

5. Чтобы создать таблицу с помощью `hbaseapp`, используйте следующую команду:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername
    ```

    Замените `hdinsightclustername` именем кластера. При появлении запроса введите имя для входа и пароль администратора кластера.

    Эта команда создает таблицу с именем **people** в HBase в кластере HDInsight. Эта команда не отображает какие-либо выходные данные в окне консоли.

6. Для осуществления поиска записей таблицы используйте следующую команду:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com
    ```

    Замените `hdinsightclustername` именем кластера. При появлении запроса введите имя для входа и пароль администратора кластера.

    Будет использован класс `SearchByEmail` для поиска всех строк, у которых значение семейства столбцов `contactinformation` и столбца `email` содержит строку `contoso.com`. Вы получите следующие результаты:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Использование **fabrikam.com** для значения `-emailRegex` вернет список пользователей, у которых имеется строка **fabrikam.com** в поле электронного адреса. В качестве поискового запроса также можно использовать регулярные выражения. Например, **^r** возвращает адреса электронной почты, начинающиеся с буквы r.

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>При использовании Start-HBaseExample результаты отсутствуют или не получено каких-либо неожиданных результатов.

Используйте параметр `-showErr` для просмотра стандартной ошибки (STDERR), выдаваемой при выполнении задания.

## <a name="delete-the-table"></a>Удаление таблицы

Завершив работу с примером, удалите таблицу **people**, используя следующую команду в сеансе Azure PowerShell.

__Из сеанса `ssh`__ :

`yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable`

__Из Azure PowerShell__:

`Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername`

## <a name="next-steps"></a>Дополнительная информация

[Использование Apache Phoenix с кластерами HBase под управлением Linux в HDInsight](apache-hbase-phoenix-squirrel-linux.md)
