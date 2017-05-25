---
title: "Выполнение запроса Hive с помощью JDBC в Azure HDInsight | Документация Майкрософт"
description: "Узнайте, как подключиться к Hive в кластерах Hadoop в Azure HDInsight с помощью JDBC."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 928f8d2a-684d-48cb-894c-11c59a5599ae
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: dd76e2450be2b05d011de7ded49bfa9630190e71
ms.contentlocale: ru-ru
ms.lasthandoff: 05/17/2017


---
# <a name="query-hive-through-jdbc"></a>Выполнение запроса Hive с помощью JDBC

[!INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Узнайте, как использовать JDBC из приложения Java для отправки запросов Hive в Hadoop в Azure HDInsight. Из этой статьи вы узнаете, как подключиться программным способом или из клиента SQuirreL SQL.

Дополнительные сведения об интерфейсе JDBC Hive см. в статье [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Предварительные требования

* Hadoop в кластере HDInsight. Работают кластеры либо под управлением Linux, либо под управлением Windows.

  > [!IMPORTANT]
  > Linux — единственная операционная система, используемая для работы с HDInsight 3.4 или более поздней версии. Чтобы узнать больше, ознакомьтесь с разделом [Приближается дата прекращения сопровождения HDI версии 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL представляет собой клиентское приложение JDBC.

* [Java Developer Kit (JDK) версии 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) или более поздней версии.

* [Apache Maven](https://maven.apache.org). Maven — система сборки для проектов Java, используемая в проекте, связанном с данной статьей.

## <a name="jdbc-connection-string"></a>Строка подключения JDBC

Подключения JDBC к кластеру HDInsight в Azure осуществляются через порт 443, а защита трафика обеспечивается с помощью протокола SSL. Открытый шлюз, за которым находятся кластеры, перенаправляет трафик к порту, который фактически прослушивается HiveServer2. Ниже приведен пример строки подключения:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

Замените `CLUSTERNAME` на имя вашего кластера HDInsight.

## <a name="authentication"></a>Аутентификация

При установке подключения необходимо указать имя администратора кластера HDInsight и пароль для проверки подлинности в шлюзе кластера. При подключении клиентов JDBC, например SQuirreL SQL, необходимо ввести имя и пароль администратора в параметрах клиента.

При подключении из приложения Java необходимо использовать имя пользователя и пароль. Например, приведенный ниже код Java открывает новое подключение с помощью строки подключения, имени администратора и пароля:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Подключение с использованием клиента SQuirreL SQL

SQuirreL SQL — клиент JDBC, который можно использовать для удаленного выполнения запросов Hive с кластером HDInsight. В следующих шагах предполагается, что SQuirreL SQL уже установлен.

1. Скопируйте драйверы JDBC Hive из кластера HDInsight.

    * Чтобы скачать необходимые JAR-файлы, для **HDInsight под управлением Linux** сделайте следующее.

        1. Создайте каталог, содержащий файлы. Например, `mkdir hivedriver`.

        2. В командной строке выполните следующие команды, чтобы копировать файлы из кластера HDInsight.

            ```bash
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-jdbc*standalone.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
            ```

            Замените `USERNAME` именем учетной записи пользователя SSH для кластера. Замените `CLUSTERNAME` именем кластера HDInsight.

    * Чтобы скачать JAR-файлы, для **HDInsight под управлением Windows** сделайте следующее.

        1. На портале Azure выберите свой кластер HDInsight, а затем — значок **Удаленный рабочий стол**.

            ![Значок удаленного рабочего стола](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. В колонке "Удаленный рабочий стол" нажмите кнопку **Подключить** для подключения к кластеру. Если значок "Удаленный рабочий стол" не включен, укажите имя пользователя и пароль в форме, а затем выберите **Включить**, чтобы включить значок "Удаленный рабочий стол" для кластера.

            ![Колонка удаленного рабочего стола](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)

            После нажатия кнопки **Подключить** будет скачан RDP-файл. Используйте этот файл для запуска клиента удаленного рабочего стола. При появлении запроса укажите имя пользователя и пароль для доступа к удаленному рабочему столу.

        3. После подключения скопируйте следующие файлы из сеанса удаленного рабочего стола на локальный компьютер. Поместите их в локальный каталог с именем `hivedriver`.

            * C:\apps\dist\hive-0.14.0.2.2.9.1-7\lib\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\hadoop-common-2.6.0.2.2.9.1-7.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\lib\hadoop-auth-2.6.0.2.2.9.1-7.jar

            > [!NOTE]
            > Номера версий в путях и имена файлов для вашего кластера могут отличаться от указанных.

        4. После завершения копирования файлов отключите сеанс удаленного рабочего стола.

2. Запустите приложение SQuirreL SQL. В левой части окна выберите **Драйверы**.

    ![Вкладка "Драйверы" в левой части окна](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)

3. Среди значков в верхней части диалогового окна **Drivers** (Драйверы) щелкните значок **+** для создания драйвера.

    ![Значки драйверов](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)

4. В диалоговом окне Add Driver (Добавление драйвера) укажите следующие сведения.

    * **Имя:** Hive.
    * **Пример URL-адреса:** `jdbc:hive2://localhost:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`
    * **Путь к дополнительным классам.** Нажмите кнопку "Добавить", чтобы добавить скачанные ранее JAR-файлы.
    * **Имя класса:** org.apache.hive.jdbc.HiveDriver.

   ![диалоговое окно "Добавить драйвер"](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)

   Нажмите кнопку **ОК**, чтобы сохранить эти параметры.

5. В левой части окна SQuirreL SQL выберите **Псевдонимы**. Затем щелкните значок **+**, чтобы создать псевдоним для подключения.

    ![добавление нового псевдонима](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)

6. В диалоговом окне **Добавить псевдоним** укажите следующие значения.

    * **Имя:** Hive в HDInsight.

    * **Драйвер.** Выберите драйвер **Hive** в раскрывающемся списке.

    * **URL-адрес:** jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

        Замените **CLUSTERNAME** именем кластера HDInsight.

    * **Имя пользователя.** Имя пользователя для учетной записи входа кластера HDInsight. Значение по умолчанию — `admin`.

    * **Пароль.** Пароль для учетной записи входа кластера.

    ![диалоговое окно "Добавить псевдоним"](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)

    Нажмите кнопку **Проверить**, чтобы убедиться, что подключение работает. При появлении диалогового окна **Connect to: Hive on HDInsight** (Подключение: Hive в HDInsight) выберите **Подключиться**, чтобы выполнить проверку. Если проверка пройдет успешно, вы увидите диалоговое окно **Connection successful** (Подключение выполнено успешно).

    Чтобы сохранить псевдоним подключения, в нижней части диалогового окна **Add Alias** (Добавить псевдоним) нажмите кнопку **ОК**.

7. В раскрывающемся списке **Подключиться к** в верхней части окна SQuirreL SQL выберите **Hive on HDInsight** (Hive в HDInsight). При появлении запроса выберите **Подключиться**.

    ![диалоговое окно подключения](./media/hdinsight-connect-hive-jdbc-driver/connect.png)

8. После подключения введите следующий запрос в диалоговое окно запроса SQL, а затем нажмите кнопку **Запустить**. В области результатов должны появиться результаты запроса.

        select * from hivesampletable limit 10;

    ![диалоговое окно запроса SQL с результатами запроса](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Подключение из примера приложения Java

Пример использования клиента Java для запроса Hive в HDInsight доступен на странице [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Следуйте инструкциям в репозитории, чтобы построить и запустить образец.

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Непредвиденная ошибка при попытке открыть подключение к SQL

**Признаки.** При подключении к кластеру HDInsight версии 3.3 или 3.4 может появиться сообщение о возникновении непредвиденной ошибки. Трассировка стека для этой ошибки начинается со следующих строк:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Причина.** Эта ошибка возникает при несоответствии версии файла commons-codec.jar, используемого SQuirreL, и файла, требуемого для компонентов Hive JDBC.

**Решение.** Чтобы устранить эту ошибку, выполните приведенные далее действия.

1. Скачайте файл commons-codec.jar из кластера HDInsight.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Закройте SQuirreL, а затем перейдите в каталог, где установлен SQuirreL. В каталоге SquirreL в каталоге `lib` замените существующий файл commons-codec.ja файлом, скачанным из кластера HDInsight.

3. Перезапустите SQuirreL. При последующих подключениях к Hive в HDInsight ошибка больше не должна возникать.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как использовать JDBC для работы с Hive, воспользуйтесь следующими ссылками, чтобы изучить другие способы работы с Azure HDInsight.

* [Отправка данных в HDInsight](hdinsight-upload-data.md)
* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование заданий MapReduce с HDInsight](hdinsight-use-mapreduce.md)

