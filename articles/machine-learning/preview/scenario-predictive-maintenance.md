---
title: "Реальный сценарий прогнозного обслуживания | Документация Майкрософт"
description: "Реальный сценарий прогнозного обслуживания с помощью PySpark."
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: 0299e73aecca3b3e5714b37c8b0b776ec8561e29
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2018
---
# <a name="predictive-maintenance-real-world-scenario"></a>Реальный сценарий прогнозного обслуживания.

Незапланированный простой оборудования может оказывать неблагоприятное влияние на предприятие. Поэтому важно поддерживать рабочее состояние оборудования, используемого в области, чтобы обеспечить максимально эффективное использование ресурсов и добиться максимальной производительности, сводя к минимуму время затратного незапланированного простоя. Раннее определение проблем позволяет выделить ограниченные ресурсы обслуживания с наименьшими затратами, а также повысить качество и эффективность выполнения процессов логистической цепочки. 

В этом сценарии исследуется относительно [крупномасштабный симулированный набор данных](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data). Вы ознакомитесь со всеми этапами проекта по обработке и анализу данных с использованием прогнозного обслуживания: прием данных, проектирование признаков, создание модели, а также операционализация и развертывание модели. Код для всего процесса написан в записных книжках Jupyter на языке PySpark в среде Azure ML Workbench. Окончательная модель развертывается с помощью службы Azure "Управление моделями Машинного обучения" для прогнозирования сбоев оборудования в реальном времени.   

## <a name="link-to-the-gallery-github-repository"></a>Ссылка на репозиторий коллекции на GitHub

Вот ссылка на общедоступный репозиторий GitHub: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Обзор варианта использования

Основная проблема, с которой сталкиваются предприятия тяжелой промышленности, — это значительные затраты, связанные с задержкой из-за технических неполадок. Большинство предприятий заинтересованы в упреждающем прогнозировании этих проблем, позволяющем предотвратить их еще до возникновения. Они стараются снизить расходы за счет сокращения времени простоя и по возможности повысить безопасность. 

Этот сценарий принимает идеи из [модуля playbook для прогнозного обслуживания](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance), демонстрируя создание прогнозной модели для имитируемого набора данных. Пример данных является производным от общих элементов, используемых во многих сценариях прогнозного обслуживания.

При использовании смоделированных данных компании сталкиваются с такой сложностью, как прогнозирование проблем, вызванных сбоем компонентов. В сфере бизнеса вопрос звучит так: *Какова вероятность сбоя компьютера из-за сбоя компонента*? Эта проблема форматируется как проблема многоклассовой классификации (несколько компонентов на каждом компьютере). Для создания прогнозной модели используется алгоритм машинного обучения. Эта модель обучается на основе данных журнала, собранных с компьютеров. В этом сценарии пользователь выполнит несколько шагов реализации этой модели в среде Azure Machine Learning Workbench.

## <a name="prerequisites"></a>Необходимые компоненты

