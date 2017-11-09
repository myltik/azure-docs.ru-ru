---
title: "Инструменты Microsoft Azure HDInsight. Настройка интерактивной среды PySpark для Visual Studio Code | Документация Майкрософт"
description: "Сведения о создании и отправке запросов и сценариев с помощью средств Azure HDInsight для Visual Studio Code."
Keywords: "VScode,средства Azure HDInsight,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query"
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: 
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 839f379228322eb2da0ff61609634bf1f86e4bb3
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2017
---
# <a name="set-up-pyspark-interactive-environment-for-visual-studio-code"></a>Настройка интерактивной среды PySpark для Visual Studio Code

Ниже показано, как установить пакет Python в **интерактивной среде PySpark HDInsight**.


## <a name="set-up-pyspark-interactive-environment-on-macos-and-linux"></a>Настройка интерактивной среды PySpark в MacOS и Linux
В следующих шагах нужно использовать команду **pip3**, если применяется **python 3.x**.
1. Убедитесь, что **Python** и **pip** установлены.
 
    ![версия pip для Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Установите пакет Jupyter.
    ```
    sudo pip install jupyter
    ```
    +  В Linux и MacOS может появляться следующее сообщение:

        ![error1](./media/set-up-pyspark-interactive-environment/error1.png)
        ```Resolve:
        sudo pip uninstall asyncio
        sudo pip install trollies
        ```

    + При установке libkrb5-dev (только для Linux) может появиться следующее сообщение об ошибке:

        ![error2](./media/set-up-pyspark-interactive-environment/error2.png)
        ```Resolve:
        sudo apt-get install libkrb5-dev 
        ```

3. Установите sparkmagic.
   ```
   sudo pip install sparkmagic
   ```

4. Убедитесь, что мини-приложение ipywidgets установлено правильно. Для этого выполните следующую команду:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Установка ядер программ-оболочек](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Установите ядра программ-оболочек. Выполните команду **pip show sparkmagic**, которая показывает путь, где установлено sparkmagic. 

    ![расположение sparkmagic](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Перейдите в нужное расположение и выполните следующую команду:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![jupyter kernelspec install](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Проверьте состояние установки: 

    ```
    jupyter-kernelspec list
    ```
    ![jupyter kernelspec list](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    В доступных ядрах: **python2** и **pysparkkernel** соответствуют **python 2.x**, **python3** и **pyspark3kernel** соответствуют **python 3.x**. 

8. Перезапустите VScode, а затем снова перейдите к редактору сценариев, который работает на базе **интерактивной среды PySpark службы HDInsight**.

## <a name="next-steps"></a>Дальнейшие действия

### <a name="demo"></a>Демонстрация
* HDInsight для VScode: [видео](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование средств Azure HDInsight для Visual Studio Code](hdinsight-for-vscode.md)
* [Создание приложений Spark для кластера HDInsight с помощью набора средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Удаленная отладка приложений Spark через SSH с помощью набора средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Удаленная отладка приложений Spark через VPN с помощью набора средств Azure для IntelliJ](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование средств HDInsight в наборе средств Azure для Eclipse для создания приложений Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование записных книжек Zeppelin с кластером Spark в HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Ядра, доступные для записной книжки Jupyter в кластере Spark в HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [Визуализация данных Hive с помощью Microsoft Power BI в Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* [Выполнение запросов Hive в Azure HDInsight с помощью Zeppelin](./hdinsight-connect-hive-zeppelin.md).
