---
title: Использование JDBC для запроса Hive в Azure HDInsight
description: Узнайте, как использовать JDBC для подключения к Hive в Azure HDInsight и удаленного выполнения запросов данных, хранящихся в облаке.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/23/2016
ms.author: larryfr

---
# Подключение к Hive в Azure HDInsight с помощью драйвера Hive JDBC
[!INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

В этом документе рассматривается использование JDBC из приложения Java для удаленной отправки запросов Hive в кластер HDInsight. Вы узнаете, как подключиться из клиента SQuirreL SQL и как подключиться программным способом из Java.

Дополнительные сведения об интерфейсе JDBC Hive см. в разделе [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## Предварительные требования
Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Hadoop в кластере HDInsight. Будут работать кластеры либо под управлением Linux, либо под управлением Windows.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL представляет собой клиентское приложение JDBC.

Чтобы собрать и запустить пример приложения Java, используемый в этой статье, вам потребуется следующее.

* [Java Developer Kit (JDK) версии 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) или более поздней версии.
* [Apache Maven](https://maven.apache.org). Maven — система сборки для проектов Java, используемая в проекте, связанном с данной статьей.

## Строка подключения
Подключения JDBC к кластеру HDInsight в Azure осуществляются через порт 443, а защита трафика обеспечивается с помощью протокола SSL. Открытый шлюз, за которым находятся кластеры, перенаправляет трафик к порту, который фактически прослушивается HiveServer2. Поэтому обычная строка подключения будет следующей:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

Замените **CLUSTERNAME** именем кластера HDInsight.

## Аутентификация
При установке подключения необходимо указать имя администратора кластера HDInsight и пароль для проверки подлинности в шлюзе кластера. При подключении клиентов JDBC, например SQuirreL SQL, необходимо ввести имя и пароль администратора в параметрах клиента.

При подключении из приложения Java необходимо использовать имя пользователя и пароль. Например, приведенный ниже код Java открывает новое подключение с помощью строки подключения, имени администратора и пароля:

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

## Подключение с использованием клиента SQuirreL SQL
SQuirreL SQL — клиент JDBC, который можно использовать для удаленного выполнения запросов Hive с кластером HDInsight. При выполнении следующих действий вы скачаете и настроите драйверы для Hive. При этом предполагается, что SQuirreL SQL уже установлен.

1. Скопируйте драйверы JDBC Hive из кластера HDInsight.
   
   * Для **HDInsight под управлением Linux** выполните следующие действия для скачивания необходимых jar-файлов.
     
     1. Создайте каталог, в котором будут размещаться файлы. Пример: `mkdir hivedriver`.
     2. Из командной строки (Bash, PowerShell или другой) перейдите в новый каталог и используйте следующие команды для копирования файлов из кластера HDInsight.
        
             scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-jdbc*standalone.jar .
             scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
             scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
        
         Замените **USERNAME** именем учетной записи пользователя SSH для кластера. Замените **CLUSTERNAME** именем кластера HDInsight.
        
        > [!NOTE]
        > В средах Windows необходимо использовать служебную программу PSCP вместо scp. Ее можно скачать со страницы [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
        > 
        > 
   * Для **HDInsight под управлением Windows** выполните следующие действия для скачивания jar-файлов.
     
     1. На портале Azure выберите свой кластер HDInsight, а затем значок **Удаленный рабочий стол**.
        
         ![Значок удаленного рабочего стола](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)
     2. В колонке "Удаленный рабочий стол" нажмите кнопку **Подключить** для подключения к кластеру. Если удаленный рабочий стол не включен, укажите имя пользователя и пароль в форме, а затем выберите **Включить**, чтобы включить удаленный рабочий стол для кластера.
        
         ![Колонка удаленного рабочего стола](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)
        
         После нажатия кнопки **Подключить** будет загружен RDP-файл. Используйте этот файл для запуска клиента удаленного рабочего стола. При появлении запроса укажите имя пользователя и пароль для доступа к удаленному рабочему столу.
     3. После подключения скопируйте следующие файлы из сеанса удаленного рабочего стола на локальный компьютер. Поместите их в локальный каталог с именем `hivedriver`.
        
        * C:\\apps\\dist\\hive-0.14.0.2.2.9.1-7\\lib\\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
        * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\hadoop-common-2.6.0.2.2.9.1-7.jar
        * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\lib\\hadoop-auth-2.6.0.2.2.9.1-7.jar
          
          > [!NOTE]
          > Номера версий в путях и имена файлов для вашего кластера могут отличаться от указанных.
          > 
          > 
     4. После завершения копирования файлов отключите сеанс удаленного рабочего стола.
2. Запустите приложение SQuirreL SQL. В левой части окна выберите **Драйверы**.
   
    ![Вкладка "Драйверы" в левой части окна](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)
3. Среди значков в верхней части диалогового окна **Драйверы** нажмите значок **+** для создания драйвера.
   
    ![Значки драйверов](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)
4. В диалоговом окне "Добавление драйвера" укажите следующие сведения.
   
   * **Имя**: Hive
   * **Пример URL-адреса**: jdbc:hive2://localhost:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2
   * **Путь к дополнительным классам**: используйте кнопку "Добавить", чтобы добавить jar-файлы, загруженные ранее
   * **Имя класса**: org.apache.hive.jdbc.HiveDriver
     
     ![диалоговое окно "Добавить драйвер"](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)
     
     Нажмите кнопку **ОК**, чтобы сохранить эти параметры.
5. В левой части окна SQuirreL SQL выберите **Псевдонимы**. Затем нажмите значок **+**, чтобы создать псевдоним для подключения.
   
    ![добавление нового псевдонима](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)
6. В диалоговом окне **Добавить псевдоним** укажите следующие значения.
   
   * **Имя**: Hive в HDInsight
   * **Драйвер**: выберите драйвер **Hive** в раскрывающемся списке
   * **URL-адрес**: jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2
     
       Замените **CLUSTERNAME** именем кластера HDInsight.
   * **Имя пользователя**: имя пользователя для учетной записи входа кластера HDInsight. Значение по умолчанию — `admin`.
   * **Пароль**: пароль для учетной записи входа кластера. Это пароль, который вы указали при создании кластера HDInsight.
     
     ![диалоговое окно "Добавить псевдоним"](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)
     
     Используйте кнопку **Проверить**, чтобы убедиться, что подключение работает. При появлении диалогового окна **Подключение: Hive в HDInsight** выберите **Подключиться**, чтобы выполнить проверку. Если проверка пройдет успешно, вы увидите окно **Подключение прошло успешно**.
     
     Для сохранения псевдонима подключения нажмите кнопку **ОК** в нижней части диалогового окна **Добавить псевдоним**.
7. В раскрывающемся списке **Подключиться к...** в верхней части окна SQuirreL SQL выберите **Hive в HDInsight**. При появлении запроса выберите **Подключиться**.
   
    ![диалоговое окно подключения](./media/hdinsight-connect-hive-jdbc-driver/connect.png)
8. После подключения введите следующий запрос в окно запроса SQL, а затем нажмите кнопку **Запустить**. В области результатов должны появиться результаты запроса.
   
        select * from hivesampletable limit 10;
   
    ![диалоговое окно запроса SQL с результатами запроса](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

## Подключение из примера приложения Java
Пример использования клиента Java для запроса Hive в HDInsight доступен на странице [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Следуйте инструкциям в репозитории, чтобы построить и запустить образец.

## Устранение неполадок
### Непредвиденная ошибка при попытке открыть подключение к SQL.
**Симптомы**. При подключении к кластеру HDInsight версии 3.3 или 3.4 может появиться сообщение о возникновении непредвиденной ошибки. Трассировка стека для этой ошибки начнется со следующих строк:

    java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
    at java.util.concurrent.FutureTas...(FutureTask.java:122)
    at java.util.concurrent.FutureTask.get(FutureTask.java:206)

**Причина**. Эта ошибка возникает при несоответствии версии файла commons-codec.jar, используемого SQuirreL, и файла, требуемого для компонентов Hive JDBC, скачанных из кластера HDInsight.

**Решение**. Чтобы устранить эту ошибку, выполните приведенные далее действия.

1. Скачайте файл commons-codec.jar из кластера HDInsight.
   
        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar
2. Закройте SQuirreL, а затем перейдите в каталог, где установлен SQuirreL. В каталоге SquirreL в каталоге `lib` замените существующий файл commons-codec.ja файлом, скачанным из кластера HDInsight.
3. Перезапустите SQuirreL. При последующих подключениях к Hive в HDInsight ошибка больше не должна возникать.

## Дальнейшие действия
Теперь, когда вы узнали, как использовать JDBC для работы с Hive, воспользуйтесь следующими ссылками, чтобы изучить другие способы работы с Azure HDInsight.

* [Отправка данных в HDInsight](hdinsight-upload-data.md)
* [Использование Hive с HDInsight](hdinsight-use-hive.md)
* [Использование Pig с HDInsight](hdinsight-use-pig.md)
* [Использование заданий MapReduce с HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->