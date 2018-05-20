---
title: Руководство. Создание приложения Scala Maven для Spark в HDInsight с помощью IntelliJ | Документация Майкрософт
description: Создайте приложение Spark, написанное на Scala, используя Apache Maven в качестве системы сборки и существующий архетип Maven для Scala, который предоставляется IntelliJ IDEA.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: c72f513c7134c556afa5fa5d0b94c17b1142be54
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-create-a-scala-maven-application-for-spark-in-hdinsight-using-intellij"></a>Руководство. Создание приложения Scala Maven для Spark в HDInsight с помощью IntelliJ

Из этого руководства вы узнаете, как создать приложение Spark на языке Scala в Maven с помощью IntelliJ IDEA. В качестве системы сборки в этой статье используется Apache Maven и изначально применяется существующий архетип Maven для Scala, который обеспечивает IntelliJ IDEA.  Создание приложения Scala в IntelliJ IDEA включает в себя следующие этапы:

* использование Maven в качестве системы сборки;
* обновление файла объектной модели проектов для разрешения зависимостей модуля Spark;
* написание приложения на языке Scala;
* создание JAR-файла, который можно отправить в кластеры HDInsight Spark;
* запуск приложений с помощью Livy в кластере Spark.

> [!NOTE]
> HDInsight также предоставляет подключаемый модуль IntelliJ IDEA для упрощения процесса создания и отправки приложений в кластер HDInsight Spark на платформе Linux. Дополнительные сведения см. в статье [Создание приложений Spark для кластера HDInsight Spark на платформе Linux с помощью средств HDInsight в наборе средств Azure для IntelliJ](apache-spark-intellij-tool-plugin.md).
> 

Из этого руководства вы узнаете, как выполнять такие задачи:
> [!div class="checklist"]
> * Разработка приложения Scala Maven с помощью IntelliJ.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.


## <a name="prerequisites"></a>предварительным требованиям

* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).
* Комплект разработчика Oracle Java. Его можно установить [отсюда](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Java IDE. В этой статье используется среда IntelliJ IDEA 18.1.1. Его можно установить [отсюда](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Установка подключаемого модуля Scala для IntelliJ IDEA
Чтобы установить подключаемый модуль Scala, сделайте следующее:

1. Откройте IntelliJ IDEA.
2. На экране приветствия выберите **Configure** (Настройка), а затем **Plugins** (Подключаемые модули).
   
    ![Включение подключаемого модуля Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
3. В левом нижнем углу выберите **Install JetBrains plugin** (Установить подключаемый модуль JetBrains). 
4. В диалоговом окне **Browse JetBrains Plugins** (Обзор подключаемых модулей JetBrains) найдите модуль **Scala** и выберите **Install** (Установить).
   
    ![Установка подключаемого модуля Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
5. После успешной установки подключаемого модуля необходимо перезапустить интегрированную среду разработки.

## <a name="create-a-standalone-scala-project"></a>Создание автономного проекта Scala
1. Откройте IntelliJ IDEA.
2. В меню **File** (Файл) выберите **New > Project** (Создать > Проект), чтобы создать проект.
3. В диалоговом окне нового проекта установите параметры, как на снимке экрана ниже.
   
    ![Создание проекта Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Выберите **Maven** в качестве типа проекта.
   * Выберите пакет в поле **Project SDK** (Пакет SDK проекта). Нажмите кнопку **New** (Создать) и перейдите к каталогу установки Java. Обычно у него такой путь: `C:\Program Files\Java\jdk1.8.0_66`.
   * Установите флажок **Create from archetype** («Создать на основе архетипа»).
   * В списке архетипов выберите **org.scala-tools.archetypes:scala-archetype-simple**. Этот архетип создаст структуру каталога и скачает зависимости по умолчанию, необходимые для написания программы Scala.
4. Щелкните **Далее**.
5. Введите соответствующие значения для параметров **GroupId**, **ArtifactId** и **Version**. В этом руководстве используются представленные ниже значения.

    - GroupId: com.microsoft.spark.example;
    - ArtifactId: SparkSimpleApp.
6. Щелкните **Далее**.
7. Проверьте параметры и нажмите кнопку **Next** (Далее).
8. Проверьте имя и расположение проекта, а затем выберите **Finish** (Готово).
9. В левой области выберите **src > test > scala > com > microsoft > spark > example**, щелкните правой кнопкой мыши **MySpec** и выберите **Delete** (Удалить). Этот файл не нужно использовать для приложения.
  
10. На следующих шагах описывается обновление файла pom.xml для определения зависимостей приложения Spark Scala. Чтобы автоматически скачать эти зависимости и разрешить их, необходимо соответствующим образом настроить Maven.
   
    ![Настройка автоматической загрузки Maven](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. В меню **File** (Файл) выберите **Settings** (Параметры).
   2. В диалоговом окне **Settings** (Настройки) выберите **Build, Execution, Deployment**(Сборка, выполнение, развертывание) > **Build Tools**(Средства сборки) > **Maven** > **Importing** (Импорт).
   3. Установите флажок **Import Maven projects automatically**(«Импортировать проекты Maven автоматически»).
   4. Нажмите кнопку **Apply** (Применить), а затем нажмите кнопку **ОК**.
11. В левой области выберите **src > main > scala > com.microsoft.spark.example**, а затем дважды щелкните **App** (Приложение), чтобы открыть App.scala.

12. Замените имеющийся пример кода кодом ниже, а затем сохраните изменения. Этот код считывает данные из файла HVAC.csv (доступного во всех кластерах HDInsight Spark), извлекает строки, которые содержат только одну цифру в шестом столбце, и записывает выходные данные в папку **/HVACOut** , которая находится в используемом по умолчанию контейнере хранилища кластера.

        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
13. В левой области дважды щелкните **pom.xml**.
   
   1. Добавьте следующие сегменты в файл `<project>\<properties>`:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. Добавьте следующие сегменты в файл `<project>\<dependencies>`:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Сохраните изменения в файле pom.xml.
10. Создайте JAR-файл. IntelliJ IDEA позволяет создавать JAR-файлы в качестве артефактов проекта. Выполните следующие действия.
    
    1. В меню **File** (Файл) выберите **Project Structure** (Структура проекта).
    2. В диалоговом окне **Project Structure** (Структура проекта) выберите **Artifacts** (Артефакты) и щелкните знак плюса. Во всплывающем диалоговом окне щелкните **JAR**, а затем выберите **From modules with dependencies** (На основе модулей с зависимостями).
       
        ![Создание JAR-файла](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. В диалоговом окне **Create JAR from Modules** (Создание JAR-файла на основе модулей) нажмите кнопку с многоточием (![многоточие](./media/apache-spark-create-standalone-application/ellipsis.png)) возле пункта **Main Class** (Основной класс).
    4. В диалоговом окне **Select Main Class** (Выбор основного класса) выберите класс, который отображается по умолчанию, и нажмите кнопку **ОК**.
       
        ![Создание JAR-файла](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. В диалоговом окне **Create JAR from Modules** (Создание JAR-файла на основе модулей) установите переключатель **extract to the target JAR** (Извлечь в целевой JAR-файл) и нажмите кнопку **ОК**.  В результате будет создан один JAR-файл, содержащий все зависимости.
       
        ![Создание JAR-файла](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. На вкладке "Макет выходных данных" содержится список всех JAR-файлов, которые включены в проект Maven. Здесь можно выбрать и удалить файлы, от которых не зависит работа приложения Scala. Для создаваемого приложения можно удалить все файлы, кроме последнего (**SparkSimpleApp compile output** (Выходные данные компиляции SparkSimpleApp)). Выберите JAR-файлы, которые нужно удалить, и щелкните значок **Delete** (Удалить).
       
        ![Создание JAR-файла](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Установите флажок **Include in project build** (Включить в сборку проекта), чтобы JAR-файл создавался при каждом создании и обновлении проекта. Нажмите кнопку **Apply** (Применить), а затем — **ОК**.
    7. В меню **Build** (Сборка) выберите **Build Artifacts** (Сборка артефактов), чтобы создать JAR-файл. Выходной JAR-файл будет создан в разделе **\out\artifacts**.
       
        ![Создание JAR-файла](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Запуск приложения в кластере Spark
Чтобы запустить приложение в кластере, можно использовать следующие методы:

* **Скопируйте приложение JAR в большой двоичный объект службы хранилища Azure**, связанный с кластером. Вы можете использовать для этого служебную программу командной строки [**AzCopy**](../../storage/common/storage-use-azcopy.md). Кроме того, для отправки данных можно использовать множество других клиентов. Дополнительные сведения о них см. в статье [Отправка данных для заданий Hadoop в HDInsight](../hdinsight-upload-data.md).
* **Используйте Livy для удаленной отправки задания приложения** для кластера Spark. В кластерах HDInsight Spark есть сервер Livy, который использует конечные точки REST для удаленной отправки заданий Spark. Дополнительные сведения см. в статье [Удаленная отправка заданий Spark в кластер Apache Spark в HDInsight на платформе Linux с помощью Livy](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Дальнейшие действия

В этой статье вы узнали, как создать приложение Spark Scala. Из следующей статьи вы узнаете, как запустить это приложение на кластере HDInsight Spark, используя Livy.

> [!div class="nextstepaction"]
>[Удаленный запуск заданий с помощью Livy в кластере Spark](./apache-spark-livy-rest-interface.md)

