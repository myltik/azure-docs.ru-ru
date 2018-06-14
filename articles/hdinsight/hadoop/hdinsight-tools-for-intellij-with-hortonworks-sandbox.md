---
title: Использование набора средств Azure для IntelliJ с песочницей Hortonworks | Документация Майкрософт
description: Узнайте, как использовать инструменты HDInsight в наборе средств Azure для IntelliJ с песочницей Hortonworks.
keywords: инструменты hadoop,запрос hive,intellij,песочница hortonworks,набор средств azure для intellij
services: HDInsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 59251287f7597955fe22700cc96527b9476753c1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202549"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks

Узнайте, как использовать средства HDInsight для IntelliJ при разработке приложений Apache Scala и их тестировании в [песочнице Hortonworks](http://hortonworks.com/products/sandbox/), запущенной на компьютере. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) — это интегрированная среда разработки (IDE) Java для создания программного обеспечения для компьютеров. После разработки и тестирования приложений в песочнице Hortonworks их можно переместить в [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>предварительным требованиям

Перед началом работы с этим руководством необходимо иметь следующее:

- Платформа данных Hortonworks Data Platform 2.4 (HDP) в песочнице Hortonworks, выполняемая на локальном компьютере. Сведения о настройке HDP см. в статье [Начало работы с песочницей Hadoop, эмулятором на виртуальной машине](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > Средства HDInsight для IntelliJ были протестированы только с HDP 2.4. Чтобы получить HDP 2.4, разверните **архив песочницы Hortonworks** на [сайте скачивания песочницы Hortonworks](http://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) версии 1.8 или более поздней версии](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Для набора средств Azure для IntelliJ требуется пакет JDK.

- [Выпуск IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download) с подключаемым модулем [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) и подключаемым модулем [Набор средств Azure для IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij). Средства HDInsight для IntelliJ доступны в составе набора средств Azure для IntelliJ. 

Чтобы установить подключаемые модули, сделайте следующее:

  1. Откройте IntelliJ IDEA.
  2. На **странице приветствия** выберите **Configure** (Настройка), а затем **Plugins** (Подключаемые модули).
  3. В левом нижнем углу выберите **Install JetBrains plugin** (Установить подключаемый модуль JetBrains).
  4. Используйте функцию поиска, чтобы найти **Scala**, а затем выберите **Install** (Установить).
  5. Щелкните **Restart IntelliJ IDEA** (Перезапустить IntelliJ IDEA), чтобы завершить установку.
  6. Чтобы установить **набор средств Azure для IntelliJ**, повторите шаги 4 и 5. Дополнительные сведения см. в статье [Установка набора средств Azure для IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-a-spark-scala-application"></a>Создание приложения Spark Scala

В этом разделе с помощью IntelliJ IDEA создается пример проекта Scala. В следующем разделе IntelliJ IDEA связывается с песочницей Hortonworks (эмулятором) до отправки проекта.

1. Откройте IntelliJ IDEA на компьютере. В диалоговом окне **New Project** (Новый проект) сделайте следующее:

   1. Выберите **HDInsight** > **Spark on HDInsight (Scala)** (Spark в HDInsight (Scala)).
   2. В списке **средств сборки** выберите один из следующих вариантов в зависимости от сценария:

    * **Maven.** Для поддержки мастера создания проекта Scala.
    * **SBT.** Для управления зависимостями и сборки проекта Scala.

   ![Диалоговое окно нового проекта](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Щелкните **Далее**.
3. В диалоговом окне **New Project** (Новый проект) выполните следующие шаги:

    1. В поле **Project name** (Имя проекта) введите имя проекта.
    2. В поле **Project location** (Расположение проекта) введите расположение проекта.
    3. Рядом с раскрывающимся списком **Project SDK** (Пакет SDK проекта) выберите **New** (Создать), а затем **JDK** и укажите папку пакета Java JDK версии 1.7 или более поздней. Выберите **Java 1.8** для кластера Spark 2.x. Для кластера Spark 1.x выберите **Java 1.7**. Расположение по умолчанию: C:\Program Files\Java\jdk1.8.x_xxx.
    4. В раскрывающемся списке **Spark version** (Версия Spark) мастер создания проекта Scala интегрирует правильную версию пакета SDK для Spark и пакета SDK для Scala. Если версия кластера Spark ниже 2.0, выберите **Spark 1.x**. В противном случае выберите **Spark 2.x**. В этом примере используется Spark 1.6.2 (Scala 2.10.5). Убедитесь, что используется репозиторий, помеченный как **Scala 2.10.x**. Не используйте репозиторий, помеченный Scala 2.11.x.
    
    ![Создание свойств проекта IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Выберите **Готово**.
5. Если представление **Project** (Проект) еще не открыто, нажмите клавиши **ALT+1**, чтобы открыть его.
6. В **обозревателе проектов** разверните проект и выберите **src**.
7. Щелкните правой кнопкой мыши **src**, наведите указатель мыши на пункт **New** (Создать), а затем щелкните **Scala Class** (Класс Scala).
8. В поле **Name** (Имя) введите имя. В поле **Kind** (Тип) выберите **Object** (Объект). Нажмите кнопку **ОК**.

    ![Диалоговое окно создания класса Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

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
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. В меню **Build** (Сборка) выберите **Build project** (Собрать проект). Убедитесь, что компиляция завершилась успешно.


## <a name="link-to-the-hortonworks-sandbox"></a>Связь с песочницей HortonWorks

Прежде чем устанавливать связь с песочницей Hortonworks (эмулятором), необходимо иметь приложение IntelliJ.

Чтобы установить связь с эмулятором, сделайте следующее:

1. Откройте проект в IntelliJ.
2. В меню **View** (Вид) выберите **Tool Windows** (Окна инструментов) и выберите **Azure Explorer**.
3. Разверните **Azure**, щелкните правой кнопкой мыши **HDInsight**, а затем выберите **Link an Emulator** (Установить связь с эмулятором).
4. В диалоговом окне **Link A New Emulator** (Установка связи с новым эмулятором) введите пароль, настроенный для учетной записи привилегированного пользователя песочницы Hortonworks. Затем введите значения, аналогичные приведенным на следующем снимке экрана. Нажмите кнопку **ОК**. 

   ![Диалоговое окно установки связи с новым эмулятором](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Чтобы настроить эмулятор, выберите **Yes** (Да).

При успешном подключении эмулятор (песочница Hortonworks) будет отображаться в списке узла HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Отправка приложения Spark Scala в песочницу Hortonworks

Когда среда IntelliJ IDEA связана с эмулятором, можно отправить проект.

Чтобы отправить проект в эмулятор, выполните следующие действия:

1. В **обозревателе проектов** щелкните проект правой кнопкой мыши и выберите **Submit Spark Application to HDInsight** (Отправить приложение Spark в HDInsight).
2. Затем сделайте следующее:

    1. В раскрывающемся списке **Spark cluster (Linux only)** (Кластер Spark (только для Linux)) выберите локальную песочницу Hortonworks.
    2. В поле **Main class name** (Имя класса Main) выберите или введите имя класса Main. В этом руководстве имя — **GroupByTest**.

3. Нажмите кнопку **Submit** (Отправить). Журналы отправки заданий отображаются в окне инструмента отправки Spark.

## <a name="next-steps"></a>Дополнительная информация

- Узнайте, как [создавать приложения Spark для кластера HDInsight Spark на платформе Linux с помощью средств HDInsight в наборе средств Azure для IntelliJ](../spark/apache-spark-intellij-tool-plugin.md).

- Просмотрите видео с [вводными сведениями о средствах HDInsight для IntelliJ для разработки приложений Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Узнайте, как [выполнить удаленную отладку приложений Spark в кластере HDInsight с помощью набора средств Azure для IntelliJ через SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Узнайте, как [выполнить удаленную отладку приложений Spark для кластера HDInsight Spark на платформе Linux с помощью средств HDInsight в наборе средств Azure для IntelliJ](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Узнайте, как [с помощью средств HDInsight в наборе средств Azure для Eclipse создавать приложения Spark](../spark/apache-spark-eclipse-tool-plugin.md).

- Просмотрите видео о [создании приложений Spark с помощью средств HDInsight для Eclipse ](https://mix.office.com/watch/1rau2mopb6fha).
