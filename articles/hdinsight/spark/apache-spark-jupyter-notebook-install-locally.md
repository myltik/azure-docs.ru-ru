---
title: Установка записной книжки Jupyter в локальной среде и ее подключение к кластеру Azure HDInsight Spark | Документы Майкрософт
description: Сведения о том, как установить записную книжку Jupyter на компьютере локально и как подключить ее к кластеру Apache Spark в Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48593bdf-4122-4f2e-a8ec-fdc009e47c16
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: eea61586054f34142d77f16333fe70a66d95d529
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528364"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Установка записной книжки Jupyter на компьютере и ее подключение к Apache Spark в HDInsight

Из этой статьи вы узнаете, как установить записную книжку Jupyter с пользовательскими ядрами PySpark (для Python) и Spark (для Scala) с помощью волшебной команды Spark, а затем подключить эту записную книжку к кластеру HDInsight. Существуют разные причины для установки Jupyter на локальном компьютере, но есть и некоторые трудности. Дополнительные сведения см. в разделе [Зачем устанавливать Jupyter на компьютер?](#why-should-i-install-jupyter-on-my-computer) в конце этой статьи.

Для установки Jupyter и волшебной команды Spark на ваш компьютер необходимо выполнить три основных действия.

* Установка записной книжки Jupyter
* Установите ядра PySpark и Spark с помощью волшебной команды Spark
* Настройте волшебную команду Spark для доступа к кластеру Spark в HDInsight

Дополнительные сведения о пользовательских ядрах и волшебных командах Spark, доступных для записных книжек Jupyter в кластере HDInsight, см. в статье [Ядра, доступные для использования записными книжками Jupyter с кластерами Apache Spark в HDInsight на платформе Linux](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>предварительным требованиям
Указанные здесь предварительные требования относятся не к установке Jupyter. Они относятся к подключению записной книжки Jupyter к кластеру HDInsight после установки записной книжки.

* Подписка Azure. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Кластер Apache Spark в HDInsight. Инструкции см. в статье [Начало работы. Создание кластера Apache Spark в HDInsight на платформе Linux и выполнение интерактивных запросов с помощью SQL Spark](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Установка записной книжки Jupyter на компьютер

Перед установкой записных книжек Jupyter необходимо установить Python. Python и Jupyter доступны в составе [дистрибутива Anaconda](https://www.continuum.io/downloads). При установке Anaconda устанавливается дистрибутив Python. После установки Anaconda выполните соответствующие команды для установки Jupyter.

1. Скачайте [установщик Anaconda](https://www.continuum.io/downloads) для своей платформы и запустите программу установки. В мастере установки укажите параметр для добавления Anaconda в переменную PATH.
2. Выполните следующую команду для установки Jupyter.

        conda install jupyter

    Дополнительные сведения об установке Jupyter см. [здесь](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Установка ядер и волшебной команды Spark

Инструкции по установке волшебной команды Spark, а также ядер PySpark и Spark, см. в [документации по sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) на сайте GitHub. Чтобы начать использовать волшебную команду Spark, ее сначала нужно установить. Это — первый шаг, описанный в документации. Замените этот шаг в ссылке выше приведенными ниже командами с учетом версии кластера HDInsight, к которому вы подключитесь. После этого выполните остальные шаги в соответствии с документацией по волшебной команде Spark. Для установки разных ядер необходимо выполнить шаг 3 из раздела инструкций по установке волшебной команды Spark.

* Для кластеров версии 3.4 установите sparkmagic версии 0.2.3, выполнив команду `pip install sparkmagic==0.2.3`

* Для кластеров версий 3.5 и 3.6 установите sparkmagic версии 0.11.2, выполнив команду `pip install sparkmagic==0.11.2`

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Настройка волшебной команды Spark для подключения к кластеру HDInsight Spark

В этом разделе вы настроите подключение волшебной команды Spark, установленную ранее, к кластеру Apache Spark, который вы уже создали в Azure HDInsight.

1. Сведения о конфигурации Jupyter обычно хранятся в домашнем каталоге пользователей. Чтобы найти домашний каталог, введите следующие команды (они подходят для любой платформы ОС).

    Запустите оболочку Python. В командной строке введите следующую команду:

        python

    В оболочке Python введите следующую команду, чтобы найти домашний каталог.

        import os
        print(os.path.expanduser('~'))

2. Перейдите в домашний каталог и создайте папку с именем **.sparkmagic** , если ее еще нет.
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

4. Замените **{USERNAME}**, **{CLUSTERDNSNAME}** и **{BASE64ENCODEDPASSWORD}** соответствующими значениями. Для создания пароля в кодировке base64 вы можете использовать разные служебные программы на предпочитаемом языке программирования или средства, доступные в Интернете.

5. Правильно настройте параметры пульса в `config.json`. Эти параметры необходимо добавить на одном уровне с фрагментами `kernel_python_credentials` и `kernel_scala_credentials`, которые были добавлены ранее. В этом [примере файла config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json) показано, как и где добавляются параметры пульса.

    * Для `sparkmagic 0.2.3` (кластеры версии 3.4) добавьте:

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * Для `sparkmagic 0.11.2` (кластеры версий 3.5 и 3.6) добавьте:

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >Сигналы пульса отправляются, чтобы предотвратить утечку сеансов. При переходе в спящий режим или завершении работы компьютера пульс не отправляется, что приводит к очистке сеанса. Если вы хотите отключить такое поведение для кластеров версии 3.4, то можете настроить для параметра Livy `livy.server.interactive.heartbeat.timeout` значение `0` с помощью пользовательского интерфейса Ambari. Если для кластеров версии 3.5 не настроить соответствующую конфигурацию, приведенную выше, то сеанс не будет удален.

6. Запустите Jupyter. Выполните следующую команду из командной строки.

        jupyter notebook

7. Убедитесь, что вы можете подключиться к кластеру с помощью записной книжки Jupyter и использовать волшебную команду Spark с ядрами. Выполните следующие действия.

    a. Создайте новую записную книжку. В правом верхнем углу щелкните **Создать**. Вы должны увидеть ядро по умолчанию **Python2** и два новых ядра, которые вы установили: **PySpark** и **Spark**. Щелкните **PySpark**.

    ![Ядра в записной книжке Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Ядра в записной книжке Jupyter")

    Б. Запустите следующий фрагмент кода.

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    Если вы успешно получили выходные данные, подключение к кластеру HDInsight работает.

    >[!TIP]
    >Если вы хотите обновить конфигурацию записной книжки для подключения к другому кластеру, измените файл config.json, указав новый набор значений, как показано на шаге 3.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Зачем устанавливать Jupyter на моем компьютере?
Может быть несколько причин, по которым вам потребуется установить на компьютер Jupyter и подключить его к кластеру Spark в HDInsight.

* Хотя записные книжки Jupyter уже доступны в кластере Spark в Azure HDInsight, после их установки на компьютер вы сможете создавать записные книжки локально, тестировать приложения на работающем кластере и отправлять записные книжки в кластер. Для отправки записных книжек в кластер можно отправить их с помощью записной книжки Jupyter, которая запущена на кластере, или сохранить их в папке /HdiNotebooks в учетной записи хранения, связанной с кластером. Дополнительные сведения о хранении записных книжек в кластере см. в разделе [Где хранятся записные книжки](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored).
* С помощью локально доступных записных книжек вы сможете подключиться к различным кластерам Spark в зависимости от потребностей вашего приложения.
* Можно использовать GitHub для реализации системы управления версиями, чтобы контролировать версии записных книжек. Вы также можете создать среду совместной работы, в которой несколько пользователей будут работать с одной записной книжкой.
* Вы можете работать с записными книжками локально даже без кластера. Кластер нужен только для тестирования записных книжек, но не обязателен для ручного управления записными книжками или средой разработки.
* Возможно, вам будет проще настроить локальную среду разработки, чем настраивать установку Jupyter в кластере.  Вы можете спокойно пользоваться любым программным обеспечением, установленным локально, не настраивая удаленные кластеры.

> [!WARNING]
> Если Jupyter установлен на локальном компьютере, несколько пользователей могут одновременно запустить одну и ту же записную книжку в одном кластере Spark. В такой ситуации создаются несколько сеансов Livy. Если вы столкнетесь с проблемами и начнете их отладку, вам будет сложно определить, какой сеанс Livy какому пользователю принадлежит.
>
>

## <a name="seealso"></a>Дополнительные материалы
* [Обзор: Apache Spark в Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Сценарии
* [Использование Spark со средствами бизнес-аналитики. Выполнение интерактивного анализа данных с использованием Spark в HDInsight с помощью средств бизнес-аналитики](apache-spark-use-bi-tools.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для анализа температуры в здании на основе данных системы кондиционирования](apache-spark-ipython-notebook-machine-learning.md)
* [Использование Spark с машинным обучением. Использование Spark в HDInsight для прогнозирования результатов контроля качества пищевых продуктов](apache-spark-machine-learning-mllib-ipython.md)
* [Анализ журнала веб-сайта с использованием Spark в HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Создание и запуск приложений
* [Создание автономного приложения с использованием Scala](apache-spark-create-standalone-application.md)
* [Удаленный запуск заданий с помощью Livy в кластере Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование подключаемого модуля средств HDInsight для IntelliJ IDEA для создания и отправки приложений Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark в кластере HDInsight Spark Linux с помощью подключаемого модуля средств HDInsight для IntelliJ IDEA](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Управление ресурсами
* [Управление ресурсами кластера Apache Spark в Azure HDInsight](apache-spark-resource-manager.md)
* [Отслеживание и отладка заданий в кластере Apache Spark в HDInsight на платформе Linux](apache-spark-job-debugging.md)
