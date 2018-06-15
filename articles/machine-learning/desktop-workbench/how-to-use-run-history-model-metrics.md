---
title: Как использовать компоненты "Журнал выполнения" и "Метрики модели" в Azure Machine Learning Workbench | Документация Майкрософт
description: Руководство по использованию компонентов "Журнал выполнения" и "Метрики модели" Azure Machine Learning Workbench
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: df29117235e890a9b20619744df6320f298a73b2
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831875"
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>Как использовать компоненты "Журнал выполнения" и "Метрики модели" в Azure Machine Learning Workbench

Azure Machine Learning Workbench поддерживает выполнение экспериментов по обработке и анализу данных с помощью компонентов **Журнал выполнения** и **Метрики модели**.
**Журнал выполнения** позволяет отслеживать выходные данные экспериментов машинного обучения, а также позволяет выполнять фильтрацию и сравнение их результатов.
**Метрики модели** можно регистрировать на любом этапе выполнения скриптов. При этом отслеживаются самые важные значения эксперимента по обработке и анализу данных.
В этой статье описывается, как эффективно использовать эти компоненты, чтобы ускорить проведение экспериментов по обработке и анализу данных и повысить их качество.

## <a name="prerequisites"></a>предварительным требованиям
Прежде чем приступить к выполнению этого руководства, вам понадобится сделать следующее:
* [Создать и установить службу "Машинное обучение Azure"](../service/quickstart-installation.md).
- [Создать проект](../service/quickstart-installation.md).


## <a name="azure-ml-logging-api-overview"></a>Обзор API ведения журналов службы "Машинное обучение Azure"
Доступ к API ведения журналов службы [Машинное обучение Azure](reference-logging-api.md) можно получить через модуль **azureml.logging** в Python (который устанавливается вместе с Azure ML Workbench). Импортировав этот модуль, можно использовать метод **get_azureml_logger** для создания экземпляра объекта **logger**.
Затем можно использовать метод **log** объекта logger для хранения пар "ключ — значение", полученных при выполнении скриптов Python.
Сейчас ведение журнала метрик модели типов скаляр и список поддерживается, как показано ниже.

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
Средство ведения журнала можно легко использовать в проектах Azure ML Workbench. Соответствующие сведения представлены в этой статье.

## <a name="create-a-project-in-azure-ml-workbench"></a>Создание проекта в Azure ML Workbench
Если у вас еще нет проекта, его можно создать, используя сведения из статьи [Create Azure Machine Learning preview accounts and install Azure Machine Learning Workbench](../service/quickstart-installation.md) (Создание учетных записей для предварительной версии службы "Машинное обучение Azure" и установка Azure Machine Learning Workbench). На **панели мониторинга проекта** можно открыть скрипт **iris_sklearn.py** (как показано).

