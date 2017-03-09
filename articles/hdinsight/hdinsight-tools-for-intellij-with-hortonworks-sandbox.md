---
title: "Использование набора средств Azure для IntelliJ с песочницей Hortonworks | Документация Майкрософт"
description: "Узнайте, как использовать инструменты HDInsight в наборе средств Azure для IntelliJ с песочницей Hortonworks."
keywords: "инструменты hadoop,запрос hive,intellij,песочница hortonworks,набор средств azure для intellij"
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/07/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: b52e5bcf443afd05bf8cc37ce436cb80ab41412f
ms.lasthandoff: 01/24/2017


---
#<a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks

Узнайте, как использовать инструменты HDInsight для IntelliJ при разработке приложений Apache Scala и их тестировании в [песочнице Hortonworks](http://hortonworks.com/products/sandbox/), запущенной на рабочей станции. [IntelliJ IDEA](https://www.jetbrains.com/idea/) — это интегрированная среда разработки (IDE) Java для разработки программного обеспечения для компьютеров. После разработки и тестирования приложений в песочнице Hortonworks их можно переместить в [Azure HDInsight](hdinsight-hadoop-introduction.md).

##<a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется:

- HDP 2.4 в песочнице Hortonworks, работающей в локальной среде. Сведения о настройке см. в статье [Начало работы в экосистеме Hadoop с песочницей Hadoop на виртуальной машине](hdinsight-hadoop-emulator-get-started.md). Обратите внимание, что инструменты HDInsight для IntelliJ были протестированы только с HDP 2.4. На [сайте скачивания песочницы Hortonworks](http://hortonworks.com/downloads/#sandbox) разверните раздел **Hortonworks Sandbox Archive** и скачайте необходимую версию.
- [Java Developer Kit (JDK) версии 1.8 или более поздней версии](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). JDK необходим для набора средств Azure для IntelliJ.
- [Выпуск IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download) с подключаемым модулем [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) и подключаемым модулем [Набор средств Azure для IntelliJ](../azure-toolkit-for-intellij.md). Инструменты HDInsight для IntelliJ доступны в составе набора средств Azure для IntelliJ. 

  **Чтобы установить подключаемые модули, выполните следующие действия:**

  1. Откройте IntelliJ IDEA.
  2. На экране приветствия щелкните **Configure** (Настройка), а затем — **Plugins** (Подключаемые модули).
  3. В левом нижнем углу щелкните **Install JetBrains plugin** (Установить подключаемый модуль JetBrains).
  4. С помощью функции поиска найдите **Scala**, а затем нажмите кнопку **Install** (Установить).
  5. Чтобы завершить установку, нажмите кнопку **Restart IntelliJ IDEA** (Перезапустить IntelliJ IDEA).
  6. Чтобы установить **набор средств Azure для IntelliJ**, повторите шаги 4 и 5. Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="create-a-spark-scala-application"></a>Создание приложения Spark Scala

В этом разделе с помощью IntelliJ IDEA создается пример проекта Scala. В следующем разделе IntelliJ IDEA связывается с песочницей Hortonworks (эмулятором) до отправки проекта.

1. Откройте IntelliJ IDEA со своей рабочей станции.
2. Щелкните **Create New Project** (Создать проект).
3. В левой области щелкните **HDInsight**, выберите **Spark on HDInsight(Scala)** (Spark в HDInsight (Scala)), а затем нажмите кнопку **Next** (Далее).

    ![Создание проекта IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)
4. Введите следующие сведения:

    ![Создание свойств проекта IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)

    - **Project name** (Имя проекта): введите имя проекта.
    - **Project location** (Расположение проекта): введите расположение проекта.
    - **Project SDK** (Пакет SDK проекта): щелкните **New** (Создать), **JDK**, а затем укажите папку пакета Java JDK версии 7 или более поздней версии.  Расположение по умолчанию: C:\Program Files\Java\jdk1.8.x_xxx.
    - **Scala SDK** (Пакет SDK для Scala ): щелкните **Select** (Выбрать), выберите версию **2.10.6**, а затем нажмите кнопку **ОК**. Если такой версии нет в списке, то щелкните **Download** (Скачать), выберите **Scala version** (Версия Scala) и нажмите кнопку **ОК**. Не используйте версию 2.11.x. В этой статье используется версия 2.10.6.
    - **Spark SDK** (Пакет SDK для Spark): скачайте [пакет SDK](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Кроме того, можно использовать репозиторий Spark Maven, однако убедитесь, что у вас установлен нужный репозиторий Maven для разработки приложений Spark. (Например, при использовании потоковой передачи Spark необходимо убедиться, что установлена часть Spark Streaming. Кроме того, убедитесь, что используется репозиторий, помеченный как Scala 2.10, — не используйте репозиторий, помеченный как Scala 2.11).
5. Нажмите кнопку **Готово**
6. Нажмите клавиши **[ALT]+1**, чтобы открыть представление проекта, если оно не открыто.
7. Из **обозревателя проектов** разверните проект и щелкните **src**.
8. Щелкните правой кнопкой мыши **src**, наведите указатель мыши на пункт **New** (Создать), а затем щелкните **Scala Class** (Класс Scala).
9. Введите имя, в поле **Kind** (Вид) выберите **Object** (Объект), а затем нажмите кнопку **ОК**.

    ![Создание класса IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)
10. Скопируйте приведенный ниже код и вставьте его в файл с расширением SCALA.

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

11. В меню **Build** (Сборка) выберите **Build project** (Собрать проект). Дождитесь успешного завершения компиляции.


## <a name="link-to-the-hortonworks-sandbox"></a>Связь с песочницей HortonWorks

Прежде чем устанавливать связь с песочницей Hortonworks (эмулятором), необходимо иметь приложение IntelliJ.

**Чтобы установить связь с эмулятором, выполните следующие действия:**

1. Откройте проект в IntelliJ, если он еще не открыт.
2. В меню **View** (Вид) щелкните **Tools Windows** (Окна инструментов) и выберите **Azure Explorer**.
3. Разверните пункт **Azure**, щелкните правой кнопкой мыши **HDInsight**, а затем щелкните **Link an Emulator** (Установить связь с эмулятором).
4. Введите пароль, настроенный для учетной записи root песочницы Hortonworks, и остальные значения, как показано на следующем снимке экрана, а затем нажмите кнопку **ОК**. 

  ![Связь IntelliJ с эмулятором](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Чтобы настроить эмулятор, нажмите кнопку **Yes** (Да).

  Когда подключение успешно установлено, эмулятор (песочница Hortonworks) отображается в списке узла HDInsight.

## <a name="submit-the-spark-scala-application-to-the-sandbox"></a>Отправка приложения Spark Scala в песочницу

Когда среда IntelliJ IDEA связана с эмулятором, можно отправить проект.

**Чтобы отправить проект в эмулятор, выполните следующие действия:**

1. В **обозревателе проектов** щелкните проект правой кнопкой мыши и выберите **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).
2. Укажите следующие значения полей:

    - **Spark cluster (Linux only)** (Кластер Spark (только для Linux)): выберите локальную песочницу Hortonworks.
    - **Main class name** (Имя класса Main): выберите или введите имя класса Main.  Для работы с этим руководством выберите **GroupByTest**.
3. Нажмите кнопку **Submit**(Отправить). Журналы отправки заданий отображаются в окне инструмента отправки Spark.

##<a name="next-steps"></a>Дальнейшие действия:

- Сведения о создании приложений Spark для HDInsight с помощью инструментов HDInsight для IntelliJ см. в статье [Создание приложений Spark для кластера HDInsight Spark на платформе Linux с помощью средств HDInsight в наборе средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md).
- Просмотрите видеоролик [Introduce HDInsight Tools for IntelliJ for Spark Development](https://mix.office.com/watch/1nqkqjt5xonza) (Вводные сведения об инструментах HDInsight для IntelliJ для разработки Spark).
- Сведения об удаленной отладке приложений Spark с помощью набора средств в HDInsight см. в статье [Удаленная отладка приложений Spark в кластере HDInsight Spark на платформе Linux с помощью средств HDInsight в наборе средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).
- Сведения об использовании средств HDInsight для Eclipse для создания приложений Spark см. в статье [Использование HDInsight в наборе средств Azure для Eclipse для создания приложений Spark](hdinsight-apache-spark-eclipse-tool-plugin.md).
- Просмотрите видеоролик [Use HDInsight Tool for Eclipse to create Spark applications](https://mix.office.com/watch/1rau2mopb6fha) (Использование средств HDInsight для Eclipse для создания приложений Spark).
