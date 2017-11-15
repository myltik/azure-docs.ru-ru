---
title: "Средства Azure HDInsight — использование Visual Studio Code для Hive, LLAP или pySpark | Документы Майкрософт"
description: "Сведения о создании и отправке запросов и сценариев с помощью средств Azure HDInsight для Visual Studio Code."
Keywords: "VScode,средства Azure HDInsight,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query"
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: jgao
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 9c1d0e0520df30306c1647cf1f3ec86c8a4fd8f5
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2017
---
# <a name="use-azure-hdinsight-tool-for-visual-studio-code"></a>Использование средств Azure HDInsight для Visual Studio Code

Сведения об использовании средств Azure HDInsight для Visual Studio Code (VSCode) для создания и отправки пакетных заданий Hive, интерактивных запросов Hive и сценариев pySpark. Средства Azure HDInsight можно устанавливать на всех платформах, поддерживаемых VSCode, включая Windows, Linux и MacOS. Здесь описаны предварительные требования для различных платформ.


## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого руководства необходимы следующие элементы:

- Кластер HDInsight.  Сведения о создании кластера см. в статье о [начале работы с HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Моно требуется только для Linux и MacOS.

## <a name="install-the-hdinsight-tools"></a>Установка средств HDInsight
   
После установки всех необходимых компонентов можно переходить к установке средств Azure HDInsight для VSCode. 

**Установка средств Azure HDInsight**

1. Откройте **Visual Studio Code**.
2. Щелкните **Расширения** в левой области. В поле поиска введите **HDInsight**.
3. Щелкните **Установить** рядом с элементом **Средства Azure HDInsight**. Через несколько секунд вместо кнопки **Установить** появится кнопка **Перезагрузить**.
4. Нажмите кнопку **Перезагрузить** для активации расширения **Средства Azure HDInsight**.
5. Щелкните **Перезагрузить окно** для подтверждения. **Средства Azure HDInsight** появятся в области "Расширения".

   ![Установка расширения Python средств HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Откройте рабочую область HDInsight

Перед подключением к Azure необходимо создать рабочую область в VSCode.

**Переход к рабочей области**

1. В меню **Файл** щелкните **Открыть папку**, укажите существующую папку или создайте новую в качестве рабочей. Папка отображается в левой области.
2. В левой области щелкните значок **Создать файл** рядом с рабочей папкой.

   ![создание файла](./media/hdinsight-for-vscode/new-file.png)
3. Назовите новый файл, используя расширение HQL (запросы Hive) или PY (сценарий Spark). Обратите внимание, что файл конфигурации **XXXX_hdi_settings.json** автоматически добавляется в рабочую папку.
4. Откройте файл **XXXX_hdi_settings.json** с помощью **проводника** или щелкните правой кнопкой мыши редактор сценариев и выберите пункт **Задать конфигурацию**. Можно настроить запись для входа, кластер по умолчанию и параметры отправки заданий, как показано в примере в файле. Также можно оставить остальные параметры пустыми.

## <a name="connect-to-azure"></a>Подключение к Azure

Прежде чем отправлять сценарии в кластеры HDInsight из VSCode, необходимо подключиться к учетной записи Azure.

**Подключение к Azure**

1. Создайте рабочую папку и файл сценария, если их еще нет.
2. Щелкните правой кнопкой мыши редактор сценариев, а затем выберите **HDInsight: вход** в контекстном меню. Можно также нажать клавиши **CTRL+SHIFT+P** и ввести **HDInsight: вход**.

    ![Вход в средства HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)
3. Следуйте инструкциям по входу в области **вывода**.

    **Azure:** ![данные для входа в средства HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    После подключения в строке состояния в левом нижнем углу окна VSCode будет отображаться имя учетной записи Azure. 

    > [!NOTE]
    > Из-за известной проблемы с проверкой подлинности в Azure браузер следует открывать в режиме конфиденциальности или в режиме инкогнито. Если для учетной записи Azure используется двухфакторная проверка подлинности, мы рекомендуем выбрать проверку подлинности через телефон, а не ПИН-код.
  

4. Щелкните правой кнопкой мыши редактор сценариев, чтобы открыть контекстное меню. В контекстном меню можно выполнять следующие задачи:

    - logout
    - получение списка кластеров;
    - задание кластера по умолчанию;
    - отправка интерактивных запросов Hive;
    - отправка пакетного сценария Hive;
    - Отправка интерактивных запросов PySpark
    - отправка пакетного сценария PySpark;
    - задание конфигурации.

## <a name="list-hdinsight-clusters"></a>Получение списка кластеров HDInsight

Чтобы проверить подключение, можно составить список кластеров HDInsight.

**Получение списка кластеров HDInsight в подписке Azure**
1. Откройте рабочую область и подключитесь к Azure. См. разделы [Переход к рабочей области HDInsight](#open-hdinsight-workspace) и [Подключение к Azure](#connect-to-azure).
2. Щелкните правой кнопкой мыши редактор сценариев, а затем выберите **HDInsight: список кластеров** в контекстном меню. 
3. Кластеры Hive и Spark появятся в области **вывода**.

    ![задание конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Задание кластера по умолчанию
1. Откройте рабочую область и подключитесь к Azure. См. разделы [Переход к рабочей области HDInsight](#open-hdinsight-workspace) и [Подключение к Azure](#connect-to-azure).
2. Щелкните правой кнопкой мыши редактор сценариев и выберите пункт **HDInsight: задание кластера по умолчанию**. 
3. Выберите кластер в качестве кластера по умолчанию для текущего файла сценария. Средства автоматически обновят файл конфигурации **XXXX_hdi_settings.json**. 

   ![задание конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-azure-environment"></a>Задание среды Azure 
1. Откройте палитру команд, нажав клавиши **CTRL + SHIFT + P**.
2. Введите **HDInsight: задать среду Azure**.
3. Выберите один из вариантов (Azure или AzureChina) в качестве записи для входа по умолчанию.
4. Фактически средства уже сохранили выбранную по умолчанию запись для входа в файле **XXXX_hdi_settings.json**. Вы также можете изменить ее напрямую в файле конфигурации. 

   ![задание конфигурация записи для входа по умолчанию](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Отправка интерактивных запросов Hive

Средства HDInsight для VSCode дают возможность отправлять интерактивные запросы Hive в кластеры Interactive Query HDInsight.

1. Создайте рабочую папку и файл сценария Hive, если их еще нет.
2. Подключитесь к учетной записи Azure, а затем настройте кластер по умолчанию, если вы еще не сделали этого.
3. Скопируйте следующий код и вставьте его в файл Hive, а затем сохраните файл.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Щелкните правой кнопкой мыши редактор сценариев и выберите пункт **HDInsight: Hive Interactive** (HDInsight: интерактивный запрос Hive), чтобы отправить запрос. Средства также позволяют отправить блок кода вместо целого файла сценария с помощью контекстного меню. Вскоре после этого результат запроса будет выведен на новой вкладке:

   ![результат Interactive Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Область **результатов**: вы можете сохранить все результаты в виде файла CSV, JSON или Excel по локальному пути или просто выбрать несколько строк.
    - Область **сообщений**: щелкнув номер **строки**, можно перейти к нужной строке выполняемого сценария.

По сравнению с [выполнением пакетного задания Hive](#submit-hive-batch-scripts) выполнение интерактивного запроса занимает гораздо меньше времени.

## <a name="submit-hive-batch-scripts"></a>Отправка пакетных сценариев Hive

1. Создайте рабочую папку и файл сценария Hive, если их еще нет.
2. Подключитесь к учетной записи Azure, а затем настройте кластер по умолчанию, если вы еще не сделали этого.
3. Скопируйте следующий код и вставьте его в файл Hive, а затем сохраните файл.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Щелкните правой кнопкой мыши редактор сценариев и выберите пункт **HDInsight: Hive Batch** (HDInsight: пакет Hive), чтобы отправить задание Hive. 
4. Выберите кластер, в которой вы хотите отправить задание.  

    После отправки задания Hive сведения о состоянии отправки и идентификатор задания отображаются в области **вывода**. Открывается **веб-браузер**, в котором отображается состояние и журналы заданий в реальном времени.

   ![отправка результатов задания Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

По сравнению с [отправкой интерактивных запросов Hive](#submit-interactive-hive-queries) отправка задания занимает гораздо больше времени.

## <a name="submit-interactive-pyspark-queries"></a>Отправка интерактивных запросов PySpark
Средства HDInsight для VSCode также позволяют отправлять интерактивные запросы PySpark в кластеры Spark.
1. Создайте рабочую папку и файл сценария с разрешением PY, если их еще нет.
2. Подключитесь к учетной записи Azure, если вы еще этого не сделали.
3. Скопируйте следующий код и вставьте его в файл сценария:
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. Выделите эти сценарии и щелкните правой кнопкой мыши в редакторе сценариев, а затем выберите пункт **HDInsight: PySpark Interactive** (HDInsight: интерактивный запрос PySpark).
5. Нажмите показанную ниже кнопку **Установить**, если вы еще не установили расширение **Python** в VSCode.
    ![Установка расширения Python средств HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Настройте среду Python в своей системе, если она еще не установлена. 
   - Для Windows скачайте и установите [Python](https://www.python.org/downloads/). Затем убедитесь, что `Python` и `pip` добавлены в системную переменную PATH.
   - Инструкции по установке в MacOS и Linux см. в статье [Настройка интерактивной среды PySpark для Visual Studio Code](set-up-pyspark-interactive-environment.md).
7. Выберите кластер, чтобы отправить запрос PySpark. Вскоре после этого результат запроса отобразится на новой вкладке справа:

   ![отправка результата задания python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Наше средство также поддерживает запросы в виде **SQL-предложений**.

   ![Отправка результата задания Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) При выполнении запросов состояние отправки отображается слева в строке состояния внизу. Если отображается состояние **PySpark Kernel (busy)** (Ядро PySpark (занято)), вы не можете отправлять другие запросы. В противном случае средство перестанет отвечать на запросы.
9. Сеанс может сохраняться на наших кластерах. Например, если отображается **a=100**, этот сеанс уже сохранен на кластере. Вам нужно только запустить команду **print a** для кластера.
 

## <a name="submit-pyspark-batch-job"></a>Отправка пакетного задания PySpark

1. Создайте рабочую папку и файл сценария с разрешением PY, если их еще нет.
2. Подключитесь к учетной записи Azure, если вы еще этого не сделали.
3. Скопируйте следующий код и вставьте его в файл сценария:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. Щелкните правой кнопкой мыши редактор сценариев и выберите пункт **HDInsight: PySpark Batch** (HDInsight: пакет PySpark). 
5. Выберите кластер, чтобы отправить задание PySpark. 

   ![отправка результата задания python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

После отправки задания python журналы отправки отображаются в окне **вывода** в VSCode. Также отображаются **URL-адрес пользовательского интерфейса Spark** и **URL-адрес пользовательского интерфейса Yarn**. URL-адрес можно открыть в веб-браузере, чтобы отследить состояние задания.


## <a name="additional-features"></a>Дополнительные функции

HDInsight для VSCode поддерживает перечисленные ниже функции.

- **Автоматическое завершение IntelliSense**. Предлагаются подсказки для ключевых слов, методов, переменных и т. д. Разные значки обозначают объекты разных типов:

    ![Типы объектов IntelliSencse в средствах HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Маркер ошибки IntelliSense**. Языковая служба подчеркивает ошибки редактирования для сценария Hive.     
- **Подсветка синтаксиса**. Языковая служба выделяет разными цветами переменные, ключевые слова, типы данных, функции и т. д. 

    ![Подсветка синтаксиса в средствах HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Дальнейшие действия

### <a name="demo"></a>Демонстрация
* HDInsight для VScode: [видео](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Средства и расширения

* [Удаленная отладка приложений Spark через VPN с помощью набора средств Azure для IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Удаленная отладка приложений Spark через SSH с помощью набора средств Azure для IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование средств HDInsight в наборе средств Azure для Eclipse для создания приложений Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Визуализация данных Hive с помощью Microsoft Power BI в Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Настройка интерактивной среды PySpark для Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Выполнение запросов Hive в Azure HDInsight с помощью Zeppelin](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](spark/apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](spark/apache-spark-eventhub-streaming.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](spark/apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](spark/apache-spark-livy-rest-interface.md)

### <a name="managing-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](spark/apache-spark-job-debugging.md)



