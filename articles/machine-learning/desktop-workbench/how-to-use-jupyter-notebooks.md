---
title: Как использовать записные книжки Jupyter в Azure Machine Learning Workbench | Документация Майкрософт
description: Руководство по использованию записных книжек Jupyter Azure Machine Learning Workbench
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 07076a1d551d4dfb513deabaedb650a3262bbeab
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830995"
---
# <a name="use-jupyter-notebooks-in-azure-machine-learning-workbench"></a>Использование записных книжек Jupyter в Azure Machine Learning Workbench

Azure Machine Learning Workbench поддерживает интерактивные эксперименты по обработке и анализу данных путем интеграции записной книжки Jupyter. В данной статье описывается, как эффективно использовать этот компонент, чтобы ускорить проведение интерактивных экспериментов по обработке и анализу данных и повысить их качество.

## <a name="prerequisites"></a>предварительным требованиям
- [Создайте учетную запись для службы "Машинное обучение Azure" и установите Azure Machine Learning Workbench](../service/quickstart-installation.md).
- Ознакомьтесь со сведениями о [записной книжке Jupyter](http://jupyter.org/). В данной статье нет сведений о том, как ее использовать.

## <a name="jupyter-notebook-architecture"></a>Архитектура записной книжки Jupyter
На высоком уровне архитектура записной книжки Jupyter включает три компонента. Каждый из них может работать в разных вычислительных средах.

- **Клиент** — получает данные, вводимые пользователем, и отображает выводимые данные.
- **Сервер** — веб-сервер, на котором размещаются файлы записной книжки (IPYNB-файлы).
- **Ядро** — среда выполнения, в которой выполняются ячейки записной книжки.

Дополнительные сведения см. в статье [How IPython and Jupyter Notebook work](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html) (Сведения о записной книжке IPython и Jupyter). Ниже представлена схема сопоставления архитектуры, состоящей из клиента, сервера и ядра, с компонентами в Машинном обучении Azure.

![Архитектура записной книжки Jupyter](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-machine-learning-workbench-notebooks"></a>Ядра в записных книжках Azure Machine Learning Workbench
В Azure Machine Learning Workbench можно использовать разные ядра. Для этого нужно определить конфигурации запуска и целевые объекты вычислений в папке `aml_config` проекта. Добавление нового целевого объекта вычислений путем выполнения команды `az ml computetarget attach` эквивалентно добавлению нового ядра.

>[!NOTE]
>Дополнительные сведения о конфигурациях запуска и целевых объектах вычислений см. в статье [Настройка службы "Экспериментирование в Машинном обучении Azure"](experimentation-service-configuration.md).

### <a name="kernel-naming-convention"></a>Соглашение об именовании ядер
Azure Machine Learning Workbench создает пользовательские ядра Jupyter. Имя для них создается в формате *\<имя проекта> \<имя конфигурации запуска>*. Например, если у вас есть конфигурация запуска с именем _docker-python_ в проекте с именем _myIris_, в службе "Машинное обучение Azure" будет доступно ядро с именем *myIris docker-python.* Выполняющееся ядро задается в подменю **Change kernel** (Изменение ядра) меню **Ядро** записной книжки Jupyter. Имя выполняющегося ядра отображается в правом углу строки меню.
 
Сейчас Azure Machine Learning Workbench поддерживает следующие типы ядер.

### <a name="local-python-kernel"></a>Локальное ядро Python
Это ядро Python поддерживает выполнение на локальном компьютере. В нем интегрирована поддержка журнала выполнения службы "Машинное обучение Azure". Как правило, имя ядра — *my_project_name local.*

>[!NOTE]
>Не используйте ядро Python 3. Это изолированное ядро, которое Jupyter предоставляет по умолчанию. Оно не интегрировано с возможностями службы "Машинное обучение Azure". Например, контрольные команды Jupyter `%azureml` возвращают ошибки типа "не найдено". 

### <a name="python-kernel-in-docker-local-or-remote"></a>Ядро Python в Docker (локальный или удаленный компьютер)
Это ядро Python выполняется в контейнере Docker на локальном компьютере или на удаленной виртуальной машине Linux. Как правило, имя ядра — *my_project docker.* В связанном файле `docker.runconfig` для поля `Framework` задано значение `Python`.

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Ядро PySpark в Docker (локальный или удаленный компьютер)
Это ядро PySpark выполняет скрипты в контексте Spark в контейнере Docker на локальном компьютере или на удаленной виртуальной машине Linux. Как правило, имя ядра — *my_project docker.* В связанном файле `docker.runconfig` для поля `Framework` задано значение `PySpark`.

### <a name="pyspark-kernel-in-an-azure-hdinsight-cluster"></a>Ядро PySpark в кластере HDInsight Azure
Это ядро выполняется в удаленном кластере HDInsight Azure, подключенном в качестве целевого объекта вычислений для проекта. Как правило, имя ядра — *my_project my_hdi.* 

>[!IMPORTANT]
>Чтобы использовать это ядро, в файле `.compute` целевого объекта вычислений HDI для поля `yarnDeployMode` необходимо задать значение `client` (значение по умолчанию — `cluster`). 

## <a name="start-a-jupyter-server-from-azure-machine-learning-workbench"></a>Запуск сервера Jupyter из Azure Machine Learning Workbench
В Azure Machine Learning Workbench доступ к записным книжкам можно получить на вкладке **Notebooks** (Записные книжки). В примере проекта _Классификация Iris_ содержится пример записной книжки `iris.ipynb`.

![Вкладка Notebooks (Записные книжки)](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

При открытии в Azure Machine Learning Workbench записная книжка отображается на отдельной вкладке документов в **режиме предварительного просмотра**. Это представление, доступное только для чтения, которое не требует выполняющегося сервера и ядра Jupyter.

![Предварительный просмотр записной книжки](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

Если нажать кнопку **Start Notebook Server** (Запуск сервера записной книжки), запускается сервер Jupyter, а записная книжка переходит в **режим правки**. Появляется знакомый пользовательский интерфейс записной книжки Jupyter, встроенный в Workbench. Теперь вы можете настроить ядро в меню **Ядро** и запустить сеанс интерактивной записной книжки. 

>[!NOTE]
>Для нелокальных ядер при первом использовании сеанс запускается одну-две минуты. Вы можете выполнить команду `az ml experiment prepare` из окна интерфейса командной строки для подготовки целевого объекта вычислений, чтобы после этого ядро запускалось гораздо быстрее.

![Режим правки](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

Это полностью интерактивная записная книжка Jupyter. В этом окне поддерживаются стандартные сочетания клавиш и операции с записными книжками, за исключением некоторых операций с файлами, которые можно выполнить с помощью вкладок Workbench **Notebooks** (Записные книжки) и **Файл**.

## <a name="start-a-jupyter-server-from-the-command-line"></a>Запуск сервера Jupyter из командной строки
Сеанс записной книжки также можно запустить, выполнив в окне командной строки команду `az ml notebook start`:
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
Copy and paste this URL into your browser when you connect for the first time, to login with a token: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
После этого автоматически браузер открывается по умолчанию с сервером Jupyter, указывающим на домашний каталог проекта. Кроме того, чтобы открыть другие окна браузера локально, можно использовать URL-адрес и маркер из окна интерфейса командной строки. 

![Панель мониторинга проекта](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

Теперь можно выбрать файл записной книжки `.ipynb`, открыть его, настроить ядро (если оно не настроено) и запустить интерактивный сеанс.

![Панель мониторинга проекта](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>Использование magic-команд для управления экспериментами

Используя [magic-команды](http://ipython.readthedocs.io/en/stable/interactive/magics.html) внутри ячеек записной книжки, можно отслеживать историю выполнения и сохранять выходные данные, например модели или наборы данных.

Для отслеживания отдельных выполнений ячеек записной книжки используйте magic-команду `%azureml history on`. После включения журнала выполнение каждой ячейки будет отображаться как запись в журнале выполнения.

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

Чтобы отключить отслеживание выполнения ячеек, используйте magic-команду `%azureml history off`.

Magic-команда `%azureml upload` позволяет сохранить полученные в результате выполнения файлы моделей и данных. Сохраненные объекты отображаются как выходные данные в представлении журнала выполнения.

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>Выходные данные должны сохраняться в папку с именем *outputs.*

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения об использовании записной книжки Jupyter см. в [официальной документации Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/).    
- Более подробные сведения о среде выполнения службы "Экспериментирование в Машинном обучении" см. в статье [Настройка службы "Экспериментирование в Машинном обучении Azure"](experimentation-service-configuration.md).