* [Учетная запись Azure](https://azure.microsoft.com/en-us/free/) (доступны бесплатные пробные версии).
* Установленная копия [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md). Чтобы установить эту программу и создать рабочую область, выполните инструкции из [краткого руководства по установке](./quickstart-installation.md).
* Для практического применения машинного обучения Azure требуется локальная среда развертывания и [учетная запись службы "Управление моделями"](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview).

Этот пример можно выполнить в любом контексте вычислений AML Workbench. Но мы рекомендуем использовать не менее 16 ГБ памяти. Этот сценарий был разработан и протестирован на компьютере под управлением Windows 10 с удаленным доступом к [виртуальной машине Linux (Ubuntu) для обработки и анализа данных](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) категории DS4_V2 Standard.

Ввод модели в эксплуатацию был выполнен с помощью версии 0.1.0a22 интерфейса командной строки машинного обучения Azure.

## <a name="create-a-new-workbench-project"></a>Создание проекта в Workbench

Создайте проект, используя в качестве шаблона следующий пример:
1.  Откройте Azure Machine Learning Workbench.
2.  На странице **Projects** (Проекты) щелкните знак **+** и выберите **New Project** (Создать проект).
3.  В области **Create New Project** (Создание проекта) введите информацию о новом проекте.
4.  В поле поиска **Search Project Templates** (Поиск шаблонов проектов) введите Predictive Maintenance (Прогнозное обслуживание) и выберите шаблон.
5.  Нажмите кнопку **Создать**.

## <a name="prepare-the-notebook-server-computation-target"></a>Подготовка целевого объекта вычислений для сервера записных книжек

Чтобы запустить проект на локальном компьютере, в меню `File` AML Workbench выберите пункт `Open Command Prompt` или `Open PowerShell CLI`. Интерфейс CLI предоставляет доступ к службам Azure с помощью команд `az`. Сначала войдите в свою учетную запись Azure с помощью команды:

```
az login
``` 

Эта команда предоставляет ключ аутентификации для использования с URL-адресом `https:\\aka.ms\devicelogin`. CLI ожидает, пока операция входа в устройство не вернет данные о подключении. Затем, если у вас выполнена локальная установка [docker](https://www.docker.com/get-docker), подготовьте локальную вычислительную среду с помощью следующих команд:

```
az ml experiment prepare --target docker --run-configuration docker
```

Выполнять эту задачу рекомендуется на [виртуальной машине для обработки и анализа данных для Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) с учетом требований к памяти и диску. После настройки DSVM подготовьте удаленную среду Docker с помощью следующих двух команд:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

После подключения к удаленному контейнеру Docker подготовьте вычислительную среду DSVM Docker с помощью следующей команды: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Подготовив вычислительную среду Docker, откройте сервер записных книжек Jupyter, используя вкладку записных книжек в AML Workbench, или запустите сервер в браузере с помощью команды: 
```
az ml notebook start
```

Примеры записных книжек хранятся в каталоге `Code`. Записные книжки настроены на последовательный запуск, начиная с первой (`Code\1_data_ingestion.ipynb`). При открытии каждой записной книжки вам будет предложено выбрать ядро для вычислений. Выберите ядро `[Project_Name]_Template [Connection_Name]` для выполнения на ранее настроенной машине DSVM.

## <a name="data-description"></a>Описание данных

[Смоделированные данные](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) состоят из пяти файлов данных с разделителями-запятыми (CSV-файлы). Подробные описания наборов данных см. по следующим ссылкам.

* [machines.](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv) Признаки, дифференцирующие каждый компьютер. Например, срок использования и модель.
* [error.](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv) Журнал ошибок содержит список некритических ошибок, которые не прерывают работу компьютера. Эти ошибки не рассматриваются в качестве сбоев, хотя они могут спрогнозировать событие сбоя в будущем. Дата и время ошибок округляются до ближайшего часа, так как данные телеметрии собираются по часовому тарифу.
* [maintenance.](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv) Журнал обслуживания содержит записи запланированных и незапланированных обслуживаний. Запланированное обслуживание представляет собой регулярную проверку компонентов. Незапланированное обслуживание может возникать из-за механической неисправности или других причин снижения производительности. Дата и время обслуживания округляются до ближайшего часа, так как данные телеметрии собираются по часовому тарифу.
* [telemetry.](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv) Данные телеметрии состоят из измерений временных рядов, полученных от множества датчиков внутри каждого компьютера. Данные записываются с усреднением значений датчика, получаемых через каждый час.
* [failures.](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv) Сбои соответствуют записи о замене компонентов в журнале обслуживания. Каждая запись содержит идентификатор компьютера, тип компонента, дату и время замены. Эти записи используются для создания меток машинного обучения, которые модель пытается спрогнозировать.

Просмотрите сценарий [приема данных](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) записной книжки Jupyter в разделе кода, чтобы скачать наборы необработанных данных из репозитория GitHub и создать наборы данных PySpark для этого анализа.

## <a name="scenario-structure"></a>Структура сценария
Содержимое этого сценария доступно в [репозитории GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

[Файл сведений](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) описывает рабочий процесс подготовки данных, создания модели и последующего развертывания решения в рабочей среде. Каждый шаг рабочего процесса включается в записную книжку Jupyter в папке [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) в репозитории.   

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) загружает пять входных CSV-файлов, выполняет предварительную очистку данных и визуализацию. Записная книжка преобразует каждый набор данных в формат PySpark и сохраняет его в контейнере больших двоичных объектов Azure. Так их можно использовать в записной книжке проектирования признаков.

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb). С помощью необработанного набора данных из большого двоичного объекта Azure признаки модели создаются стандартным образом с использованием временных рядов для данных телеметрии, ошибок и обслуживания. На основе сведений о заменах компонентов, вызванных сбоями, создаются метки модели с описанием неисправных компонентов. Данные о признаках с метками сохраняются в большом двоичном объекте Azure для записной книжки создания модели.

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb) считывает набор данных о компонентах с метками, разбивает данные на наборы для обучения и разработки на основании меток даты и времени. Эта записная книжка создает эксперимент с моделями `pyspark.ml.classification`. Данные для обучения векторизуются, что позволяет пользователю экспериментировать с `DecisionTreeClassifier` или `RandomForestClassifier` и изменять гиперпараметры, чтобы найти наиболее эффективные модели. Производительность определяется путем расчета статистики мер для набора данных разработки. Эти статистические данные передаются для отслеживания в экран времени выполнения AML Workbench. При каждом запуске записная книжка сохраняет полученную модель на локальный диск компьютера, на котором выполняется ядро записной книжки Jupyter. 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb). На основе последней модели, сохраненной на локальном диске (где выполняется ядро записной книжки Jupyter), создаются компоненты для развертывания модели в веб-службе Azure. Весь набор ресурсов для ввода в эксплуатацию сжимается в файл `o16n.zip`, который сохраняется в другом контейнере больших двоичных объектов Azure. Сжатый ZIP-файл содержит следующие данные.

