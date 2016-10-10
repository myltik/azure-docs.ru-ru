<properties 
	pageTitle="Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Spark в HDInsight | Microsoft Azure" 
	description="Узнайте, как установить записную книжку Jupyter на компьютере локально и как подключить ее к кластеру Apache Spark в Azure HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="jhubbard" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/26/2016" 
	ms.author="nitinme"/>


# Установка записной книжки Jupyter на компьютер и подключение к кластеру Apache Spark в HDInsight на платформе Linux

В этой статье вы узнаете, как установить записную книжку Jupyter с пользовательскими ядрами PySpark (для Python) и Spark (для Scala) с помощью волшебной команды Spark и как подключить записную книжку к кластеру HDInsight. Существуют разные причины для установки Jupyter на локальном компьютере, но есть и некоторые трудности. Список причин и возможных проблем приводится в разделе [Зачем устанавливать Jupyter на моем компьютере?](#why-should-i-install-jupyter-on-my-computer) в конце этой статьи.

Для установки Jupyter и волшебной команды Spark на ваш компьютер необходимо выполнить три основных действия.

* Установка записной книжки Jupyter
* Установите ядра PySpark и Spark с помощью волшебной команды Spark
* Настройте волшебную команду Spark для доступа к кластеру Spark в HDInsight

Дополнительные сведения о пользовательских ядрах и наборе средств Spark magic для записных книжек Jupyter с кластерами HDInsight см. в статье [Ядра, доступные для записных книжек Jupyter с кластерами Apache Spark Linux в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

##Предварительные требования

Указанные здесь предварительные требования относятся не к установке Jupyter. Они относятся к подключению записной книжки Jupyter к кластеру HDInsight после установки записной книжки.

- Подписка Azure. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Кластер Apache Spark в HDInsight на платформе Linux. Инструкции см. в разделе [Создание кластеров Apache Spark в Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Установка записной книжки Jupyter на компьютер

Перед установкой записных книжек Jupyter необходимо установить Python. Python и Jupyter доступны в составе [дистрибутива Ananconda](https://www.continuum.io/downloads). Дистрибутив Python фактически устанавливается при установке Anaconda. После установки Anaconda выполните следующую команду для установки Jupyter. Этот раздел содержит инструкции, которые необходимо выполнить.

1. Скачайте [установщик Anaconda](https://www.continuum.io/downloads) для своей платформы и запустите программу установки. В мастере установки укажите параметр для добавления Anaconda в переменную PATH.

2. Выполните следующую команду для установки Jupyter.

		conda install jupyter

	Дополнительные сведения об установке Jupyter см. в разделе [Установка Jupyter с помощью Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## Установка ядер и волшебной команды Spark

Инструкции по установке магической команды Spark, ядер PySpark и Spark см. в [документации по sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) на GitHub.

## Настройка волшебной команды Spark для доступа к кластеру Spark в HDInsight

В этом разделе вы настроите подключение волшебной команды Spark, установленную ранее, к кластеру Apache Spark, который вы уже создали в Azure HDInsight.

1. Сведения о конфигурации Jupyter обычно хранятся в домашнем каталоге пользователей. Чтобы найти домашний каталог, введите следующие команды (они подходят для любой платформы ОС).

	Запустите оболочку Python. В командной строке введите следующую команду:

		python

	В оболочке Python введите следующую команду, чтобы найти домашний каталог.

		import os
		print os.path.expanduser('~')

2. Перейдите в домашний каталог и создайте папку с именем **.sparkmagic**, если ее еще нет.

3. В этой папке создайте файл **config.json** и добавьте в него следующий фрагмент кода JSON.

		{
		  "kernel_python_credentials" : {
		    "username": "{USERNAME}",
		    "base64_password": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  },
		  "kernel_scala_credentials" : {
		    "username": "{USERNAME}",
		    "base64_password": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  }
		}

4. Замените **{USERNAME}**, **{CLUSTERDNSNAME}** и **{BASE64ENCODEDPASSWORD}** соответствующими значениями. Для создания пароля в кодировке base64 вы можете воспользоваться различными служебными программами для своего любимого языка программирования или интернет-ресурсами. Например, простой фрагмент на Python для запуска из командной строки может выглядеть так:

		python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. Запустите Jupyter. Выполните следующую команду из командной строки.

		jupyter notebook

6. Убедитесь, что вы можете подключиться к кластеру с помощью записной книжки Jupyter и использовать волшебную команду Spark с ядрами. Выполните следующие действия.

	1. Создайте новую записную книжку. В правом верхнем углу щелкните **Создать**. Вы должны увидеть ядро по умолчанию **Python2** и два новых ядра, которые вы установили: **PySpark** и **Spark**.

		![Создание новой записной книжки Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Создание новой записной книжки Jupyter")

	
		Щелкните **PySpark**.


	2. Запустите следующий фрагмент кода.

			%%sql
			SELECT * FROM hivesampletable LIMIT 5

		Если вы успешно получили выходные данные, подключение к кластеру HDInsight работает.

	>[AZURE.TIP] Если вы хотите обновить конфигурацию записной книжки для подключения к другому кластеру, измените файл config.json, указав новый набор значений, как показано на шаге 3.

## Зачем устанавливать Jupyter на моем компьютере?

Может быть несколько причин, по которым вам потребуется установить на компьютер Jupyter и подключить его к кластеру Spark в HDInsight.

* Хотя записные книжки Jupyter уже доступны в кластере Spark в Azure HDInsight, после их установки на компьютер вы сможете создавать записные книжки локально, тестировать приложения на работающем кластере и отправлять записные книжки в кластер. Для отправки записных книжек в кластер можно отправить их с помощью записной книжки Jupyter, которая запущена на кластере, или сохранить их в папке /HdiNotebooks в учетной записи хранения, связанной с кластером. Дополнительные сведения о хранении записных книжек в кластере см. в разделе [Где хранятся записные книжки?](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)
* С помощью локально доступных записных книжек вы сможете подключиться к различным кластерам Spark в зависимости от потребностей вашего приложения.
* Можно использовать GitHub для реализации системы управления версиями, чтобы контролировать версии записных книжек. Вы также можете создать среду совместной работы, в которой несколько пользователей будут работать с одной записной книжкой.
* Вы можете работать с записными книжками локально даже без кластера. Кластер нужен только для тестирования записных книжек, но не обязателен для ручного управления записными книжками или средой разработки.
* Возможно, вам будет проще настроить локальную среду разработки, чем настраивать установку Jupyter в кластере. Вы можете спокойно пользоваться любым программным обеспечением, установленным локально, не настраивая удаленные кластеры.

>[AZURE.WARNING] Если Jupyter установлен на локальном компьютере, несколько пользователей могут одновременно запустить одну и ту же записную книжку в одном кластере Spark. В такой ситуации создаются несколько сеансов Livy. Если вы столкнетесь с проблемами и начнете их отладку, вам будет сложно определить, какой сеанс Livy какому пользователю принадлежит.




## <a name="seealso"></a>См. также:


* [Обзор: Apache Spark в Azure HDInsight](hdinsight-apache-spark-overview.md)

### Сценарии

* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](hdinsight-apache-spark-use-bi-tools.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Потоковая передача Spark. Использование Spark в HDInsight для сборки приложений потоковой передачи данных в режиме реального времени](hdinsight-apache-spark-eventhub-streaming.md)

* [Анализ журнала веб-сайта с использованием Spark в HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Создание и запуск приложений

* [Создание автономного приложения с использованием Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Удаленный запуск заданий с помощью Livy в кластере Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Средства и расширения

* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### Управление ресурсами

* [Управление ресурсами кластера Apache Spark в Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0928_2016-->