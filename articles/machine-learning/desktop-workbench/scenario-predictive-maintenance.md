---
title: Прогнозное обслуживание в реальных сценариях | Документация Майкрософт
description: Прогнозное обслуживание в реальных сценариях с использованием PySpark
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: f3f24a9b269205dd77ec3301b2650ee7a03f435b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832702"
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>Прогнозное обслуживание в реальных сценариях

Незапланированный простой оборудования может оказывать неблагоприятное влияние на предприятие. Важно поддерживать рабочее состояние используемого оборудования, чтобы обеспечить максимально эффективное использование ресурсов, добиться максимальной производительности и минимизировать время затратных незапланированных простоев. Раннее определение проблем позволяет выделить ограниченные ресурсы обслуживания с наименьшими затратами, а также повысить качество и эффективность выполнения процессов логистической цепочки. 

В этом сценарии исследуется относительно [крупномасштабный симулированный набор данных](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data). Вы ознакомитесь со всеми этапами проекта по обработке и анализу данных с использованием прогнозного обслуживания: прием данных, проектирование признаков, создание модели, а также операционализация и развертывание модели. Код для всего процесса написан в записной книжке Jupyter на языке PySpark в среде Azure Machine Learning Workbench. Окончательная модель развертывается с помощью службы "Управление моделями Машинного обучения Azure" для прогнозирования сбоев оборудования в реальном времени.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Репозиторий коллекции Cortana Intelligence на GitHub

Коллекция Cortana Intelligence для руководства по прогнозному обслуживанию — это общедоступный репозиторий GitHub ([https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)), где можно сообщить о проблемах и предложить свои идеи.


## <a name="use-case-overview"></a>Обзор варианта использования

Основная проблема, с которой сталкиваются предприятия тяжелой промышленности, — это значительные затраты, связанные с задержкой из-за технических неполадок. Большинство предприятий заинтересованы в упреждающем прогнозировании этих проблем, позволяющем предотвратить их еще до возникновения. Они стараются снизить расходы за счет сокращения времени простоя и по возможности повысить безопасность. 

Этот сценарий принимает идеи из [модуля playbook для прогнозного обслуживания](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance), демонстрируя создание прогнозной модели для имитируемого набора данных. Пример данных является производным от общих элементов, используемых во многих сценариях прогнозного обслуживания.

При использовании смоделированных данных компании сталкиваются с такой сложностью, как прогнозирование проблем, вызванных сбоем компонентов. В сфере бизнеса вопрос звучит так: *Какова вероятность сбоя компьютера из-за сбоя компонента*? Эта проблема форматируется как проблема многоклассовой классификации (несколько компонентов на каждом компьютере). Для создания прогнозной модели используется алгоритм машинного обучения. Эта модель обучается на основе данных журнала, собранных с компьютеров. В этом сценарии пользователь выполнит несколько шагов для реализации модели в среде Machine Learning Workbench.

## <a name="prerequisites"></a>предварительным требованиям