![доступ к скрипту из вкладки "Файлы"](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

Этот скрипт можно использовать в качестве руководства по ожидаемой реализации ведения журналов метрик модели в службе "Машинное обучение Azure".

## <a name="parameterize-and-log-model-metrics-from-script"></a>Параметризация и запись метрик модели в журнал из скрипта
В скрипте **iris_sklearn.py** ожидаемый шаблон для импорта и создания средства ведения журнала в Python можно сократить до следующих строк кода.

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

После создания можно вызвать метод **log** с любой парой "имя — значение".

По завершении разработки часто бывает полезно параметризовать скрипты для передачи значений через командную строку.
В следующем примере показано, как принять параметры командной строки (если они есть) с помощью стандартных библиотек Python.
Этот скрипт принимает один параметр для скорости регуляризации (*reg*), использующийся для обучения модели классификации с целью повышения ее *точности* без переобучения.
Затем эти переменные вносятся в журнал как *Скорость регуляризации* и *Точность*, чтобы определить модель с оптимальными результатами.

```Python
# change regularization rate and you will likely get a different accuracy.
reg = 0.01
# load regularization rate from argument if present
if len(sys.argv) > 1:
    reg = float(sys.argv[1])

print("Regularization rate is {}".format(reg))

# log the regularization rate
run_logger.log("Regularization Rate", reg)

# train a logistic regression model on the training set
clf1 = LogisticRegression(C=1/reg).fit(X_train, Y_train)
print (clf1)

# evaluate the test set
accuracy = clf1.score(X_test, Y_test)
print ("Accuracy is {}".format(accuracy))

# log accuracy
run_logger.log("Accuracy", accuracy)
```

Выполняя эти действия в скриптах, можно обеспечить оптимальное использование компонента **Журнал выполнения**.

## <a name="launch-runs-from-project-dashboard"></a>Запуск с панели мониторинга проекта
Вернувшись к **панели мониторинга проекта**, можно запустить **отслеживаемое выполнение**, выбрав скрипт **iris_sklearn.py** и введя параметр **скорости регуляризации** в поле ввода **Аргументы**.

![ввод параметров и запуск выполнений](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

Так как при запуске отслеживаемых выполнений Azure ML Workbench не блокируется, можно запустить несколько выполнений параллельно.
Состояние каждого отслеживаемого выполнения отображается на **панели заданий**, как показано.

![отслеживание выполнений на панели заданий](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

Таким образом обеспечивается оптимальное использование ресурсов без необходимости последовательного выполнения каждого задания.

## <a name="view-results-in-run-history"></a>Просмотр результатов в журнале выполнения
Ход выполнения и результаты отслеживаемых выполнений доступны для анализа в **журнале выполнения** Azure ML Workbench.
**Журнал выполнения** предоставляет три представления:
- панель мониторинга
- Сведения
- Сравнение

Представление **Мониторинг** предоставляет данные всех выполнений указанного скрипта в графическом и табличном виде.
В представлении **Сведения** содержатся все данные, созданные в ходе конкретного выполнения скрипта, включая метрики журнала и выходные файлы (например, готовые к просмотру графики). В представлении **Сравнение** можно сравнить результаты двух или трех выполнений, включая также метрики журнала и выходные файлы.

После восьми отслеживаемых выполнений **iris_sklearn.py** были записаны значения параметра **скорости регуляризации** и **точности**, на примере которых можно показать, как использовать просмотры журнала выполнения.

### <a name="run-history-dashboard"></a>Панель мониторинга журнала выполнения
Результаты всех восьми выполнений можно просмотреть на **панели мониторинга журнала выполнения**.
Так как скрипт **iris_sklearn.py** регистрирует значения *Скорость регуляризации* и *Точность*, на **панели мониторинга журнала выполнения** по умолчанию отображаются диаграммы для этих значений.

![панель мониторинга журнала выполнения](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

**Панель мониторинга журнала выполнения** можно настроить таким образом, чтобы записанные в журнале значения также отображались в сетке.  Если щелкнуть значок **настройки**, отобразится диалоговое окно **List View Customization** (Настройка представления списка).

![настройка сетки панели мониторинга журнала выполнения](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

Все значения, зарегистрированные во время отслеживаемых выполнений, доступны для отображения. Если выбрать значения **Скорость регуляризации** и **Точность**, они будут добавлены в сетку.

![зарегистрированные значения в настраиваемой сетке](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

Вы можете легко найти интересующие выполнения, наведя указатель мыши на точки на диаграммах.  В этом случае в выполнении 7 мы получили высокую точность и малую длительность.

![поиск интересующего выполнения](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

Если щелкнуть точку, связанную с выполнением 7, на любой диаграмме или выбрать ссылку на выполнение 7 в сетке, отобразится представление **Run History Details** (Сведения о журнале выполнения).

### <a name="run-history-details"></a>Сведения о журнале выполнения
В этом представлении отображаются все результаты и артефакты выполнения 7.

![сведения о журнале выполнения](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

Кроме того, с помощью представления **Run History Details** (Сведения о журнале выполнения) можно **скачать** файлы, записанные в папку **./outputs** (эти файлы используются в облачном хранилище Azure ML Workbench для журнала выполнения, о котором рассказывается в другой статье).

Наконец, с помощью представления **Run History Details** (Сведения о журнале выполнения) можно восстановить состояние проекта во время этого выполнения.
Если нажать кнопку **Восстановить**, появится диалоговое окно подтверждения, как показано ниже.

![подтверждение восстановления выполнения](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

В случае подтверждения файлы могут быть перезаписаны или удалены, поэтому используйте эту функцию осторожно.

### <a name="run-history-comparison"></a>Сравнение журнала выполнения
Если выбрать два или три выполнения на **панели мониторинга журнала выполнения** и щелкнуть **Сравнить**, появится представление **Run History Comparison** (Сравнение журнала выполнения).
Кроме того, представление **Run History Comparison** (Сравнение журнала выполнения) можно открыть, щелкнув **Сравнить** и выбрав выполнение в представлении **Run History Details** (Сведения о журнале выполнения).
В любом случае с помощью представления **Run History Comparison** (Сравнение журнала выполнения) можно параллельно сравнить зарегистрированные результаты и артефакты из двух или трех выполнений.

![сравнение журнала выполнения](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

Это представление особенно полезно для сравнения графиков, но в общем здесь можно сравнивать любые свойства выполнений.

### <a name="command-line-interface"></a>Интерфейс командной строки
Azure Machine Learning Workbench также предоставляет доступ к журналу выполнения через **интерфейс командной строки**.
Чтобы получить доступ к **интерфейсу командной строки**, щелкните меню **Открыть командную строку**, как показано ниже.

![открыть командную строку](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

Доступ к командам, доступным для журнала выполнения, можно получить, выполнив команду `az ml history`. Интерактивную справку по командам можно открыть, добавив флаг `-h`.
```
$ az ml history -h

Group
    az ml history: View run history of machine learning experiments.

Commands:
    compare : Compare two runs.
    download: Download all the artifacts from a run into the destination path.
    info    : Details about one run.
    last    : Get detail about most recent run.
    list    : List runs.
    promote : Promote Artifacts.
```
Эти команды предоставляют те же функции и возвращают те же данные, которые показаны в представлении **Run History Views** (Просмотры журнала выполнения).
Например, результаты последнего выполнения можно отобразить как объект JSON.
```
$ az ml history last
{
  "Accuracy": 0.6792452830188679,
  "Regularization Rate": 0.078125,
  "attachments": "control_log, control_log.txt, driver_log, driver_log.txt, pid.txt, dataprep/backgroundProcess.log, dataprep/backgroundProcess_Engine.log, dataprep/backgroundProcess_EngineHost.log, dataprep/backgroundProcess_ProjectProvider.log, dataprep/backgroundProcess_Sampling.log, dataprep/backgroundProcess_Telemetry.log, outputs/cm.png, outputs/model.pkl, outputs/sdk_debug.txt, outputs/roc.png",
  "created_utc": "2017-09-08T00:58:01.611105+00:00",
  "description": null,
  "duration": "0:00:04.892389",
  "end_time_utc": "2017-09-08T00:58:07.951403+00:00",
  "experiment_id": "ce92d0a9-3e2c-4d51-85de-93ef22249ce1",
  "heartbeat_enabled": true,
  "hidden": false,
  "name": null,
  "parent_run_id": null,
  "properties": {
    "CommitId": "e77a5f0df2af1a482bbe39b70bfbb16b62228cb3"
  },
  "run_id": "IrisDemo_1504832281116",
  "run_number": 8,
  "script_name": "iris_sklearn.py",
  "start_time_utc": "2017-09-08T00:58:03.059014+00:00",
  "status": "Completed",
  "target": "local",
  "user_id": "e9fafe06-b0e4-4154-8374-aae34f9977b2"
}
```
Кроме того, список всех выполнений можно отобразить в табличном формате.
```
$ az ml history list -o table
  Accuracy    Regularization Rate  Duration        Run_id                  Script_name      Start_time_utc                    Status
----------  ---------------------  --------------  ----------------------  ---------------  --------------------------------  ---------
  0.679245               0.078125  0:00:04.892389  IrisDemo_1504832281116  iris_sklearn.py  2017-09-08T00:58:03.059014+00:00  Completed
  0.679245               0.15625   0:00:04.734956  IrisDemo_1504832255198  iris_sklearn.py  2017-09-08T00:57:38.507196+00:00  Completed
  0.660377               0.3125    0:00:04.913762  IrisDemo_1504832234904  iris_sklearn.py  2017-09-08T00:57:16.849881+00:00  Completed
  0.660377               0.625     0:00:06.107764  IrisDemo_1504832210767  iris_sklearn.py  2017-09-08T00:56:54.602813+00:00  Completed
  0.641509               1.25      0:00:04.742727  IrisDemo_1504832171373  iris_sklearn.py  2017-09-08T00:56:13.879280+00:00  Completed
  0.660377               2.5       0:00:04.915401  IrisDemo_1504832148526  iris_sklearn.py  2017-09-08T00:55:52.937083+00:00  Completed
  0.641509               5         0:00:04.730627  IrisDemo_1504832127172  iris_sklearn.py  2017-09-08T00:55:29.612382+00:00  Completed
  0.641509              10         0:00:06.059082  IrisDemo_1504832109906  iris_sklearn.py  2017-09-08T00:55:14.739806+00:00  Completed

```
**Интерфейс командной строки** — альтернативный путь доступа к возможностям Azure Machine Learning Workbench.

## <a name="next-steps"></a>Дальнейшие действия
Эти возможности позволяют упростить проведение экспериментов по обработке и анализу данных.
Мы надеемся, что они пригодятся вам. Мы будем признательны за ваш отзыв.
Это просто начальная реализация. Мы запланировали большое количество улучшений.
Мы будем рады внедрить их в Azure Machine Learning Workbench. 
