<properties
 pageTitle="Использование JDBC для запроса Hive в Azure HDInsight"
 description="Узнайте, как использовать JDBC для подключения к Hive в Azure HDInsight и удаленного выполнения запросов данных, хранящихся в облаке."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="07/17/2015"
 ms.author="larryfr"/>

#Подключение к Hive в Azure HDInsight с помощью драйвера Hive JDBC

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

В этом документе рассматривается использование JDBC из приложения Java для удаленной отправки запросов Hive в кластер HDInsight. Дополнительные сведения об интерфейсе JDBC Hive см. в разделе [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

##Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Hadoop в кластере HDInsight. Будут работать кластеры либо под управлением Linux, либо под управлением Windows.

* [Java Developer Kit (JDK) версии 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) или более поздней версии.

* [Apache Maven](https://maven.apache.org). Maven — система сборки для проектов Java, используемая в проекте, связанном с данной статьей.

##Строка подключения

Подключения JDBC к кластеру HDInsight в Azure осуществляются через порт 443, а защита трафика обеспечивается с помощью протокола SSL. Открытый шлюз, за которым находятся кластеры, перенаправляет трафик к порту, который фактически прослушивается HiveServer2. Поэтому обычная строка подключения будет следующей:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

##Аутентификация

При установке подключения необходимо указать имя администратора кластера HDInsight и пароль. Это проверка подлинности запроса к шлюзу. Например, приведенный ниже код Java открывает новое подключение с помощью строки подключения, имени администратора и пароля:

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##Запросы

После установки подключения можно выполнять запросы к Hive. Например, приведенный ниже код Java выполняет команду __SELECT__ из таблицы, ограничивая результаты только тремя строками, а затем отображает результаты:

    sql = "SELECT querytime, market, deviceplatform, devicemodel, state, country from " + tableName + " LIMIT 3";
    stmt2 = conn.createStatement();
    System.out.println("\nRetrieving inserted data:");

    res2 = stmt2.executeQuery(sql);

    while (res2.next()) {
      System.out.println( res2.getString(1) + "\t" + res2.getString(2) + "\t" + res2.getString(3) + "\t" + res2.getString(4) + "\t" + res2.getString(5) + "\t" + res2.getString(6));
    }

##Пример проекта Java

Пример использования клиента Java для запроса Hive в HDInsight доступен на странице [https://github.com/Blackmist/hdinsight-hive-jdbc](https://github.com/Blackmist/hdinsight-hive-jdbc). Следуйте инструкциям в репозитории, чтобы построить и запустить образец.

##Дальнейшие действия

Теперь, когда вы узнали, как использовать JDBC для работы с Hive, воспользуйтесь следующими ссылками, чтобы изучить другие способы работы с Azure HDInsight.

* [Отправка данных в HDInsight](hdinsight-upload-data.md)
* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование заданий MapReduce с HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO6-->