* [Учетная запись Azure](https://azure.microsoft.com/free/) (доступны бесплатные пробные версии).
* Установленная копия [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). Чтобы установить эту программу и создать рабочую область, выполните инструкции из [краткого руководства по установке](../service/quickstart-installation.md).
* Для практического применения Машинного обучения Azure требуется локальная среда развертывания и [учетная запись службы "Управление моделями Машинного обучения Azure"](model-management-overview.md).

Этот пример можно выполнить в любом контексте вычислений Machine Learning Workbench. Но мы рекомендуем использовать не менее 16 ГБ памяти. Этот сценарий был разработан и протестирован на компьютере под управлением Windows 10 с удаленным доступом к [виртуальной машине Linux (Ubuntu) для обработки и анализа данных (DSVM)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) категории DS4_V2 Standard.

Ввод модели в эксплуатацию был выполнен с помощью версии 0.1.0a22 интерфейса командной строки Машинного обучения Azure.

## <a name="create-a-new-workbench-project"></a>Создание проекта в Workbench

Создайте проект, используя в качестве шаблона следующий пример:
1.  Откройте Machine Learning Workbench.
2.  На странице **Projects** (Проекты) щелкните **+** и выберите **New Project** (Создать проект).
3.  В области **Create New Project** (Создание проекта) введите информацию о новом проекте.
4.  В поле поиска **Search Project Templates** (Поиск шаблонов проектов) введите Predictive Maintenance (Прогнозное обслуживание) и выберите шаблон **Predictive Maintenance** (Прогнозное обслуживание).
5.  Нажмите кнопку **Создать**.

## <a name="prepare-the-notebook-server-computation-target"></a>Подготовка целевого объекта вычислений для сервера записных книжек

Чтобы запустить проект на локальном компьютере, в меню **File** (Файл) Machine Learning Workbench выберите **Open Command Prompt** (Открыть командную строку) или **Open PowerShell CLI** (Открыть PowerShell CLI). Интерфейс CLI предоставляет доступ к службам Azure с помощью команд `az`. Сначала войдите в свою учетную запись Azure с помощью команды:

```
az login
``` 

Эта команда предоставляет ключ аутентификации для использования с URL-адресом https:\\aka.ms\devicelogin. CLI ожидает, пока операция входа в устройство не вернет данные о подключении. Затем, если у вас выполнена локальная установка [Docker](https://www.docker.com/get-docker), подготовьте локальную вычислительную среду с помощью следующей команды:

```
az ml experiment prepare --target docker --run-configuration docker
```

Мы советуем выполнять эту задачу на [DSVM под управлением Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) с учетом требований к памяти и диску. После настройки DSVM подготовьте удаленную среду Docker с помощью следующих двух команд:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

После подключения к удаленному контейнеру Docker подготовьте вычислительную среду DSVM Docker с помощью следующей команды: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Подготовив вычислительную среду Docker, откройте сервер записных книжек Jupyter, используя вкладку **Записные книжки** в Azure Machine Learning Workbench, или запустите сервер в браузере с помощью команды: 

```
az ml notebook start
```

Примеры записных книжек хранятся в каталоге Code. Записные книжки настроены на последовательный запуск, начиная с первой (Code\1_data_ingestion.ipynb). При открытии каждой записной книжки вам будет предложено выбрать ядро для вычислений. Выберите ядро [имя_проекта]_Template [имя_подключения] для выполнения на ранее настроенной машине DSVM.

## <a name="data-description"></a>Описание данных

[Смоделированные данные](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) состоят из пяти файлов данных с разделителями-запятыми (CSV-файлы). Подробные описания наборов данных см. по следующим ссылкам.

* [machines.](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv) Признаки, дифференцирующие каждый компьютер. Например, срок использования и модель.
* [error.](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv) Журнал ошибок содержит список некритических ошибок, которые не прерывают работу компьютера. Эти ошибки не рассматриваются в качестве сбоев, хотя они могут спрогнозировать событие сбоя в будущем. Значения даты и времени ошибок округляются до ближайшего часа, так как данные телеметрии собираются по часовому тарифу.
* [maintenance.](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv) Журнал обслуживания содержит записи запланированных и незапланированных обслуживаний. Запланированное обслуживание представляет собой регулярную проверку компонентов. Незапланированное обслуживание может возникать из-за механической неисправности или других причин снижения производительности. Значения даты и времени обслуживания округляются до ближайшего часа, так как данные телеметрии собираются по часовому тарифу.
* [telemetry.](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv) Данные телеметрии состоят из измерений временных рядов, полученных от множества датчиков внутри каждого компьютера. Данные записываются с усреднением значений датчика, получаемых через каждый час.
* [failures.](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv) Сбои соответствуют записи о замене компонентов в журнале обслуживания. Каждая запись содержит идентификатор компьютера, тип компонента, дату и время замены. Эти записи используются для создания меток машинного обучения, которые модель пытается спрогнозировать.

Просмотрите сценарий [приема данных](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) записной книжки Jupyter в папке Code, чтобы скачать наборы необработанных данных из репозитория GitHub и создать наборы данных PySpark для этого анализа.

## <a name="scenario-structure"></a>Структура сценария
Содержимое этого сценария доступно в [репозитории GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

[Файл сведений](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) описывает рабочий процесс подготовки данных, создания модели и последующего развертывания решения в рабочей среде. Каждый шаг рабочего процесса включается в записную книжку Jupyter в папке [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) в репозитории.   

[Code\1_data_ingestion.ipynb.](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) Эта записная книжка загружает пять входных CSV-файлов, выполняет предварительную очистку данных и визуализацию. Записная книжка преобразует каждый набор данных в формат PySpark и сохраняет его в контейнере больших двоичных объектов Azure. Так их можно использовать в записной книжке проектирования признаков.

[Code\2_feature_engineering.ipynb.](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb) С помощью необработанного набора данных из большого двоичного объекта Azure признаки модели создаются стандартным образом с использованием временных рядов для данных телеметрии, ошибок и обслуживания. На основе сведений о заменах компонентов, вызванных сбоями, создаются метки модели с описанием неисправных компонентов. Данные о признаках с метками сохраняются в большом двоичном объекте Azure для записной книжки создания модели.

[Code\3_model_building.ipynb.](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb) Записная книжка создания модели считывает набор данных о признаках с метками, разбивает данные на наборы для обучения и разработки на основании меток даты и времени. Эта записная книжка создает эксперимент с моделями pyspark.ml.classification. Данные для обучения векторизуются. Пользователь может экспериментировать с **DecisionTreeClassifier** или **RandomForestClassifier** и изменять гиперпараметры, чтобы найти наиболее эффективные модели. Производительность определяется путем расчета статистики мер для набора данных разработки. Эти статистические данные передаются для отслеживания на экран среды выполнения Machine Learning Workbench. При каждом запуске записная книжка сохраняет полученную модель на локальный диск компьютера, на котором выполняется ядро записной книжки Jupyter. 

[Code\4_operationalization.ipynb.](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb) Эта записная книжка создает компоненты для развертывания модели в веб-службе Azure на основе последней модели, сохраненной на локальном диске (где выполняется ядро записной книжки Jupyter). Весь набор ресурсов для ввода в эксплуатацию сжимается в файл o16n.zip, который сохраняется в другом контейнере больших двоичных объектов Azure. Сжатый ZIP-файл содержит следующие данные.

* **service_schema.json** — файл определения схемы для развертывания; 
* **pdmscore.py** — функции **init()** и **run()**, необходимые для веб-службы Azure;
* **pdmrfull.model** — каталог с описанием модели.
    
Записная книжка проверяет функции по определению модели, а затем упаковывает эти ресурсы для развертывания. В конце записной книжки добавляются инструкции по развертыванию.

## <a name="conclusion"></a>Заключение

Этот сценарий предоставляет общие сведения о создании комплексного решения для прогнозного обслуживания с помощью PySpark в среде записной книжки Jupyter в Machine Learning Workbench. В этом примере сценария также подробно описывается развертывание модели с помощью среды "Управление моделями Машинного обучения" для получения прогнозов сбоев оборудования в реальном времени.

## <a name="references"></a>Ссылки

Ниже приведены ссылки, по которым приведены примеры других сценариев прогнозного обслуживания для разных платформ.

* [Сборник тренировочных заданий для шаблона решения Cortana Intelligence для прогнозируемого обслуживания в аэрокосмических и других компаниях](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Predictive Maintenance Modelling Guide](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1) (Руководство по моделированию прогнозного обслуживания)
* [Predictive Maintenance Modeling Guide using SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1) (Руководство по моделированию прогнозного обслуживания с помощью служб R SQL)
* [Predictive Maintenance Modelling Guide Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1) (Руководство по моделированию прогнозного обслуживания с помощью записной книжки Python)
* [Predictive Maintenance using PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark) (Прогнозное обслуживание с помощью PySpark)
* [Глубокое обучение по прогнозному обслуживанию](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>Дополнительная информация

В Machine Learning Workbench есть другие примеры сценариев, которые описывают дополнительные возможности продукта. 
