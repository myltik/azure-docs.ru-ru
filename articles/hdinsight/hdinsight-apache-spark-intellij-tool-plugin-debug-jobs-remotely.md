---
title: "Удаленная отладка приложений в HDInsight Spark с помощью набора средств Azure для IntelliJ | Документация Майкрософт"
description: "Узнайте, как использовать средства HDInsight в наборе средств Azure для IntelliJ для удаленной отладки приложений Spark в кластерах HDInsight Spark через VPN."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 55fb454f-c7dc-46de-a978-e242e9a94f4c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 21b675e0baddd634583c6ecbd78e46885bb51386
ms.contentlocale: ru-ru
ms.lasthandoff: 07/28/2017

---
# <a name="use-azure-toolkit-for-intellij-to-debug-applications-remotely-on-hdinsight-spark-through-vpn"></a>Удаленная отладка приложений в HDInsight Spark через VPN с помощью набора средств Azure для IntelliJ

Для удаленной отладки приложений Spark мы рекомендуем использовать SSH. Инструкции см. в статье [Удаленная отладка приложений Spark в кластере HDInsight через SSH с помощью набора средств Azure для IntelliJ](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

В этой статье приводятся пошаговые инструкции по использованию средств HDInsight в наборе средств Azure для IntelliJ для отправки задания Spark в кластер HDInsight Spark и его удаленной отладки с настольного компьютера. Для этого необходимо выполнить перечисленные ниже общие шаги.

1. Создание виртуальной сети Azure типа "сеть — сеть" или "точка — сеть". В инструкциях в этом документе предполагается, что вы используете тип "сеть — сеть".
2. Создание в Azure HDInsight кластера Spark, который является частью виртуальной сети Azure типа "сеть — сеть".
3. Проверка подключения между головным узлом кластера и компьютером.
4. Создание приложения Scala в IntelliJ IDEA и его настройка для удаленной отладки.
5. Запуск и отладка приложения.

## <a name="prerequisites"></a>Предварительные требования
* Подписка Azure. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](hdinsight-apache-spark-jupyter-spark-sql.md).
* Комплект разработчика Oracle Java. Его можно установить [отсюда](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. В этой статье используется версия 2017.1. Его можно установить [отсюда](https://www.jetbrains.com/idea/download/).
* Средства HDInsight в наборе средств Azure для IntelliJ. Средства HDInsight для IntelliJ доступны в составе набора средств Azure для IntelliJ. Инструкции по установке набора средств Azure см. в статье [Установка набора средств Azure для IntelliJ](../azure-toolkit-for-intellij-installation.md).
* Войдите в подписку Azure из IntelliJ IDEA. Следуйте указаниям, приведенным [здесь](hdinsight-apache-spark-intellij-tool-plugin.md).
* При запуске приложения Spark Scala для удаленной отладки на компьютере Windows может возникнуть исключение, описанное в [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356) и связанное с отсутствием в Windows файла WinUtils.exe. Чтобы обойти эту ошибку, [скачайте этот исполняемый файл отсюда](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe), например в папку **C:\WinUtils\bin**. После этого добавьте переменную среды **HADOOP_HOME** и присвойте ей значение **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Шаг 1. Создание виртуальной сети Azure
Следуйте инструкциям по созданию виртуальной сети Azure по ссылкам ниже. Затем проверьте сетевое подключение между своим компьютером и виртуальной сетью Azure.

* [Создание виртуальной сети с VPN-подключением типа "сеть — сеть" с помощью портала Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Создание виртуальной сети с VPN-подключением типа "сеть — сеть" с помощью PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Настройка подключения к виртуальной сети типа "точка — сеть" с помощью PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Шаг 2. Создание кластера HDInsight Spark
Кроме того, следует создать в Azure HDInsight кластер Apache Spark в составе созданной вами виртуальной сети Azure. Воспользуйтесь сведениями из статьи [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md). На этапе дополнительной настройки выберите виртуальную сеть Azure, созданную на предыдущем шаге.

## <a name="step-3-verify-the-connectivity-between-the-cluster-headnode-and-your-desktop"></a>Шаг 3. Проверка сетевого подключения между головным узлом кластера и компьютером
1. Прежде всего необходимо получить IP-адрес головного узла. Откройте пользовательский интерфейс Ambari для кластера. В колонке кластера щелкните **Панель мониторинга**.

    ![Поиск IP-адреса головного узла](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/launch-ambari-ui.png)
2. В правом верхнем углу окна пользовательского интерфейса Ambari щелкните **Узлы**.

    ![Поиск IP-адреса головного узла](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/ambari-hosts.png)
3. Отобразится список головных узлов, рабочих узлов и узлов zookeeper. Головные узлы обозначены префиксом **hn***. Щелкните первый головной узел.

    ![Поиск IP-адреса головного узла](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/cluster-headnodes.png)
4. В нижней части открывшейся страницы скопируйте IP-адрес и имя головного узла в поле **Сводка** .

    ![Поиск IP-адреса головного узла](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/headnode-ip-address.png)
5. Добавьте IP-адрес и имя головного узла в файл **Узлы** на компьютере, с которого собираетесь выполнять и удаленно отлаживать задания Spark. Это обеспечит обмен данными с головным узлом.

   1. Откройте блокнот с повышенным уровнем разрешений. В меню "Файл" выберите пункт **Открыть** и перейдите в папку с файлом hosts. На компьютере под управлением Windows это папка `C:\Windows\System32\Drivers\etc\hosts`.
   2. Добавьте в файл **hosts** указанные далее данные.

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
6. С компьютера, подключенного к виртуальной сети Azure и использующегося кластером HDInsight, проверьте связь с обоими головными узлами при помощи IP-адреса и имени узла.
7. Установите SSH-подключение к головному узлу кластера, воспользовавшись инструкциями из раздела [Подключение к кластеру HDInsight на основе Linux](hdinsight-hadoop-linux-use-ssh-unix.md). Проверьте связь головного узла кластера с компьютером при помощь IP-адреса. Следует проверить возможность подключения к обоим IP-адресам, назначенным компьютеру. Один предназначен для сетевого подключения, а другой — для виртуальной сети Azure, к которой подключен компьютер.
8. Повторите эти действия на другом головном узле.

## <a name="step-4-create-a-spark-scala-application-using-the-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Шаг 4. Создание приложения Spark Scala при помощи средств HDInsight в наборе средств Azure для IntelliJ и его настройка для удаленной отладки
1. Запустите IntelliJ IDEA и создайте проект. В диалоговом окне нового проекта установите параметры, как на снимке экрана ниже, а затем нажмите кнопку **Next**(«Далее»).

    ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/create-hdi-scala-app.png)

   * В левой области выберите **HDInsight**.
   * В правой области выберите **Spark on HDInsight (Scala)**(Spark в HDInsight (Scala)).
   * Щелкните **Далее**.
2. В следующем окне укажите приведенные ниже сведения, а затем нажмите кнопку **Готово**.  
   - Введите имя и расположение проекта.
   - Для параметра **Project SDK** (Пакет SDK проекта) выберите Java 1.8 для кластера Spark 2.x или Java 1.7 для кластера Spark 1.x.
   - Для параметра **Spark Version** (Версия Spark) мастер создания проекта Scala интегрирует правильную версию пакета SDK Spark и пакета SDK Scala. Если версия кластера Spark ниже, чем 2.0, выберите Spark 1.x. В противном случае следует выбрать Spark 2.x. В этом примере используется Spark 2.0.2 (Scala 2.11.8).
       ![Создание приложения Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/hdi-scala-project-details.png)
  
3. Проект Spark автоматически создаст артефакт. Чтобы просмотреть артефакт, выполните описанные ниже действия.

   1. В меню **File** (Файл) выберите пункт **Project Structure** (Структура проекта).
   2. В диалоговом окне **Project Structure** (Структура проекта) щелкните **Артефакты**, чтобы просмотреть созданный по умолчанию артефакт.
   ![Создание JAR-файла](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/default-artifact.png)

      Кроме того, можно создать свой собственный артефакт, щелкнув значок **+** , выделенный на рисунке выше.

4. Добавьте библиотеки в проект. Чтобы добавить библиотеку, щелкните правой кнопкой мыши имя проекта в дереве проектов и выберите пункт **Open Module Settings**(Открыть параметры модуля). В левой области диалогового окна **Project Structure** (Структура проекта) выберите пункт **Библиотеки**, щелкните знак (+) и выберите пункт **From Maven** (Из Maven).

    ![Добавление библиотеки](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/add-library.png)

    В диалоговом окне **Download Library from Maven Repository** (Скачивание библиотеки из репозитория Maven) найдите и добавьте перечисленные ниже библиотеки.

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
5. Скопируйте файлы `yarn-site.xml` и `core-site.xml` с головного узла кластера и добавьте их в проект. Выполните указанные ниже команды, чтобы скопировать файлы. Для выполнения приведенных далее команд `scp` можно использовать [Cygwin](https://cygwin.com/install.html). Это позволит копировать файлы из головных узлов кластера.

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Так как мы уже добавили IP-адреса и имена головных узлов кластера в файл hosts на компьютере, можно использовать команды **scp** , как указано ниже.

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Добавьте эти файлы в проект, скопировав их в папку **/src** дерева проектов, например `<your project directory>\src`.
6. Обновите `core-site.xml` , чтобы внести описанные ниже изменения.

   1. `core-site.xml` содержит зашифрованный ключ учетной записи хранения, связанной с кластером. В файле `core-site.xml` , добавленном в проект, замените зашифрованный ключ фактическим ключом к хранилищу данных, связанным с учетной записью хранения, используемой по умолчанию. Ознакомьтесь с разделом [Управление ключами доступа к хранилищу](../storage/storage-create-storage-account.md#manage-your-storage-account).

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   2. Удалите из файла `core-site.xml`указанные ниже записи.

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
   3. Сохраните файл.
7. Добавьте класс Main для вашего приложения. В **обозревателе проектов** щелкните правой кнопкой мыши **src**, наведите указатель мыши на пункт **Создать** и щелкните **Scala Class** (Класс Scala).

    ![Добавить исходный код](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/hdi-spark-scala-code.png)
8. В диалоговом окне **Create New Scala Class** (Создание класса Scala) введите имя, в поле **Вид** выберите **Объект** и нажмите кнопку **ОК**.

    ![Добавить исходный код](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/hdi-spark-scala-code-object.png)
9. Скопируйте приведенный ниже код и вставьте его в файл `MyClusterAppMain.scala` . Этот код создает контекст Spark и запускает метод `executeJob` из объекта `SparkSample`.

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

10. Повторите шаги 8 и 9, описанные выше, чтобы добавить новый объект Scala с именем `SparkSample`. Добавьте в этот класс приведенный далее код. Этот код считывает данные из файла HVAC.csv (доступного для всех кластеров HDInsight Spark), извлекает строки, содержащие только одну цифру в седьмом столбце CSV-файла, и записывает результат в **/HVACOut** в используемом по умолчанию контейнере хранилища для кластера.

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
11. Повторите шаги 8 и 9, описанные выше, чтобы добавить новый класс с именем `RemoteClusterDebugging`. Этот класс реализует платформу тестирования Spark, которая используется для отладки приложений. Добавьте в класс `RemoteClusterDebugging` приведенный далее код.

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

     При этом необходимо обратить внимание на несколько важных моментов.

   * Для `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`убедитесь, что JAR-файл сборки Spark доступен в хранилище кластера по указанному пути.
   * Для `setJars`укажите расположение, где будет создан JAR-файл артефакта. Обычно это `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
12. В классе `RemoteClusterDebugging` щелкните правой кнопкой мыши ключевое слово `test` и выберите пункт **Create RemoteClusterDebugging Configuration** (Создать конфигурацию RemoteClusterDebugging).

    ![Создание удаленной конфигурации](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/create-remote-config.png)

13. В диалоговом окне введите имя конфигурации и выберите для параметра **Test kind** (Тип теста) значение **Имя теста**. Оставьте остальные значения, установленные по умолчанию, нажмите кнопку **Применить**, а затем — кнопку **ОК**.

    ![Создание удаленной конфигурации](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/provide-config-value.png)
14. В строке меню должен отобразиться раскрывающийся список конфигурации **Remote Run** (Удаленный запуск).

    ![Создание удаленной конфигурации](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Шаг 5. Запуск приложения в режиме отладки
1. В проекте IntelliJ IDEA откройте `SparkSample.scala` и создайте точку останова рядом с "val rdd1". В контекстном меню создания точки останова выберите пункт **line in function executeJob**(Строка функции executeJob).

    ![Добавление точки останова](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/create-breakpoint.png)
2. Нажмите кнопку **Debug Run** (Запуск отладки) рядом с раскрывающимся списком конфигурации **Удаленный запуск**, чтобы запустить приложение.

    ![Запуск программы в режиме отладки](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/debug-run-mode.png)
3. Когда выполнение программы достигнет точки останова, на нижней панели отобразится вкладка **Debugger** (Отладчик).

    ![Запуск программы в режиме отладки](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/debug-add-watch.png)
4. Щелкните значок (**+**), чтобы добавить контрольное значение, как показано на рисунке ниже.

    ![Запуск программы в режиме отладки](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/debug-add-watch-variable.png)

    В данном случае, так как приложение было остановлено до создания переменной `rdd1`, при помощи этого контрольного значения можно увидеть первые пять строк для переменной `rdd`. Нажмите клавишу **ВВОД**.

    ![Запуск программы в режиме отладки](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/debug-add-watch-variable-value.png)

    Как видно на рисунке выше, во время выполнения можно создавать запросы на терабайты данных и отлаживать работу приложения. Например, в результатах на рисунке выше можно увидеть, что первая строка выходных данных является заголовком. На основе этих данных можно изменить код приложения, если требуется пропустить строку заголовка.
5. Теперь можно щелкнуть значок **Resume Program** (Возобновить работу программы), чтобы продолжить выполнение приложения.

    ![Запуск программы в режиме отладки](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/debug-continue-run.png)
6. Если работа приложения завершится успешно, отобразится результат, подобный приведенному ниже.

    ![Запуск программы в режиме отладки](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/debug-complete.png)

## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>Демонстрация
* Создание проекта Scala (видео): [создание приложений Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Удаленная отладка (видео): [удаленная отладка приложений Spark в кластере HDInsight с помощью набора средств Azure для IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование средств HDInsight в наборе средств Azure для IntelliJ для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark через SSH с помощью набора средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование средств HDInsight в наборе средств Azure для Eclipse для создания приложений Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)

