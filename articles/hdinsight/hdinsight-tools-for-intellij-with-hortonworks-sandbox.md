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
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: d252a4a8b811f966098348866cc498e2be2924f2
ms.contentlocale: ru-ru
ms.lasthandoff: 07/17/2017

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
    - Средство сборки: мастер создания проекта Scala поддерживает Maven или SBT для управления зависимостями и сборки проекта Scala. Выберите одно из средств в соответствии с вашими задачами.
4. Введите следующие сведения:

    ![Создание свойств проекта IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)

    - **Project name** (Имя проекта): введите имя проекта.
    - **Project location** (Расположение проекта): введите расположение проекта.
    - **Project SDK** (Пакет SDK проекта): щелкните **New** (Создать), **JDK**, а затем укажите папку пакета Java JDK версии 7 или более поздней версии. Используйте Java 1.8 для кластера Spark 2.x или Java 1.7 для кластера Spark 1.x. Расположение по умолчанию: C:\Program Files\Java\jdk1.8.x_xxx.
    - **Spark Version** (Версия Spark). Мастер создания проекта Scala подставит правильную версию пакета SDK Spark и пакета SDK Scala. Если версия кластера Spark ниже, чем 2.0, выберите Spark 1.x. В противном случае следует выбрать Spark 2.x. В этом примере используется Spark 1.6.2 (Scala 2.10.5). Также убедитесь, что используемый репозиторий имеет обозначение Scala 2.10.x. Не используйте репозиторий, обозначенный как Scala 2.11.x.
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

## <a name="next-steps"></a>Дальнейшие действия:

- Сведения о создании приложений Spark для HDInsight с помощью инструментов HDInsight для IntelliJ см. в статье [Создание приложений Spark для кластера HDInsight Spark на платформе Linux с помощью средств HDInsight в наборе средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md).
- Просмотрите видеоролик [Introduce HDInsight Tools for IntelliJ for Spark Development](https://www.youtube.com/watch?v=YTZzYVgut6c) (Вводные сведения об инструментах HDInsight для IntelliJ для разработки Spark).
- Сведения об удаленной отладке приложений Spark в HDInsight по протоколу SSH см. в статье [Удаленная отладка приложений Spark в кластере HDInsight с помощью набора средств Azure для IntelliJ через SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md).
- Сведения об удаленной отладке приложений Spark через VPN с помощью набора средств в HDInsight см. в статье [Удаленная отладка приложений в HDInsight Spark через VPN с помощью набора средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).
- Сведения об использовании средств HDInsight для Eclipse для создания приложений Spark см. в статье [Использование HDInsight в наборе средств Azure для Eclipse для создания приложений Spark](hdinsight-apache-spark-eclipse-tool-plugin.md).
- Просмотрите видеоролик [Use HDInsight Tool for Eclipse to create Spark applications](https://mix.office.com/watch/1rau2mopb6fha) (Использование средств HDInsight для Eclipse для создания приложений Spark).
