---
title: Создание приложения Scala для работы в кластерах Spark — Azure HDInsight | Документы Майкрософт
description: Создайте приложение Spark, написанное на Scala, используя Apache Maven в качестве системы сборки и существующий архетип Maven для Scala, который предоставляется IntelliJ IDEA.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: d64d60112641992a5a56ee5d129e7cbe754bde9d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-scala-maven-application-to-run-on-apache-spark-cluster-on-hdinsight"></a>Создание приложения Scala в Maven для работы в кластере Apache Spark в HDInsight

Узнайте, как создать приложение Spark на языке Scala в Maven с помощью IntelliJ IDEA. В качестве системы сборки в этой статье используется Apache Maven и изначально применяется существующий архетип Maven для Scala, который обеспечивает IntelliJ IDEA.  Создание приложения Scala в IntelliJ IDEA включает в себя следующие этапы:

* использование Maven в качестве системы сборки;
* обновление файла объектной модели проектов для разрешения зависимостей модуля Spark;
* написание приложения на языке Scala;
* создание JAR-файла, который можно отправить в кластеры HDInsight Spark;
* запуск приложений с помощью Livy в кластере Spark.

> [!NOTE]
> HDInsight также предоставляет подключаемый модуль IntelliJ IDEA для упрощения процесса создания и отправки приложений в кластер HDInsight Spark на платформе Linux. Дополнительные сведения см. в статье [Создание приложений Spark для кластера HDInsight Spark на платформе Linux с помощью средств HDInsight в наборе средств Azure для IntelliJ](apache-spark-intellij-tool-plugin.md).
> 
> 

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).
* Комплект разработчика Oracle Java. Его можно установить [отсюда](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Java IDE. В этой статье используется среда IntelliJ IDEA 15.0.1. Его можно установить [отсюда](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Установка подключаемого модуля Scala для IntelliJ IDEA
Если во время установки IntelliJ IDEA вы не получили запрос на включение подключаемого модуля Scala, запустите IntelliJ IDEA и выполните следующие действия, чтобы установить его:

1. Запустите IntelliJ IDEA и на экране приветствия щелкните **Configure** (Настройка), а затем — **Plugins** (Подключаемые модули).
   
    ![Включение подключаемого модуля Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
2. На следующем экране в левом нижнем углу щелкните **Install JetBrains plugin** («Установить подключаемый модуль JetBrains»). В открывшемся диалоговом окне **Browse JetBrains Plugins** (Обзор подключаемых модулей JetBrains) найдите модуль Scala и нажмите кнопку **Install** (Установить).
   
    ![Установка подключаемого модуля Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
3. После успешной установки подключаемого модуля нажмите кнопку **Restart IntelliJ IDEA** («Перезапустить IntelliJ IDEA»), чтобы перезапустить IDE.

## <a name="create-a-standalone-scala-project"></a>Создание автономного проекта Scala
1. Запустите IntelliJ IDEA и создайте проект. В диалоговом окне нового проекта установите параметры, как на снимке экрана ниже, а затем нажмите кнопку **Next**(«Далее»).
   
    ![Создание проекта Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Выберите **Maven** в качестве типа проекта.
   * Выберите пакет в поле **Project SDK** (Пакет SDK проекта). Нажмите кнопку New («Создать») и перейдите к каталогу установки Java. Обычно у него такой путь: `C:\Program Files\Java\jdk1.8.0_66`.
   * Установите флажок **Create from archetype** («Создать на основе архетипа»).
   * В списке архетипов выберите **org.scala-tools.archetypes:scala-archetype-simple**. В результате будет создана структура каталога и скачаны зависимости по умолчанию, необходимые для написания программы Scala.
2. Введите соответствующие значения для параметров **GroupId**, **ArtifactId** и **Version**. Нажмите кнопку **Далее**.
3. В следующем диалоговом окне, где нужно указать основной каталог Maven и другие пользовательские настройки, примите значения по умолчанию и нажмите кнопку **Next**(«Далее»).
4. В последнем диалоговом окне укажите имя и расположение проекта, а затем нажмите кнопку **Finish**(«Готово»).
5. Удалите файл **MySpec.Scala** с путем **src\test\scala\com\microsoft\spark\example**. Его не нужно использовать для приложения.
6. При необходимости переименуйте исходные файлы и файлы тестирования по умолчанию. В левой области в IntelliJ IDEA перейдите по пути **src\main\scala\com.microsoft.spark.example**. Щелкните правой кнопкой мыши **App.scala**, выберите пункт **Refactor** (Рефакторинг), щелкните Rename file (Переименовать файл), а затем в диалоговом окне укажите новое имя для приложения и нажмите кнопку **Refactor** (Выполнить рефакторинг).
   
    ![Переименование файлов](./media/apache-spark-create-standalone-application/rename-scala-files.png)  
7. На следующих шагах описывается обновление файла pom.xml для определения зависимостей приложения Spark Scala. Чтобы автоматически скачать эти зависимости и разрешить их, необходимо соответствующим образом настроить Maven.
   
    ![Настройка автоматической загрузки Maven](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. В меню **File** (Файл) выберите пункт **Settings** (Настройки).
   2. В диалоговом окне **Settings** (Настройки) выберите **Build, Execution, Deployment**(Сборка, выполнение, развертывание) > **Build Tools**(Средства сборки) > **Maven** > **Importing** (Импорт).
   3. Установите флажок **Import Maven projects automatically**(«Импортировать проекты Maven автоматически»).
   4. Нажмите кнопку **Применить**, а затем — **ОК**.
8. Обновите исходный файл Scala, чтобы включить его данные в код приложения. Откройте и замените существующий пример кода кодом ниже, а затем сохраните изменения. Этот код считывает данные из файла HVAC.csv (доступного во всех кластерах HDInsight Spark), извлекает строки, которые содержат только одну цифру в шестом столбце, и записывает выходные данные в папку **/HVACOut** , которая находится в используемом по умолчанию контейнере хранилища кластера.
   
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
9. Обновите файл pom.xml.
   
   1. Добавьте следующий код в файл `<project>\<properties>`:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. Добавьте следующий код в файл `<project>\<dependencies>`:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Сохраните изменения в файле pom.xml.
10. Создайте JAR-файл. IntelliJ IDEA позволяет создавать JAR-файлы в качестве артефактов проекта. Выполните следующие действия.
    
    1. В меню **File** (Файл) выберите пункт **Project Structure** (Структура проекта).
    2. В диалоговом окне **Project Structure** (Структура проекта) выберите **Artifacts** (Артефакты) и щелкните знак плюса. Во всплывающем диалоговом окне щелкните **JAR**, а затем выберите **From modules with dependencies** (На основе модулей с зависимостями).
       
        ![Создание JAR-файла](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. В диалоговом окне **Create JAR from Modules** (Создание JAR-файла на основе модулей) нажмите кнопку с многоточием (![многоточие](./media/apache-spark-create-standalone-application/ellipsis.png)) возле пункта **Main Class** (Основной класс).
    4. В диалоговом окне **Select Main Class** (Выбор основного класса) выберите класс, который отображается по умолчанию, и нажмите кнопку **ОК**.
       
        ![Создание JAR-файла](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. В диалоговом окне **Create JAR from Modules** (Создание JAR-файла на основе модулей) установите переключатель **extract to the target JAR** (Извлечь в целевой JAR-файл) и нажмите кнопку **ОК**. В результате будет создан один JAR-файл, содержащий все зависимости.
       
        ![Создание JAR-файла](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. На вкладке "Макет выходных данных" содержится список всех JAR-файлов, которые включены в проект Maven. Здесь можно выбрать и удалить файлы, от которых не зависит работа приложения Scala. Для создаваемого приложения можно удалить все файлы, кроме последнего (**SparkSimpleApp compile output**(«Выходные данные компиляции SparkSimpleApp»)). Выберите JAR-файлы, которые нужно удалить, и щелкните значок **Delete** («Удалить»).
       
        ![Создание JAR-файла](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Установите флажок **Build on make** («Сборка на основе созданного»), чтобы JAR-файл создавался при каждом создании и обновлении проекта. Нажмите кнопку **Применить**, а затем — **ОК**.
    7. В строке меню щелкните **Build** (Сборка) и выберите **Make Project** (Создать проект). Кроме того, можно щелкнуть **Build Artifacts** («Сборка артефактов»), чтобы создать JAR-файл. Выходной JAR-файл будет создан в разделе **\out\artifacts**.
       
        ![Создание JAR-файла](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Запуск приложения в кластере Spark
Чтобы запустить приложение в кластере, нужно выполнить следующее:

* **Скопируйте приложение JAR в большой двоичный объект службы хранилища Azure**, связанный с кластером. Вы можете использовать для этого служебную программу командной строки [**AzCopy**](../../storage/common/storage-use-azcopy.md). Кроме того, для отправки данных можно использовать множество других клиентов. Дополнительные сведения о них см. в статье [Отправка данных для заданий Hadoop в HDInsight](../hdinsight-upload-data.md).
* **Используйте Livy для удаленной отправки задания приложения** для кластера Spark. В кластерах HDInsight Spark есть сервер Livy, который использует конечные точки REST для удаленной отправки заданий Spark. Дополнительные сведения см. в статье [Удаленная отправка заданий Spark в кластер Apache Spark в HDInsight на платформе Linux с помощью Livy](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Дальнейшие действия

В этой статье вы узнали, как создать приложение Spark Scala. Из следующей статьи вы узнаете, как запустить это приложение на кластере HDInsight Spark, используя Livy.

> [!div class="nextstepaction"]
>[Удаленный запуск заданий с помощью Livy в кластере Spark](apache-spark-livy-rest-interface.md)

