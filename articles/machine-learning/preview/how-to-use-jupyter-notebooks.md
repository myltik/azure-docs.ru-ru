---
title: "Как использовать записные книжки Jupyter в Azure Machine Learning Workbench | Документация Майкрософт"
description: "Руководство по использованию записных книжек Jupyter Azure Machine Learning Workbench"
services: machine-learning
author: jopela
ms.author: jopela
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 07759a590e3a4e061a1c5727428d2c7c2a655f51
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---
# <a name="how-to-use-jupyter-notebook-in-azure-machine-learning-workbench"></a>Как использовать записную книжку Jupyter в Azure Machine Learning Workbench

Azure Machine Learning Workbench поддерживает интерактивные эксперименты по обработке и анализу данных путем интеграции записной книжки Jupyter. В этой статье описывается, как эффективно использовать этот компонент, чтобы ускорить проведение интерактивных экспериментов по обработке и анализу данных и повысить их качество.

## <a name="prerequisites"></a>Предварительные требования
- [Установите службу "Машинное обучение Azure" и создайте проект](/machine-learning/preview/quickstart-installation.md).
- Ознакомьтесь с [записной книжкой Jupyter](http://jupyter.org/), так как в этой статье нет сведений о том, как ее использовать.

## <a name="jupyter-notebook-architecture"></a>Архитектура записной книжки Jupyter
На высоком уровне архитектура записной книжки Jupyter включает три компонента, каждый из которых может работать в разных вычислительных средах:

- **Клиент** — получает данные, вводимые пользователем, и отображает выводимые данные.
- **Сервер** — веб-сервер, на котором размещаются файлы записной книжки (IPYNB-файлы).
- **Ядро** — среда выполнения, где фактически выполняются ячейки записной книжки.

Дополнительные сведения см. в официальной [документации по Jupyter](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html). Далее представлена схема сопоставления этих архитектурных компонентов (клиент, сервер и ядро) с компонентами в Azure ML.

![архитектура записной книжки](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-ml-workbench-notebook"></a>Ядра в записной книжке Azure ML Workbench
В Azure ML Workbench вы можете получить доступ ко многим ядрам, просто настроив конфигурации запуска и целевые объекты вычислений в папке проекта `aml_config`. Добавление новой целевой среды вычислений путем выполнения команды `az ml computetarget attach` эквивалентно добавлению нового ядра.

>[!NOTE]
>Дополнительные сведения о конфигурациях запуска и целевых объектах вычислений см. в статье [Overview of Azure Machine Learning experiment execution service](experiment-execution-configuration.md) (Обзор службы выполнения экспериментов службы "Машинное обучение Azure").

### <a name="kernel-naming-convention"></a>Соглашение об именовании ядер
Имена ядер обычно имеют формат "\<имя проекта> \<имя конфигурации запуска>". Например, если у вас есть конфигурация запуска с именем _docker python_ в проекте _myIris_, при открытии записной книжки Jupyter можно найти ядро с именем myIris docker-python в списке ядер.

Сейчас Workbench поддерживает следующие типы ядер.

### <a name="local-python-kernel"></a>Локальное ядро Python
Это ядро Python поддерживает выполнение на локальном компьютере. В нем интегрирована поддержка журнала выполнения службы "Машинное обучение Azure". Как правило, имя ядра — my_project_name local.

### <a name="python-kernel-in-docker-local-or-remote"></a>Ядро Python в Docker (локальный или удаленный компьютер)
Это ядро Python выполняется в контейнере Docker на локальном компьютере или на удаленной виртуальной машине Linux. Как правило, имя ядра — my_project docker. В связанном файле `docker.runconfig` для поля `Framework` задано значение `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Ядро PySpark в Docker (локальный или удаленный компьютер)
Это ядро PySpark выполняет скрипты в контексте Spark в контейнере Docker на локальном компьютере или на удаленной виртуальной машине Linux. Как правило, имя ядра — my_project docker. В связанном файле `docker.runconfig` для поля `Framework` задано значение `PySpark`.

### <a name="pyspark-kernel-on-hdinsight-cluster"></a>Ядро PySpark в кластере HDInsight
Это ядро выполняется в удаленном кластере HDInsight, подключенном в качестве целевого объекта вычислений для проекта. Как правило, имя ядра — my_project my_hdi. 

>[!IMPORTANT]
>В файле `.compute` целевого объекта вычислений HDI для поля `yarnDeployMode` необходимо изменить значение на `client` (значение по умолчанию — `cluster`) для использования этого ядра. 

## <a name="start-jupyter-server-from-the-workbench"></a>Запуск сервера Jupyter из Workbench
В Azure Machine Learning Workbench доступ к записным книжкам можно получить на вкладке Workbench **Notebooks** (Записные книжки). В примере проекта _Классификация Iris_ содержится пример записной книжки `iris.ipynb`.

![вкладка Notebooks (Записные книжки)](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

При открытии в Azure Machine Learning Workbench записная книжка отображается в отдельной вкладке документов в **режиме предварительного просмотра**. Это представление, доступное только для чтения, которое не требует выполняющегося сервера и ядра Jupyter.

![предварительный просмотр записной книжки](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Если нажать кнопку **Start Notebook Server** (Запуск сервера записной книжки), запускается сервер Jupyter, а записная книжка переходит в **режим изменения**. Появляется знакомый пользовательский интерфейс записной книжки Jupyter, встроенный в Workbench. Теперь вы можете настроить ядро в меню **Ядро** и запустить сеанс интерактивной записной книжки. 

>[!NOTE]
>Обратите внимание, что для нелокальных ядер при первом использовании сеанс запускается одну-две минуты. Вы можете выполнить команду `az ml experiment prepare` из окна интерфейса командной строки для подготовки целевого объекта вычислений, чтобы после этого ядро запускалось гораздо быстрее.

![режим правки](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Это полностью интерактивная записная книжка Jupyter. В этом окне поддерживаются стандартные сочетания клавиш и операции с записными книжками, за исключением некоторых операций с файлами, так как их можно выполнить с помощью вкладок Workbench **Notebooks** (Записные книжки) и **Файл**.

## <a name="start-jupyter-server-from-command-line"></a>Запуск сервера Jupyter из командной строки
Сеанс записной книжки также можно запустить, выполнив команду `az ml notebook start` в окне командной строки:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
После этого автоматически запускается браузер по умолчанию с сервером Jupyter, указывающим на домашний каталог проекта. Кроме того, можно использовать URL-адрес и маркер из окна интерфейса командной строки для запуска других окон браузера локально. 

![панель мониторинга проекта](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Теперь можно щелкнуть файл записной книжки `.ipynb`, открыть его, настроить ядро (если оно не настроено) и запустить интерактивный сеанс.

![панель мониторинга проекта](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения об использовании записной книжки Jupyter см. в [официальной документации Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Более подробные сведения о среде выполнения экспериментов Azure ML см. в статье [Overview of Azure Machine Learning experiment execution service](experiment-execution-configuration.md) (Обзор службы выполнения экспериментов службы "Машинное обучение Azure").


