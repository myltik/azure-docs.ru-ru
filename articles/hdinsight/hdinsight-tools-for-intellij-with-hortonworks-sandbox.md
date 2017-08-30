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
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: c49f185db5a035f70a711bf309b973182d94a2b0
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks

Узнайте, как использовать инструменты HDInsight для IntelliJ при разработке приложений Apache Scala и их тестировании в [песочнице Hortonworks](http://hortonworks.com/products/sandbox/), запущенной на рабочей станции. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) — это интегрированная среда разработки (IDE) Java для разработки программного обеспечения для компьютеров. После разработки и тестирования приложений в песочнице Hortonworks их можно переместить в [Azure HDInsight](hdinsight-hadoop-introduction.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется:

- Платформа данных Hortonworks Data Platform 2.4 (HDP) в песочнице Hortonworks, выполняемая в локальной среде. Сведения о настройке см. в статье [Начало работы с песочницей Hadoop, эмулятором на виртуальной машине](hdinsight-hadoop-emulator-get-started.md). 
    >[!NOTE]
    >Средства HDInsight для IntelliJ были протестированы только с HDP 2.4. Чтобы получить HDP 2.4, разверните **архив песочницы Hortonworks** на [сайте скачивания песочницы Hortonworks](http://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) версии 1.8 или более поздней версии](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). JDK необходим для набора средств Azure для IntelliJ.

- [Выпуск IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download) с подключаемым модулем [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) и подключаемым модулем [Набор средств Azure для IntelliJ](../azure-toolkit-for-intellij.md). Средства HDInsight для IntelliJ доступны в составе набора средств Azure для IntelliJ. 

  Чтобы установить подключаемые модули, выполните такие действия:

  1. Откройте IntelliJ IDEA.
  2. На экране **Welcome** (Добро пожаловать) выберите **Configure** (Настройка), а затем **Plugins** (Подключаемые модули).
  3. Выберите **Install JetBrains plugin** (Установить подключаемый модуль JetBrains) в левом нижнем углу.
  4. Используйте функцию поиска, чтобы найти **Scala**, а затем выберите **Install** (Установить).
  5. Чтобы завершить установку, щелкните **Restart IntelliJ IDEA** (Перезапустить IntelliJ IDEA).
  6. Чтобы установить **набор средств Azure для IntelliJ**, повторите шаги 4 и 5. Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="create-a-spark-scala-application"></a>Создание приложения Spark Scala

В этом разделе с помощью IntelliJ IDEA создается пример проекта Scala. В следующем разделе IntelliJ IDEA связывается с песочницей Hortonworks (эмулятором) до отправки проекта.

1. Откройте IntelliJ IDEA со своей рабочей станции. В диалоговом окне **Новый проект** сделайте следующее:

   а. Выберите **HDInsight** > **Spark on HDInsight (Scala)** (Spark в HDInsight (Scala)).

   b. В списке **средств сборки** выберите один из следующих вариантов:

    * **Maven.** Для поддержки мастера создания проекта Scala.
    * **SBT.** Для управления зависимостями и создания проекта Scala.

   ![Диалоговое окно нового проекта](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Щелкните **Далее**.

3. В диалоговом окне **New Project** (Новый проект) сделайте следующее:

    ![Создание свойств проекта IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)

    а. В поле **Project name** (Имя проекта) введите имя проекта.

    b. В поле **Project location** (Расположение проекта) введите расположение проекта.

    c. Рядом с раскрывающимся списком **Project SDK** (Пакет SDK проекта) выберите **New** (Создать), а затем **JDK** и укажите папку пакета Java JDK версии 1.7 или более поздней. Выберите **Java 1.8** для кластера Spark 2.x или **Java 1.7** для кластера Spark 1.x. Расположение по умолчанию: C:\Program Files\Java\jdk1.8.x_xxx.

    d. В раскрывающемся списке для параметра **Spark version** (Версия Spark) мастер создания проекта Scala интегрирует правильную версию пакета SDK для Spark и пакета SDK для Scala. Если версия кластера Spark ниже 2.0, выберите **Spark 1.x**. В противном случае выберите **Spark 2.x**. В этом примере используется Spark 1.6.2 (Scala 2.10.5). Убедитесь, что используется репозиторий, помеченный Scala 2.10.x. Не используйте репозиторий, помеченный Scala 2.11.x.

4. Выберите **Готово**.

5. Если представление **Project** (Проект) еще не открыто, нажмите клавиши **ALT+1**, чтобы открыть его.

6. В **обозревателе проектов** разверните проект и выберите **src**.

7. Щелкните правой кнопкой мыши **src**, наведите указатель мыши на пункт **New** (Создать), а затем щелкните **Scala Class** (Класс Scala).

8. В поле **Name** (Имя) введите имя, а в поле **Kind** (Тип) выберите **Object** (Объект), а затем нажмите кнопку **ОК**.

    ![Окно создания нового класса Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. Скопируйте приведенный ниже код и вставьте его в файл с расширением SCALA.

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

10. В меню **Build** (Сборка) выберите **Build project** (Собрать проект). Убедитесь, что компиляция завершилась успешно.


## <a name="link-to-the-hortonworks-sandbox"></a>Связь с песочницей HortonWorks

Прежде чем устанавливать связь с песочницей Hortonworks (эмулятором), необходимо иметь приложение IntelliJ.

Чтобы установить связь с эмулятором, выполните следующее:

1. Откройте проект в IntelliJ.

2. В меню **View** (Вид) выберите **Tool Windows** (Окна инструментов) и выберите **Azure Explorer**.

3. Разверните **Azure**, щелкните правой кнопкой мыши **HDInsight**, а затем выберите **Link an Emulator** (Установить связь с эмулятором).

4. В окне **Link A New Emulator** (Установить связь с новым эмулятором) введите пароль, который был настроен для учетной записи привилегированного пользователя песочницы Hortonworks, введите значения, аналогичные приведенным на следующем снимке экрана, а затем нажмите кнопку **OK**. 

   ![Окно Link a New Emulator (Установить связь с новым эмулятором)](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Чтобы настроить эмулятор, выберите **Yes** (Да).

При успешном подключении эмулятор (песочница Hortonworks) будет внесен в узел HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Отправка приложения Spark Scala в песочницу Hortonworks

Когда среда IntelliJ IDEA связана с эмулятором, можно отправить проект.

Чтобы отправить проект на эмулятор, выполните следующее:

1. В **обозревателе проектов** щелкните проект правой кнопкой мыши и выберите **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).

2. Выполните следующее:

    а. В раскрывающемся списке **Spark cluster (Linux only)** (Кластер Spark (только для Linux)) выберите локальную песочницу Hortonworks.

    b. В поле **Main class name** (Имя класса Main) введите имя класса Main. В этом руководстве имя — **GroupByTest**.

3. Нажмите кнопку **Submit** (Отправить). Журналы отправки заданий отображаются в окне инструмента отправки Spark.

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о создании приложений Spark для HDInsight с помощью инструментов HDInsight для IntelliJ см. в статье [Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md).

- Просмотрите видеоролик [Introduce HDInsight Tools for IntelliJ for Spark Development](https://www.youtube.com/watch?v=YTZzYVgut6c) (Вводные сведения об инструментах HDInsight для IntelliJ для разработки Spark).

- Сведения об удаленной отладке приложений Spark в HDInsight по протоколу SSH см. в статье [Удаленная отладка приложений Spark в кластере HDInsight с помощью набора средств Azure для IntelliJ через SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Сведения об удаленной отладке приложений Spark через VPN с помощью набора средств в HDInsight см. в статье [Удаленная отладка приложений в HDInsight Spark через VPN с помощью набора средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Сведения об использовании средств HDInsight для Eclipse для создания приложений Spark см. в статье [Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md).

- Просмотрите видеоролик [Use HDInsight Tool for Eclipse to create Spark applications](https://mix.office.com/watch/1rau2mopb6fha) (Использование средств HDInsight для Eclipse для создания приложений Spark).