* `service_schema.json` — файл определения схемы для развертывания; 
* `pdmscore.py` — функции Init() и run() для веб-службы Azure;
* `pdmrfull.model` — каталог с описанием модели.
    
 Записная книжка проверяет функции по определению модели, а затем упаковывает эти ресурсы для развертывания. В конце записной книжки добавляются инструкции по развертыванию.

## <a name="conclusion"></a>Заключение

Этот сценарий предоставляет пользователю общие сведения о создании комплексного решения для прогнозного обслуживания с помощью PySpark в среде записной книжки Jupyter в Azure ML Workbench. В этом примере сценария также подробно описывается развертывание модели с помощью среды управления моделями Машинного обучения Azure для получения прогнозов сбоев оборудования в реальном времени.

## <a name="references"></a>Ссылки

Этот пример использования был ранее применен для нескольких платформ:

* [Сборник тренировочных заданий для шаблона решения Cortana Intelligence для прогнозируемого обслуживания в аэрокосмических и других компаниях](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Predictive Maintenance Modelling Guide](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1) (Руководство по моделированию прогнозного обслуживания)
* [Predictive Maintenance Modeling Guide using SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1) (Руководство по моделированию прогнозного обслуживания с помощью служб R SQL)
* [Predictive Maintenance Modelling Guide Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1) (Руководство по моделированию прогнозного обслуживания с помощью записной книжки Python)
* [Predictive Maintenance using PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark) (Прогнозное обслуживание с помощью PySpark)

## <a name="next-steps"></a>Дополнительная информация

В Azure Machine Learning Workbench есть много других примеров сценариев, которые описывают дополнительные возможности продукта. 