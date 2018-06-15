---
title: Общие сведения о виртуальных машинах Linux и Windows для обработки и анализа данных | Документация Майкрософт
description: Основные аналитические сценарии и компоненты виртуальных машин Linux и Windows для обработки и анализа данных.
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/27/2017
ms.author: gokuma
ms.openlocfilehash: 9ef6b216889416ea00786dcd3043d6e0f246b305
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31595479"
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>Общие сведения о виртуальных машинах Linux и Windows для обработки и анализа данных

Виртуальная машина для анализа и обработки данных (DSVM) — это настроенный образ в облаке Microsoft Azure. В ней предварительно установлено и настроено множество популярных средств для анализа и обработки данных, а также других средств, позволяющих быстро приступить к созданию интеллектуальных приложений для расширенной аналитики. Она доступна на платформах Windows Server и Linux. Версия DSVM для операционных систем Windows работает в Windows Server 2016 и Windows Server 2012. Версия DSVM для операционных систем Linux работает в Ubuntu 16.04 LTS и CentOS 7.4.

В этом разделе обсуждаются возможности, которые предоставляет виртуальная машина для анализа и обработки данных. Здесь также описаны некоторые основные сценарии ее использования, перечислены ее ключевые функции, доступные в версиях Windows и Linux, а также инструкции о том, как приступить к работе с ними.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Какие возможности предоставляет виртуальная машина для анализа и обработки данных?
Назначение виртуальной машины для анализа и обработки данных (DSVM) — обеспечить для специалистов, обладающих любыми навыками и выполняющих любые роли, беспроблемную предварительно настроенную и полностью интегрированную среду для обработки и анализа данных. Вместо самостоятельного развертывания сопоставимой рабочей области вы можете подготовить DSVM. Это сэкономит вам несколько дней или даже _недель_, обычно требующихся для установки, настройки и администрирования пакета. Когда виртуальная машина DSVM будет выделена, можно немедленно приступать к проекту по обработке и анализу данных.

Виртуальная машина для анализа и обработки данных разработана и настроена для работы в широком ряде сценариев использования. Масштаб среды можно увеличивать и уменьшать согласно требованиям проекта. Вы можете использовать предпочитаемый язык, чтобы программировать задачи обработки и анализа данных. Вы также можете установить другие инструменты и настроить систему согласно конкретным потребностям.

## <a name="key-scenarios"></a>Ключевые сценарии
В этой главе рассматриваются некоторые основные сценарии, для которых можно развернуть виртуальную машину обработки и анализа данных.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Предварительно настроенный облачный компьютер для анализа
В виртуальной машине для обработки и анализа данных доступна базовая конфигурация, что не смогут не оценить рабочие группы, которые ищут подходящий вариант для замены своих локальных компьютеров на управляемые облачные компьютеры. За счет базы у всех специалистов по обработке и анализу данных в рабочей группе будут согласованные настройки, позволяющие проверять эксперименты и повышать уровень взаимодействия. Кроме того, благодаря уменьшению нагрузки на системных администраторов и экономии времени, затрачиваемого на оценку, установку и обслуживание различных пакетов программного обеспечения, необходимого для использования расширенной аналитики, сокращаются расходы.  

### <a name="data-science-training-and-education"></a>Обучение обработке и анализу данных
Обычно учителя и преподаватели курсов по обработке и анализу данных предоставляют ученикам образ виртуальной машины, чтобы они использовали одинаковые настройки, а примеры работали правильно. Виртуальная машина обработки и анализа данных создает требуемую среду с согласованными настройками, что упрощает задачи поддержки и устраняет проблемы несовместимости. Для случаев, когда необходимо часто создавать такие среды, особенно в рамках коротких учебных курсов, характерно больше преимуществ.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Эластичность по требованию для проектов большого масштаба
Интенсивная работа и соревнования по обработке и анализу данных, моделирование и изучение данных большого масштаба обычно требуют использования мощного оборудования в течение короткого периода. Виртуальная машина для обработки и анализа данных помогает быстро и по требованию реплицировать среду обработки данных на развернутые серверы. Это позволяет проводить эксперименты, требующие мощных вычислительных ресурсов для репликации.

