---
title: "Поиск запусков с наибольшей точность и наименьшей длительностью в Azure Machine Learning Workbench | Документация Майкрософт"
description: "Полный вариант использования, в котором показано, как находить запуски с наибольшей точностью с помощью CLI и Azure Machine Learning Workbench"
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 9e5c2cc0b9ec17154c5280850d971308abfc2eb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-find-runs-with-the-best-accuracy-and-lowest-duration"></a>Как найти операции запуска с максимальной точностью и минимальным временем выполнения
При множестве запусков один из вариантов использования — нахождение запусков с наибольшей точностью. Один из возможных подходов — использовать интерфейс командной строки (CLI) с запросом [JMESPath](http://jmespath.org/). Дополнительные сведения об использовании JMESPath в Azure CLI см. в [этой статье](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). В следующем примере создаются четыре запуска со значениями точности 0, 0,98, 1 и 1. Запуски фильтруются, если они находятся в диапазоне `[MaxAccuracy-Threshold, MaxAccuracy]`, где `Threshold = .03`.

## <a name="sample-data"></a>Пример данных
Если запуски со значением `Accuracy` отсутствуют, указанные ниже действия создадут запуски для запроса:

Сначала создайте файл Python в Workbench, присвойте ему имя `log_accuracy.py`и вставьте в него следующий код:
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

Наконец, откройте CLI в Workbench и выполните команду `python run.py` для отправки четырех экспериментов. После завершения скрипта на вкладке `Run History` должны появиться четыре дополнительных запуска.

## <a name="querying-the-run-history"></a>Запрос журнала выполнения
Первая команда находит максимальное значение точности.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

Используя это максимальное значение точности `1` и пороговое значение `0.03`, вторая команда отфильтрует запуски с помощью `Accuracy`, а затем отсортирует их по возрастанию `duration`.
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

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения о записи в журнал см. в статье [Как использовать компоненты "Журнал выполнения" и "Метрики модели" в Azure Machine Learning Workbench](how-to-use-run-history-model-metrics.md).    
