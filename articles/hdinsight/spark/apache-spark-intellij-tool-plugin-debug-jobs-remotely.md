---
title: Удаленная отладка приложений в HDInsight Spark с помощью набора средств Azure для IntelliJ | Документация Майкрософт
description: Узнайте, как использовать средства HDInsight в наборе средств Azure для IntelliJ для удаленной отладки приложений Spark, выполняемых в кластерах HDInsight через VPN.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 55fb454f-c7dc-46de-a978-e242e9a94f4c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 6ca69ccab6c9b526c86f6f7a8998089e52c6c939
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31521438"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-spark-applications-remotely-in-hdinsight-through-vpn"></a>Удаленная отладка приложений Spark в HDInsight через VPN с помощью набора средств Azure для IntelliJ

Для удаленной отладки приложений Spark мы рекомендуем использовать протокол SSH. Инструкции см. в статье [Удаленная отладка приложений Spark в кластере HDInsight через SSH с помощью набора средств Azure для IntelliJ](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

В этой статье приводятся пошаговые инструкции по использованию средств HDInsight в наборе средств Azure для IntelliJ для отправки задания Spark в кластер HDInsight Spark и его удаленной отладки с настольного компьютера. Чтобы выполнить эти задачи, необходимо выполнить перечисленные ниже общие шаги.

1. Создание виртуальной сети Azure типа "сеть — сеть" или "точка — сеть". В инструкциях в этом документе предполагается, что вы используете тип "сеть — сеть".
2. Создание в HDInsight кластера Spark, который является частью виртуальной сети типа "сеть — сеть".
3. Проверка подключения между головным узлом кластера и компьютером.
4. Создание приложения Scala в IntelliJ IDEA и его настройка для удаленной отладки.
5. Запуск и отладка приложения.

## <a name="prerequisites"></a>предварительным требованиям
* **Подписка Azure**. Чтобы узнать больше, ознакомьтесь с [получением бесплатной пробной учетной записи Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Кластер Apache Spark в HDInsight**. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).
* **Комплект разработчика Oracle Java**. Его можно установить с [веб-сайта Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* **IntelliJ IDEA**. В этой статье используется версия 2017.1. Его можно установить с [веб-сайта JetBrains](https://www.jetbrains.com/idea/download/).
* **Средства HDInsight из набора средств Azure для IntelliJ**. Средства HDInsight для IntelliJ доступны в составе набора средств Azure для IntelliJ. Инструкции по установке набора средств Azure см. в статье [Установка набора средств Azure для IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Войдите в подписку Azure из IntelliJ IDEA**. Следуйте указаниям в разделе [Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для IntelliJ](apache-spark-intellij-tool-plugin.md).
* **Обработка исключений**. При запуске приложения Spark Scala для удаленной отладки на компьютере Windows может возникнуть исключение. Это исключение описывается в [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) и порождается из-за отсутствия файла WinUtils.exe в Windows. Чтобы обойти эту ошибку, нужно [скачать этот исполняемый файл](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe), например в папку **C:\WinUtils\bin**. Добавьте переменную среды **HADOOP_HOME** и присвойте ей значение **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Шаг 1. Создание виртуальной сети Azure
Следуйте инструкциям по созданию виртуальной сети Azure по ссылкам ниже. Затем проверьте сетевое подключение между своим компьютером и этой виртуальной сетью.

* [Создание виртуальной сети с VPN-подключением типа "сеть — сеть" с помощью портала Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Создание виртуальной сети с VPN-подключением типа "сеть — сеть" с помощью PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Настройка подключения к виртуальной сети типа "точка — сеть" с помощью PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Шаг 2. Создание кластера HDInsight Spark
Рекомендуется создать в Azure HDInsight кластер Apache Spark в составе созданной вами виртуальной сети Azure. Воспользуйтесь сведениями из статьи [Создание кластеров Hadoop под управлением Linux в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). На этапе дополнительной настройки выберите виртуальную сеть Azure, созданную на предыдущем шаге.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Шаг 3. Проверка подключения между головным узлом кластера и компьютером
1. Прежде всего необходимо получить IP-адрес головного узла. Откройте пользовательский интерфейс Ambari для кластера. В колонке кластера щелкните **Панель мониторинга**.

    ![Выбор панели мониторинга в Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
2. В пользовательском интерфейсе Ambari выберите **Hosts** (Узлы).

    ![Выборе "Hosts" (Узлы) в Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
3. Отобразится список головных узлов, рабочих узлов и узлов zookeeper. Головные узлы отличаются наличием префикса **hn**\*. Выберите первый головной узел.

    ![Поиск головного узла в Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
4. В области **Summary** (Сводка) в нижней части открывшейся страницы скопируйте значения **IP Address** (IP-адрес) и **Hostname** (Имя узла) для головного узла.

    ![Поиск IP-адреса в Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
5. Добавьте IP-адрес и имя головного узла в файл **hosts** на компьютер, на котором вы собираетесь выполнять и удаленно отлаживать задание Spark. Это обеспечит обмен данными с головным узлом с помощью IP-адреса и имени узла.

   a. Откройте Блокнот с повышенным уровнем разрешений. В меню **Файл** выберите **Открыть** и найдите папку с файлом hosts. На компьютере Windows это папка **C:\Windows\System32\Drivers\etc\hosts**.

   Б. Добавьте в файл **hosts** следующие данные.

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
6. На компьютере, подключенном к виртуальной сети Azure и используемом кластером HDInsight, проверьте связь с головными узлами при помощи IP-адреса и имени узла.
7. Установите SSH-подключение к головному узлу кластера, следуя инструкциям из раздела [Подключение к кластеру HDInsight на основе Linux](../hdinsight-hadoop-linux-use-ssh-unix.md). Проверьте связь головного узла кластера с компьютером при помощи IP-адреса. Проверьте подключение к обоим IP-адресам, назначенным компьютеру:

    - IP-адресу сетевого подключения и
    - IP-адресу виртуальной сети Azure.

8. Повторите эти действия на другом головном узле.

## <a name="step-4-create-a-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Шаг 4. Создание приложения Spark Scala при помощи средств HDInsight в наборе средств Azure для IntelliJ и его настройка для удаленной отладки
1. Откройте IntelliJ IDEA и создайте проект. В диалоговом окне **Новый проект** сделайте следующее:

    ![Выбор шаблона нового проекта в IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Выберите **HDInsight** > **Spark on HDInsight (Scala)** (Spark в HDInsight (Scala)).

    Б. Щелкните **Далее**.
2. В следующем диалоговом окне **New Project** (Новый проект) выполните приведенные ниже действия, после чего щелкните **Finish** (Готово).

    - Введите имя и расположение проекта.

    - В раскрывающемся списке для параметра **Project SDK** (Пакет SDK проекта) выберите **Java 1.8** для кластера Spark 2.x или **Java 1.7** для кластера Spark 1.x.

    - В раскрывающемся списке **Spark version** (Версия Spark) мастер создания проекта Scala интегрирует правильную версию пакета SDK для Spark и пакета SDK для Scala. Если версия кластера Spark ниже 2.0, выберите **Spark 1.x**. В противном случае выберите **Spark 2.x**. В этом примере используется **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Выбор версии пакета SDK и Spark](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
3. Проект Spark автоматически создаст артефакт. Чтобы просмотреть артефакт, сделайте следующее:

    a. В меню **File** (Файл) выберите **Project Structure** (Структура проекта).

    Б. В диалоговом окне **Project Structure** (Структура проекта) щелкните **артефакты**, чтобы просмотреть созданный по умолчанию артефакт. Вы также можете создать собственный артефакт, щелкнув значок "плюс" (**+**).

   ![Создание JAR-файла](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


4. Добавьте библиотеки в проект. Чтобы добавить библиотеку, выполните следующее.

    a. Щелкните правой кнопкой мыши имя проекта в дереве проектов и выберите **Open Module Settings** (Открыть параметры модуля). 

    Б. В диалоговом окне **Project Structure** (Структура проекта) выберите **Libraries** (Библиотеки), щелкните символ (**+**), а затем выберите **From Maven** (Из Maven).

    ![Добавление библиотеки](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. В диалоговом окне **Download Library from Maven Repository** (Скачивание библиотеки из репозитория Maven) найдите и добавьте перечисленные ниже библиотеки.

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
5. Скопируйте файлы `yarn-site.xml` и `core-site.xml` с головного узла кластера и добавьте их в проект. Выполните указанные ниже команды, чтобы скопировать файлы. Для выполнения приведенных далее команд `scp` можно использовать [Cygwin](https://cygwin.com/install.html). Это позволит копировать файлы с головных узлов кластера.

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Так как мы уже добавили IP-адреса и имена головных узлов кластера в файл hosts на компьютере, можно использовать команды `scp`, как показано ниже.

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Чтобы добавить эти файлы в проект, скопируйте их в папку **/src** дерева проектов, например `<your project directory>\src`.
6. Обновите файл `core-site.xml`, чтобы внести описанные ниже изменения.

   a. Замените зашифрованный ключ. Файл `core-site.xml` содержит зашифрованный ключ учетной записи хранения, связанной с кластером. В файле `core-site.xml`, добавленном в проект, замените зашифрованный ключ фактическим ключом к хранилищу данных, связанным с учетной записью хранения, используемой по умолчанию. Дополнительные сведения см. в разделе [Управление ключами доступа к хранилищу](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   Б. Удалите из файла `core-site.xml` указанные ниже записи.

           <property>
                 <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
                 <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
           </property>

           <property>
                 <name>fs.azure.shellkeyprovider.script</name>
                 <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
           </property>

           <property>
                 <name>net.topology.script.file.name</name>
                 <value>/etc/hadoop/conf/topology_script.py</value>
           </property>
   c. Сохраните файл.
7. Добавьте класс main для своего приложения. В **обозревателе проектов** щелкните правой кнопкой мыши **src**, наведите указатель мыши на пункт **New** (Создать) и выберите **Scala Class** (Класс Scala).

    ![Выбор класса main](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
8. В диалоговом окне **Create New Scala Class** (Создание класса Scala) введите имя, в поле **Kind** (Вид) выберите **Object** (Объект) и нажмите кнопку **ОК**.

    ![Создание класса Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
9. Скопируйте приведенный ниже код и вставьте его в файл `MyClusterAppMain.scala` . Этот код создает контекст Spark и открывает метод `executeJob` из объекта `SparkSample`.

        import org.apache.spark.{SparkConf, SparkContext}

        object SparkSampleMain {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkSample")
                                      .set("spark.hadoop.validateOutputSpecs", "false")
            val sc = new SparkContext(conf)

            SparkSample.executeJob(sc,
                                   "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                                   "wasb:///HVACOut")
          }
        }

10. Повторите шаги 8 и 9, чтобы добавить новый объект Scala с именем `*SparkSample`. Добавьте в этот класс приведенный ниже код. Этот код считывает данные из файла HVAC.csv (доступного для всех кластеров HDInsight Spark). Он извлекает строки, содержащие только одну цифру в седьмом столбце CSV-файла, и записывает результат в **/HVACOut** в используемом по умолчанию контейнере хранилища для кластера.

        import org.apache.spark.SparkContext

        object SparkSample {
         def executeJob (sc: SparkContext, input: String, output: String): Unit = {
           val rdd = sc.textFile(input)

           //find the rows which have only one digit in the 7th column in the CSV
           val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

           val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
           println(s)

           rdd1.saveAsTextFile(output)
           //rdd1.collect().foreach(println)
         }
        }
11. Повторите шаги 8 и 9, чтобы добавить новый класс `RemoteClusterDebugging`. Этот класс реализует платформу тестирования Spark, которая используется для отладки приложений. Добавьте в класс `RemoteClusterDebugging` приведенный ниже код.

        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }

     Необходимо обратить внимание на несколько важных моментов.

      * Для `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`убедитесь, что JAR-файл сборки Spark доступен в хранилище кластера по указанному пути.
      * Для `setJars` укажите расположение, в котором будет создан JAR-файл артефакта. Обычно это `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
12. В классе `*RemoteClusterDebugging` щелкните правой кнопкой мыши ключевое слово `test` и выберите **Create RemoteClusterDebugging Configuration** (Создать конфигурацию RemoteClusterDebugging).

    ![Создание удаленной конфигурации](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

13. В диалоговом окне **Create RemoteClusterDebugging Configuration** (Создание конфигурации RemoteClusterDebugging) введите имя конфигурации, а затем выберите **Test kind** (Тестовая) для параметра **Test name** (Имя теста). Оставьте значения всех остальных параметров по умолчанию. Нажмите кнопку **Apply** (Применить), а затем нажмите кнопку **ОК**.

    ![Добавление сведений о конфигурации](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
14. В строке меню должен отобразиться раскрывающийся список конфигурации **Remote Run** (Удаленный запуск).

    ![Раскрывающийся список удаленного запуска](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Шаг 5. Запуск приложения в режиме отладки
1. В проекте IntelliJ IDEA откройте `SparkSample.scala` и создайте точку останова рядом с `val rdd1`. В контекстном меню **Create Breakpoint for** (Создание точки останова для) выберите **line in function executeJob** (Встроенная функция executeJob).

    ![Добавление точки останова](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
2. Нажмите кнопку **Debug Run** (Запуск отладки) рядом с раскрывающимся списком конфигурации **Remote Run** (Удаленный запуск), чтобы запустить приложение.

    ![Нажатие кнопки "Debug Run" (Запуск отладки)](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
3. Когда выполнение программы достигнет точки останова, на нижней панели отобразится вкладка **Debugger** (Отладчик).

    ![Просмотр вкладки "Debugger" (Отладчик)](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
4. Чтобы добавить контрольное значение, щелкните значок (**+**).

    ![Щелчок значка "+"](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    В этом примере выполнение приложения было прервано до создания переменной `rdd1`. С помощью этого контрольного значения можно просмотреть первые пять строк в переменной `rdd`. Нажмите клавишу **ВВОД**.

    ![Запуск программы в режиме отладки](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Как видно на предыдущем рисунке, во время выполнения можно создавать запросы на терабайты данных и отлаживать работу приложения. Например, в результатах на предыдущем рисунке можно увидеть, что первая строка выходных данных является заголовком. На основе этих выходных данных можно изменить код приложения, чтобы пропустить строку заголовка, если требуется.
5. Теперь можно щелкнуть значок **Resume Program** (Возобновить выполнение программы), чтобы продолжить выполнение приложения.

    ![Выбор "Resume Program" (Возобновить выполнение программы)](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
6. Если работа приложения завершится успешно, отобразится результат, подобный приведенному ниже.

    ![Вывод на консоль](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>Дальнейшие действия
* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Демонстрация
* Создание проекта Scala (видео): [создание приложений Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Удаленная отладка (видео): [Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Удаленная отладка приложений Spark в кластере HDInsight с помощью набора средств Azure для IntelliJ)

### <a name="scenarios"></a>Сценарии
* [Использование средств визуализации данных с помощью Apache Spark BI в Azure HDInsight](apache-spark-use-bi-tools.md)
* [Создание приложений машинного обучения Apache Spark в Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark через SSH с помощью набора средств Azure для IntelliJ](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование средств HDInsight в наборе средств Azure для Eclipse для создания приложений Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)