### <a name="short-term-experimentation-and-evaluation"></a>Краткосрочные эксперименты и оценка
Виртуальную машину для обработки и анализа данных можно использовать для оценки таких средств, как Microsoft ML Server, SQL Server, инструментов Visual Studio, Jupyter, наборов средств глубокого и машинного обучения, новых средств, популярных в сообществе, а также обучения им при минимальной необходимости в настройке. Так как виртуальную машину для обработки и анализа данных можно быстро настроить, ее можно использовать и в других краткосрочных сценариях, к примеру для репликации опубликованных экспериментов, демонстрации, выполнения пошаговых учебников в рамках онлайн-сеансов или руководств на конференциях.

### <a name="deep-learning"></a>Глубокое обучение
ВМ для обработки и анализа данных можно применять в модели обучения, использующей алгоритмы глубокого обучения на оборудовании с GPU (графическими процессорами). DSVM использует возможности облака Azure по масштабированию виртуальных машин, что позволяет предоставлять вам оборудование GPU в таких объемах, которые требуются в конкретный момент. Вы можете переключаться на виртуальные машины с GPU, когда обучаете большие модели или выполняете высокоскоростные вычисления, не меняя диск операционной системы.  Версия DSVM для Windows Server 2016 содержит предустановленные драйверы GPU, платформы и версии GPU платформ глубокого обучения. В ОС Linux глубокое обучение на GPU включено во все версии DSVM, как для CentOS, так и для Ubuntu. Версию DSVM для Ubuntu, CentOS или Windows 2016 можно развернуть на виртуальной машине Azure без GPU. В этом случае все платформы глубокого обучения будут переведены в режим ЦП. 

## <a name="whats-included-in-the-data-science-vm"></a>Что предусмотрено в виртуальной машине для обработки и анализа данных?
В этой виртуальной машине уже установлено и настроено множество популярных средств для обработки и анализа данных, а также глубокого обучения. Среди них также есть средства, упрощающие работу с различными данными и продуктами для анализа Azure. Вы можете просматривать и создавать прогнозные модели на основе наборов данных большого масштаба, используя Microsoft ML Server (R, Python) или SQL Server 2017. Эта виртуальная машина включает в себя узел других средств, предоставленных корпорацией Майкрософт и сообществом открытого исходного кода, а также здесь предусмотрены приложения Notebook и образец кода. В следующей таблице перечислены основные компоненты выпусков Windows и Linux виртуальной машины для обработки и анализа данных.


