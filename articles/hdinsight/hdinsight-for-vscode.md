---
title: Средства Azure HDInsight — использование Visual Studio Code для Hive, LLAP или pySpark | Документы Майкрософт
description: Сведения о создании и отправке запросов и скриптов с помощью средств Azure HDInsight для Visual Studio Code.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
manager: ''
editor: jgao
tags: azure-portal
ms.assetid: ''
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: e8dc802d67b4cd2e38ab195b771ceeaa07876e58
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31595309"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Использование средств Azure HDInsight для Visual Studio Code

Сведения об использовании средств Azure HDInsight для Visual Studio Code (VS Code) для создания и отправки пакетных заданий Hive, интерактивных запросов Hive и скриптов pySpark. Средства Azure HDInsight можно устанавливать на всех платформах, поддерживаемых VS Code. К ним относятся Windows, Linux и macOS. Здесь описаны предварительные требования для различных платформ.


## <a name="prerequisites"></a>предварительным требованиям

Для выполнения действий в этом руководстве необходимы такие компоненты:

- Кластер HDInsight. Сведения о создании кластера см. в статье о [начале работы с HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono требуется только для Linux и macOS.

## <a name="install-the-hdinsight-tools"></a>Установка средств HDInsight
   
После установки всех необходимых компонентов можно переходить к установке средств Azure HDInsight для VS Code. 

**Установка средств Azure HDInsight**

1. Откройте Visual Studio Code.

2. В левой области выберите **Extensions** (Расширения). В поле поиска введите **HDInsight**.

3. Рядом с элементом **Azure HDInsight tools** (Средства Azure HDInsight) нажмите кнопку **Установить**. Через несколько секунд кнопка **Установить** изменится на **Перезагрузить**.

4. Выберите **Перезагрузить** для активации расширения **Средства Azure HDInsight**.

5. Выберите **Перезагрузить окно** для подтверждения. **Средства Azure HDInsight** появятся в области **Extensions** (Расширения).

   ![Установка расширения Python средств HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Откройте рабочую область HDInsight

Перед подключением к Azure создайте рабочую область в VS Code.

**Переход к рабочей области**

1. В меню **Файл** выберите **Открыть папку**. Затем назначьте имеющуюся папку в качестве рабочей или создайте папку. Папка появится в левой области.

2. В левой области выберите значок **Создать файл** рядом с рабочей папкой.

   ![Создание файла](./media/hdinsight-for-vscode/new-file.png)

3. Назовите новый файл, используя расширение HQL (запросы Hive) или PY (сценарий Spark). Обратите внимание, что файл конфигурации **XXXX_hdi_settings.json** автоматически добавляется в рабочую папку.

4. Откройте файл **XXXX_hdi_settings.json** с помощью **проводника** или щелкните правой кнопкой мыши редактор скриптов и выберите пункт **Задать конфигурацию**. Вы можете настроить запись для входа, кластер по умолчанию и параметры отправки заданий, как показано в примере в файле. Также можно оставить остальные параметры пустыми.

## <a name="connect-to-hdinsight-cluster"></a>Подключение к кластеру HDInsight

Прежде чем отправлять скрипты для кластеров HDInsight из VS Code, необходимо подключиться к учетной записи Azure либо связать кластер (с помощью имени пользователя и пароля Ambari или учетной записи присоединения к домену).

**Подключение к Azure**

1. Создайте рабочую папку и файл сценария, если у вас их еще нет.

2. Щелкните редактор сценариев правой кнопкой мыши, а затем выберите **HDInsight: Login** (HDInsight: вход) в контекстном меню. Вы также можете ввести **Ctrl+Shift+P**, а затем — **HDInsight: вход**.

    ![Вход в средства HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Чтобы войти, следуйте инструкциям на панели **вывода**.

    **Azure:** ![данные для входа в средства HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    После подключения имя учетной записи Azure будет отображаться в строке состояния в левом нижнем углу окна VS Code. 

    > [!NOTE]
    > Из-за известной проблемы с проверкой подлинности в Azure браузер следует открывать в режиме конфиденциальности или в режиме инкогнито. Если для учетной записи Azure используется двухфакторная проверка подлинности, мы рекомендуем выбрать проверку подлинности через телефон, а не ПИН-код.
  

4. Щелкните редактор скриптов правой кнопкой мыши, чтобы открыть контекстное меню. В контекстном меню можно выполнять следующие задачи:

    - выход из системы;
    - Получение списка кластеров
    - настройка кластеров по умолчанию;
    - Отправка интерактивных запросов Hive
    - Отправка пакетных сценариев Hive
    - Отправка интерактивных запросов PySpark
    - отправка пакетных сценариев PySpark;
    - настройка конфигурации.

<a id="linkcluster"></a>**Связывание кластера**

Вы можете связать обычный кластер с помощью управляемого имени пользователя Ambari, а кластер безопасности — с помощью имени пользователя домена (например, user1@contoso.com).
1. Откройте палитру команд, нажав **CTRL+SHIFT+P** и введите **HDInsight: Link a cluster** (HDInsight: связать кластер).

   ![команда связывания кластера](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Введите URL-адрес кластера HDInsight, имя пользователя и пароль и выберите тип кластера. Если проверка пройдена, отобразится сообщение об успешном выполнении.
   
   ![диалоговое окно связывания кластера](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > Если кластер зарегистрирован в подписке Azure и связан, используется имя пользователя и пароль для связывания. 
   
3. Данные связанного кластера можно просмотреть с помощью команды **перечисления кластеров**. Теперь в этот связанный кластер можно отправить скрипт.

   ![связанный кластер](./media/hdinsight-for-vscode/linked-cluster.png)

4. Также можно удалить связь кластера. Для этого введите **HDInsight: Unlink a cluster** (HDInsight: удалить связь кластера) в палитре команд.

## <a name="list-hdinsight-clusters"></a>Получение списка кластеров HDInsight

Чтобы проверить подключение, можно составить список кластеров HDInsight.

**Получение списка кластеров HDInsight в подписке Azure**
1. Откройте рабочую область, а затем подключитесь к Azure. Дополнительную информацию см. в разделе [Переход к рабочей области HDInsight](#open-hdinsight-workspace) и [Подключение к Azure](#connect-to-azure).

2. Щелкните правой кнопкой мыши редактор сценариев, а затем выберите **HDInsight: список кластеров** в контекстном меню. 

3. Кластеры Hive и Spark появятся в области **вывода**.

    ![Установка конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Установка кластера по умолчанию
1. Откройте рабочую область и подключитесь к Azure. См. разделы [Переход к рабочей области HDInsight](#open-hdinsight-workspace) и [Подключение к Azure](#connect-to-azure).

2. Щелкните редактор скриптов правой кнопкой мыши и выберите **HDInsight: Set Default Cluster** (HDInsight: задание кластера по умолчанию). 

3. Выберите кластер в качестве кластера по умолчанию для текущего файла сценария. Средства автоматически обновят файл конфигурации **XXXX_hdi_settings.json**. 

   ![Задание конфигурации кластера по умолчанию](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Настройка среды Azure 
1. Откройте палитру команд, нажав клавиши **CTRL+SHIFT+P**.

2. Введите **HDInsight: задать среду Azure**.

3. Выберите один из вариантов (Azure или AzureChina) в качестве записи для входа по умолчанию.

4. В свою очередь, средство уже сохранило запись для входа по умолчанию в файле **XXXX_hdi_settings.json**. Вы также можете изменить ее напрямую в файле конфигурации. 

   ![Задание конфигурации записи для входа по умолчанию](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Отправка интерактивных запросов Hive

С помощью Средств HDInsight для VS Code вы можете отправлять интерактивные запросы Hive в кластеры интерактивных запросов HDInsight.

1. Создайте рабочую папку и файл сценария Hive, если у вас их еще нет.

2. Подключитесь к учетной записи Azure, а затем настройте кластер по умолчанию, если вы еще этого не сделали.

3. Скопируйте следующий код и вставьте его в файл Hive, а затем сохраните файл.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Щелкните редактор скриптов правой кнопкой мыши и выберите **HDInsight: Hive Interactive** (HDInsight: интерактивный запрос Hive), чтобы отправить запрос. Средства также позволяют отправить блок кода вместо целого файла сценария с помощью контекстного меню. Вскоре после этого результаты запроса появятся на новой вкладке.

   ![Результат Interactive Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Область **результатов**: вы можете сохранить все результаты в виде файла CSV, JSON или Excel по локальному пути или просто выбрать несколько строк.

    - Область **сообщений**: выбрав номер **строки**, можно перейти к нужной строке выполняемого скрипта.

Выполнение интерактивного запроса занимает гораздо меньше времени, чем [выполнение пакетного задания Hive](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Отправка пакетных сценариев Hive

1. Создайте рабочую папку и файл сценария Hive, если у вас их еще нет.

2. Подключитесь к учетной записи Azure, а затем настройте кластер по умолчанию, если вы еще этого не сделали.

3. Скопируйте следующий код и вставьте его в файл Hive, а затем сохраните файл.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Щелкните редактор скриптов правой кнопкой мыши и выберите **HDInsight: Hive Batch** (HDInsight: пакет Hive), чтобы отправить задание Hive. 

4. Выберите кластер для отправки.  

    После отправки задания Hive сведения о состоянии отправки и идентификатор задания появятся в области **вывода**. Задание Hive также открывает **веб-браузер**, в котором показаны журналы заданий и их состояние в режиме реального времени.

   ![отправка результатов задания Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

[Отправка интерактивных запросов Hive](#submit-interactive-hive-queries) происходит намного быстрее, чем отправка пакетного задания.

## <a name="submit-interactive-pyspark-queries"></a>Отправка интерактивных запросов PySpark
Средства HDInsight для VS Code также позволяют отправлять интерактивные запросы PySpark в кластеры Spark.
1. Создайте рабочую папку и файл сценария с расширением PY, если у вас их еще нет.

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
4. Выделите эти скрипты. Затем щелкните редактор скриптов правой кнопкой мыши и выберите **HDInsight: PySpark Interactive**.

5. Если вы еще не установили расширение **Python** в VS Code, нажмите кнопку **Установить**, как показано на следующей иллюстрации:

    ![Установка расширения Python средств HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Установите среду Python в своей системе, если вы еще этого не сделали. 
   - Для Windows скачайте и установите [Python](https://www.python.org/downloads/). Затем убедитесь, что `Python` и `pip` добавлены в системную переменную PATH.

   - Инструкции для macOS и Linux можно найти в статье [Настройка интерактивной среды PySpark для Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Выберите кластер, к которому будет отправлен запрос PySpark. Вскоре после этого результат запроса отображается на новой вкладке справа:

   ![Отправка результата задания Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. Средство также поддерживает запросы **SQL-предложений**.

   ![Отправка результата задания Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) При выполнении запросов статус отправки отображается слева от нижней строки состояния. Если отображается состояние **PySpark Kernel (busy)** (Ядро PySpark (занято)), не отправляйте другие запросы. 

>[!NOTE]
>Кластеры могут сохранять сведения о сеансе. Определенная переменная, функция и соответствующие значения хранятся в сеансе, поэтому к ним можно обращаться в нескольких вызовах службы одного кластера. 
 

## <a name="submit-pyspark-batch-job"></a>Отправка пакетного задания PySpark

1. Создайте рабочую папку и файл сценария с расширением PY, если у вас их еще нет.

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
4. Щелкните редактор скриптов правой кнопкой мыши и выберите **HDInsight: PySpark Batch** (HDInsight: пакет PySpark). 

5. Выберите кластер, к которому будет отправлено задание PySpark. 

   ![Отправка результата задания Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

После отправки задания Python в окне **вывода** в VS Code отобразятся журналы отправки. Также отображаются **URL-адрес пользовательского интерфейса Spark** и **URL-адрес пользовательского интерфейса Yarn**. URL-адрес можно открыть в веб-браузере, чтобы отследить состояние задания.

>[!NOTE]
>PySpark3 больше не поддерживается в Livy 0.4 (который является кластером HDI Spark 2.2). Python поддерживает только PySpark. Это известная проблема, при которой отправляется сбой Spark 2.2 с помощью python3.
   
## <a name="livy-configuration"></a>Конфигурация Livy
Поддерживается конфигурация Livy, ее можно установить в настройках проекта в папке рабочего пространства. Дополнительные сведения см. в [файле сведений Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

+ Параметры проекта:

    ![Конфигурация Livy](./media/hdinsight-for-vscode/hdi-livyconfig.png)

+ Поддерживаемые конфигурации Livy:   

    **Пакеты POST**   
    Текст запроса

    | name | description | Тип | 
    | :- | :- | :- | 
    | file | Файл, содержащий выполнение приложения | path (обязательный параметр) | 
    | proxyUser | Пользователь для олицетворения при выполнении задания | строка | 
    | className | Основной класс Java или Spark приложения | строка |
    | args | Аргументы командной строки для приложения | Список строк | 
    | jars | jars для использования в этом сеансе | Список строк | 
    | pyFiles | Файлы Python для использования в этом сеансе | Список строк |
    | файлов | Файлы для использования в этом сеансе | Список строк |
    | driverMemory | Объем памяти для процесса драйвера | строка |
    | driverCores | Число ядер для процесса драйвера | int |
    | executorMemory | Объем памяти для каждого процесса исполнителя | строка |
    | executorCores | Число ядер для каждого исполнителя | int |
    | numExecutors | Количество исполнителей для запуска в этом сеансе | int |
    | archives | Архивы для использования в этом сеансе | Список строк |
    | очередь | Имя очереди YARN для отправки | строка |
    | name | Имя этого сеанса | строка |
    | conf | Свойства конфигурации Spark. | Сопоставление key=val |

    Текст ответа   
    Созданный объект пакетной службы

    | name | description | Тип | 
    | :- | :- | :- | 
    | id | Идентификатор сеанса | int | 
    | appId | Идентификатор приложения для этого сеанса |  Строка |
    | appInfo | Информация о приложении | Сопоставление key=val |
    | log | Строки журнала | Список строк |
    | state |   Состояние пакета | строка |


## <a name="additional-features"></a>Дополнительные функции

HDInsight для VSCode поддерживает перечисленные ниже функции.

- **Автоматическое завершение IntelliSense**. Появляются предложения для ключевых слов, методов, переменных и т. д. Разные значки обозначают объекты разных типов.

    ![Типы объектов IntelliSencse в средствах HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Маркер ошибки IntelliSense**. Языковая служба подчеркивает ошибки редактирования для сценария Hive.     
- **Подсветка синтаксиса**. Языковая служба выделяет разными цветами переменные, ключевые слова, типы данных, функции и т. д. 

    ![Подсветка синтаксиса в средствах HDInsight для Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Дополнительная информация

### <a name="demo"></a>Демонстрация
* HDInsight для VS Code: [видео](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Средства и расширения

* [Удаленная отладка приложений Spark через VPN с помощью набора средств Azure для IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Удаленная отладка приложений Spark через SSH с помощью набора средств Azure для IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование средств HDInsight в наборе средств Azure для Eclipse для создания приложений Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Визуализация данных Hive с помощью Microsoft Power BI в Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Visualize Interactive Query Hive data with Microsoft Power BI using DirectQuery in Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Визуализация данных Hive из кластера Interactive Query с помощью Microsoft Power BI и DirectQuery в Azure HDInsight).
* [Настройка интерактивной среды PySpark для Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Выполнение запросов Hive в Azure HDInsight с помощью Zeppelin](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](spark/apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](spark/apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](spark/apache-spark-job-debugging.md)



