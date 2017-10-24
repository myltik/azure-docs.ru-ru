---
title: "Руководство по устранению неполадок развертывания службы \"Машинное обучение Azure\" | Документация Майкрософт"
description: "Руководство по устранению неполадок при развертывании и создании службы"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/05/2017
ms.openlocfilehash: 066a6a223692055c7855abc63667e345ee8dc2d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="model-management-troubleshooting"></a>Устранение неполадок управления моделями
Следующие сведения могут помочь определить причину возникновения ошибок во время развертывания или вызова веб-службы.
 
## <a name="1-service-logs"></a>1. Журналы служб
Параметр `logs` интерфейса командной строки службы предоставляет данные журнала из Docker и Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Для дополнительных настроек журнала используйте параметр `--help` (или `-h`).

```
az ml service logs realtime -h
```

## <a name="2-debug-and-verbose-options"></a>2. Параметры Debug и Verbose
Флаг `--debug` позволяет отображать журналы отладки по мере развертывания службы.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Флаг `--verbose` позволяет просматривать дополнительные сведения по мере развертывания службы.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

## <a name="3-app-insights"></a>3. Анализ приложения
Установите для флага `-l` значение true при создании веб-службы, чтобы включить ведение журнала на уровне запросов. Журналы запросов записываются в экземпляр службы Application Insights для вашей среды в Azure. Выполните поиск этого экземпляра, используя имя среды, которое использовалось при выполнении команды `az ml env setup`.

- Установите для `-l` значение true при создании службы.
- Откройте службу Application Insights на портале Azure. При поиске экземпляра Application Insights используйте имя своей среды.
- В службе Application Insights выберите "Поиск" в верхнем меню, чтобы просмотреть результаты.
- Или же последовательно выберите `Analytics` > `Exceptions` > `exceptions take | 10`.


## <a name="4-error-handling-in-script"></a>4. Обработка ошибок в скрипте
Используйте обработку исключений в функции **run** вашего скрипта `scoring.py` для возврата сообщения об ошибке в составе выходных данных веб-службы.

Пример на Python:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="5-other-common-problems"></a>5. Другие распространенные проблемы
- Если команду `env setup` выполнить не удается, убедитесь, что в подписке доступно достаточное количество ядер.
- Не используйте символ подчеркивания (_) в имени веб-службы (как в *my_webservice*).
- Повторите попытку, если при вызове веб-службы возникает ошибка **502 — Недопустимый шлюз**. Обычно это означает, что контейнер еще не развернут в кластере.
- Если при создании службы возникает ошибка **CrashLoopBackOff**, проверьте журналы. Она обычно является результатом отсутствующих зависимостей в функции **init**.