| **Средство**                                                           | **Выпуск для Windows** | **Выпуск для Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) с предустановленными популярными пакетами   |Да                      | Да             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition включает: <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) — параллельная распределенная платформа высокой производительности (R и Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) — новые передовые алгоритмы ML от Майкрософт; <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [Ввод в эксплуатацию R и Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Да                      | Да |
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) Pro Plus с общей активацией — Excel, Word и PowerPoint   |Да                      |Нет              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 с предустановленными популярными пакетами    |Да                      |Да              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) с предварительно установленными популярными пакетами для языка Julia                         |Да                      |Да              |
| Реляционные базы данных                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS)<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Инструменты базы данных                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * Драйверы ODBC и JDBC| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (инструмент запросов), <br /> * bcp, sqlcmd <br /> * Драйверы ODBC и JDBC|
| Масштабируемое аналитическое решение в базе данных со службами ML SQL Server (R, Python) | Да     |Нет              |
| **[Jupyter Notebook Server](http://jupyter.org/) со следующими ядрами**                                  | Да     | Да |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Да | Да |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | Да | Да |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | Да | Да |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | Да | Да |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | Нет | Да (только Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | Нет | Да |
| JupyterHub (многопользовательский сервер блокнотов)| Нет | Да |
| JupyterLab (многопользовательский сервер блокнотов) | Нет | Да (только Ubuntu) |
| **Инструменты разработки, редакторы кода и интегрированные среды разработки**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) с подключаемым модулем Git, Azure HDInsight (Hadoop), Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](http://aka.ms/ptvs) и [Инструменты R для Visual Studio (RTVS)](http://microsoft.github.io/RTVS-docs/) | Да | Нет |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (интегрированная среда разработки Julia)](http://junolab.org/)| Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim и Emacs | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git и GitBash | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* .Net Framework | Да | Нет |
| PowerBI Desktop | Да | Нет |
| Пакеты SDK для доступа к Azure и набор служб Cortana Intelligence | Да | Да |
| **Инструменты перемещения данных и управления ими** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Обозреватель хранилищ Azure | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Да | Нет |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Да | Нет |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Драйвер больших двоичных объектов FUSE](https://github.com/Azure/azure-storage-fuse) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy (служба хранилища Azure Data Lake)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Да | Нет |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Инструмент переноса данных DocDB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Да | Нет |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Шлюз управления данными Майкрософт](https://msdn.microsoft.com/library/dn879362.aspx): перемещение данных между локальным хранилищем и облаком | Да | Нет |
| &nbsp;&nbsp;&nbsp;&nbsp;* Служебные программы командной строки Unix/Linux | Да | Да |
| [Apache Drill](http://drill.apache.org) для просмотра данных | Да | Да |
| **Инструменты машинного обучения** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Средства, интегрирующиеся с [Машинным обучением Azure](https://azure.microsoft.com/services/machine-learning/) (R, Python) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](http://rattle.togaware.com/) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | Нет | Да (только Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/), [Deep Water](https://www.h2o.ai/deep-water/) | Нет | Да (только Ubuntu) |
| **Deep Learning Tools** <br>Все средства используют GPU или ЦП |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) (Windows 2016) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | Y (Windows 2016) | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | Нет | Y (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | Y (Windows 2016) | Да|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe и Caffe2](https://github.com/caffe2/caffe2) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](http://pytorch.org/)| Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Сервер модели MXNet ](https://github.com/awslabs/mxnet-model-server) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow Serving](https://www.tensorflow.org/serving/) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA, cuDNN, NVIDIA Driver](https://developer.nvidia.com/cuda-toolkit) | Да | Да |
| **Платформа для больших данных (только Devtest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* Локальный изолированный экземпляр [Spark](http://spark.apache.org/) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp;* Локальный экземпляр [Hadoop](http://hadoop.apache.org/) (HDFS, YARN) | Нет | Да |

## <a name="get-started"></a>Начало работы

### <a name="windows-data-science-vm"></a>Виртуальная машина для обработки и анализа данных
* См. дополнительные сведения о [создании виртуальной машины Windows для обработки и анализа данных](provision-vm.md). Дополнительные сведения о том, как выполнять разные задачи рамках в проекта по обработке и анализу данных на виртуальной машине Windows для обработки и анализа данных, см. в статье [10 задач, которые можно выполнить в виртуальной машине для обработки и анализа данных](vm-do-ten-things.md).

### <a name="linux-data-science-vm"></a>Виртуальная машина Linux для обработки и анализа данных
* См. дополнительные сведения о [создании виртуальной машины Linux (Ubuntu) для обработки и анализа данных](dsvm-ubuntu-intro.md). См. дополнительные сведения о [создании виртуальной машины CentOS для обработки и анализа данных в Azure](linux-dsvm-intro.md).
* Пошаговое руководство по некоторым общим задачам обработки и анализа данных на виртуальных машинах Linux (CentOS и Ubuntu) см. в статье [Обработка и анализ данных с использованием специально подготовленной виртуальной машины Linux](linux-dsvm-walkthrough.md).

