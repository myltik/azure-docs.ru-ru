<properties
	pageTitle="Создание приложения HBase с помощью Maven или Java с последующим развертыванием в HDInsight на основе Linux | Microsoft Azure"
	description="Узнайте, как использовать Apache Maven для создания приложения Java Apache HBase и его последующего развертывания в кластере HDInsight под управлением Linux в облаке Azure."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="larryfr"/>

#Использование Maven для выполнения сборки приложений Java, которые используют HBase с HDInsight (Hadoop) под управлением Linux

Вы узнаете, как создать приложение [Apache HBase](http://hbase.apache.org/) на Java и выполнить его сборку с использованием Apache Maven. Затем используйте приложение с кластером HDInsight на платформе Linux.

[Maven](http://maven.apache.org/) — это инструмент для управления и повышения обозримости проектов программного обеспечения, позволяющее создавать ПО, документацию и отчеты для проектов Java. Из данной статьи вы узнаете, как использовать его для создания базового приложения Java, которое создает, запрашивает и удаляет таблицу HBase в кластере HDInsight под управлением Linux.

> [AZURE.NOTE] В этом документе предполагается, что вы используете кластер HDInsight под управлением Linux. Сведения об использовании кластера HDInsight под управлением Windows см. в разделе [Использование Maven для выполнения сборки приложений Java, которые используют HBase с HDInsight (Hadoop) под управлением Windows](hdinsight-hbase-build-java-maven.md).

##Требования

* [Пакет JDK для платформы Java](http://www.oracle.com/technetwork/java/javase/downloads/index.html) версии 7 или более поздней.

* [Maven](http://maven.apache.org/)

* [Кластер Azure HDInsight под управлением Linux с HBase.](../hdinsight-hbase-get-started-linux.md#create-hbase-cluster)

    > [AZURE.NOTE] Действия, описанные в этом документе, были проверены для версий кластера HDInsight 3.2, 3.3 и 3.4. Значения по умолчанию в примерах предназначены для кластера HDInsight 3.4.

* **Знакомство с SSH и SCP**. Дополнительные сведения об использовании SSH и SCP с HDInsight можно найти в следующих разделах:

    * **Для клиентов Linux, Unix или OS X**: См. [Использование SSH в Hadoop на HDInsight под управлением Linux из Linux, OS X или Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Для клиентов Windows**: См. [Использование SSH в Hadoop на HDInsight под управлением Linux из Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##Создание проекта

1. Из командной строки вашей среды разработки измените каталоги на расположение, где вы хотите создать проект. Например, `cd code/hdinsight`.

2. Используйте команду __mvn__, которая будет установлена вместе с Maven, для создания шаблона проекта.

		mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	При этом в текущем каталоге будет создан новый каталог с именем, указанным в параметре __artifactID__ (в нашем случае **hbaseapp**). В этом каталоге будут находиться следующие элементы:

	* __pom.xml__ — это модель объекта проекта ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)), которая содержит информацию и подробности конфигурации, использующиеся при сборке проекта;

	* __src__ — каталог, в котором содержится каталог __main/java/com/microsoft/examples__, в котором будет создаваться приложение.

3. Удалите файл __src/test/java/com/microsoft/examples/apptest.java__, так как он не используется в этом примере.

##Обновление модели объекта проекта

1. Откройте для редактирования файл __pom.xml__ и добавьте следующий код в раздел `<dependencies>`:

		<dependency>
      	  <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

	Они указывают Maven, что для проекта требуется __hbase-client__ версии __1.1.2__. При компиляции он будет загружено из репозитория Maven по умолчанию. Можно воспользоваться [поиском в центральном репозитории Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar), чтобы получить дополнительную информацию об этой зависимости.

    > [AZURE.IMPORTANT] Номер версии должен соответствовать версии HBase, которая поставляется с кластером HDInsight. Воспользуйтесь следующей таблицей, чтобы найти правильный номер версии.

    | Версия кластера HDInsight | Используемая версия HBase |
    | ----- | ----- |
    | 3\.2 | 0\.98.4-hadoop2 |
    | 3\.3 и 3.4 | 1\.1.2 |

    Дополнительные сведения о версиях и компонентах HDInsight см. в статье [Что представляют собой различные компоненты Hadoop, доступные в HDInsight?](hdinsight-component-versioning.md)

2. При использовании кластера HDInsight 3.3 или 3.4 необходимо также добавить в раздел `<dependencies>` следующий код.

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Он загрузит компоненты phoenix-core, которые необходимы для версии Hbase 1.1.x.

2. Добавьте в файл __pom.xml__ следующий код. Эти строки должны находиться в файле внутри тегов `<project>...</project>`; например, между тегами `</dependencies>` и `</project>`.

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
          	    <source>1.7</source>
          	    <target>1.7</target>
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

	При этом настраивается ресурс (__conf/hbase-site.xml__), который содержит информацию о конфигурации для HBase.

	> [AZURE.NOTE] Также можно настроить значения конфигурации непосредственно из кода. См. комментарии к примеру __CreateTable__ ниже, чтобы узнать, как это сделать.

	Также будут настроены подключаемые модули [компилятора Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) и [Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/). Подключаемый модуль компилятора используется для компиляции топологии. Подключаемый модуль shade используется для предотвращения дублирования лицензии в JAR-файле, собранном Maven. Причина — дублирующиеся файлы лицензий вызывают ошибку выполнения на кластере HDInsight. Использование maven-shade-plugin с реализацией `ApacheLicenseResourceTransformer` предотвращает возникновение этой ошибки.

	maven-shade-plugin также создает так называемый «uber jar» (или «fat jar»), который содержит все зависимости, требуемые для приложения.

3. Сохраните файл __pom.xml__.

4. Создайте новый каталог с именем __conf__ в каталоге __hbaseapp__. Он будет использоваться для хранения сведений о конфигурации для подключения к HBase.

5. Для копирования конфигурации HBase из сервера HDInsight в каталог __conf__ используйте следующую команду. Замените **USERNAME** на имя пользователя SSH. Замените **CLUSTERNAME** на имя кластера HDInsight.

		scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

	> [AZURE.NOTE] Если для учетной записи SSH используется пароль, будет предложено его ввести. Если для учетной записи SSH используется ключ, возможно, вам потребуется использовать параметр `-i`, чтобы указать путь к файлу ключа. В следующем примере выполняется загрузка закрытого ключа из файла `~/.ssh/id_rsa`:
	>
	> `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##Создание приложения

1. Перейдите в каталог __hbaseapp/src/main/java/com/microsoft/examples__ и переименуйте файл app.java в __CreateTable.java__.

2. Откройте файл __CreateTable.java__ и замените существующее содержимое следующим:

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

	Это класс __CreateTable__, который создает таблицу с именем __people__ и заполняет ее некими заранее определенными пользователями.

3. Сохраните файл __CreateTable.java__.

4. В каталоге __hbaseapp/src/main/java/com/microsoft/examples__ создайте новый файл с именем __SearchByEmail.java__. Используйте следующее в качестве содержимого этого файла:

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

	Класс __SearchByEmail__ можно использовать для запроса строк по адресу электронной почты. При использовании класса можно задавать либо строку, либо регулярное выражение, так как используется фильтр регулярных выражений.

5. Сохраните файл __SearchByEmail.java__.

6. В каталоге __hbaseapp/src/main/hava/com/microsoft/examples__ создайте новый файл с именем __DeleteTable.java__. Используйте следующее в качестве содержимого этого файла:

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

	Этот класс предназначен лишь для очистки данного примера путем отключения и удаления таблицы, созданной классом __CreateTable__.

7. Сохраните файл __DeleteTable.java__.

##Сборка и создание пакета приложения

2. Выполните следующую команду из каталога __hbaseapp__, чтобы собрать JAR-файл, содержащий приложение:

		mvn clean package

	При этом будут удалены остатки предыдущих сборок, скачаны все неустановленные на текущий момент зависимости, затем будет произведена сборка и создание пакета приложения.

3. После завершения выполнения команды в каталоге __hbaseapp/target__ будет находиться файл с именем __hbaseapp-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE] Файл __hbaseapp-1.0-SNAPSHOT.jar__ относится к типу «uber jar» (другое название — «fat jar») и содержит все зависимости, необходимые для работы приложения.

##Передача JAR-файла и запуск заданий

1. Воспользуйтесь следующей командой, чтобы загрузить JAR-файл в кластер HDInsight: Замените **USERNAME** на имя пользователя SSH. Замените **CLUSTERNAME** на имя кластера HDInsight.

		scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

	Эта команда скачает файл в домашний каталог вашей учетной записи пользователя SSH.

	> [AZURE.NOTE] Если для учетной записи SSH используется пароль, будет предложено его ввести. Если для учетной записи SSH используется ключ, возможно, вам потребуется использовать параметр `-i`, чтобы указать путь к файлу ключа. В следующем примере выполняется загрузка закрытого ключа из файла `~/.ssh/id_rsa`:
	>
	> `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. Подключитесь к кластеру HDInsight с помощью SSH. Замените **USERNAME** на имя пользователя SSH. Замените **CLUSTERNAME** именем кластера HDInsight.

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	> [AZURE.NOTE] Если для учетной записи SSH используется пароль, будет предложено его ввести. Если для учетной записи SSH используется ключ, возможно, вам потребуется использовать параметр `-i`, чтобы указать путь к файлу ключа. В следующем примере выполняется загрузка закрытого ключа из файла `~/.ssh/id_rsa`:
	>
	> `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. После подключения используйте следующую команду, чтобы создать новую таблицу HBase с помощью приложения Java:

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

	Эта команда создаст новую таблицу HBase с именем __people__ и заполнит ее данными.

4. Затем воспользуйтесь следующей командой для поиска адресов электронной почты, хранящихся в этой таблице:

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

	Вы получите следующие результаты:

		Franklin Holtz - ID: 2
		Franklin Holtz - franklin@contoso.com - ID: 2
		Rae Schroeder - ID: 4
		Rae Schroeder - rae@contoso.com - ID: 4
		Gabriela Ingram - ID: 6
		Gabriela Ingram - gabriela@contoso.com - ID: 6

##Удаление таблицы

После завершения работы с примером используйте следующую команду в сеансе Azure PowerShell для удаления таблицы __people__, использовавшейся в данном примере:

	hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable

<!---HONumber=AcomDC_0706_2016-->