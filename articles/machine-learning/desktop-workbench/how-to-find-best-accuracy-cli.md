---
title: Поиск выполнений с наибольшей точностью и наименьшей длительностью в Azure Machine Learning Workbench | Документация Майкрософт
description: Полный вариант использования, в котором показано, как находить выполнения с наибольшей точностью с помощью интерфейса командной строки и Azure Machine Learning Workbench.
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, jmartens, jasonwhowell
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 077af8b5d3367dd2188cbd6e5d76aaf52512a1e8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830805"
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>Поиск выполнений с максимальной точностью и наименьшей длительностью
При множестве запусков один из вариантов использования — нахождение запусков с наибольшей точностью. Один из возможных подходов — использовать интерфейс командной строки с запросом [JMESPath](http://jmespath.org/). Дополнительные сведения об использовании JMESPath в Azure CLI см. в разделе [Использование запросов JMESPath в Azure CLI 2.0](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). В следующем примере создаются четыре запуска со значениями точности 0, 0,98, 1 и 1. Запуски фильтруются, если они находятся в диапазоне `[MaxAccuracy-Threshold, MaxAccuracy]`, где `Threshold = .03`.

## <a name="sample-data"></a>Пример данных
Если выполнения со значением `Accuracy` отсутствуют, указанные ниже действия позволят создать выполнения для запроса.

Сначала создайте файл Python в Azure Machine Learning Workbench, присвойте ему имя `log_accuracy.py` и вставьте в него следующий код.
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

Затем создайте файл `run.py` и вставьте в него следующий код:
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

Наконец, откройте интерфейс командной строки в Workbench и выполните команду `python run.py` для отправки четырех экспериментов. После завершения скрипта на вкладке `Run History` должны появиться четыре дополнительных запуска.

## <a name="query-the-run-history"></a>Запроса журнала выполнения
Первая команда находит максимальное значение точности.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

Используя это максимальное значение точности `1` и пороговое значение `0.03`, вторая команда фильтрует выполнения с помощью `Accuracy`, а затем отсортирует их по возрастанию `duration`.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Если требуется установить строгую проверку верхней границы, формат запроса будет иметь вид ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``.

При использовании PowerShell приведенный ниже код будет использовать локальные переменные для хранения порогового значения и максимальной точности.
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о записи в журнал см. в статье [Как использовать компоненты "Журнал выполнения" и "Метрики модели" в Azure Machine Learning Workbench](how-to-use-run-history-model-metrics.md).    
