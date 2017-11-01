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
ms.date: 10/09/2017
ms.openlocfilehash: b9287c7151c96aaccbcda81c111cfe36ead5ab38
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2017
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Устранение неполадок при развертывании службы и настройке среды
Следующие сведения помогут вам определить причину возникновения ошибок при настройке среды управления моделями.

## <a name="model-management-environment"></a>Среда управления моделями
### <a name="owner-permission-required"></a>Требуется разрешение владельца
Для регистрации службы вычислений Машинного обучения Azure необходимо разрешение владельца подписки Azure.

Также разрешение владельца требуется для настройки кластера для развертывания веб-служб.

### <a name="resource-availability"></a>Доступность ресурсов
Чтобы подготовить ресурсы среды, в подписке должно быть достаточное количество ресурсов.

### <a name="subscription-caps"></a>Ограничения подписки
В вашей подписке может быть установлено ограничение на выставление счетов, которое может помешать вам подготовить ресурсы среды. Снимите это ограничение, чтобы выполнить подготовку.

### <a name="enable-debug-and-verbose-options"></a>Включение параметров debug и verbose
Чтобы во время подготовки среды отображались сведения об отладке и трассировке, используйте флаги `--debug` и `--verbose` в команде установки.

```
az ml env setup -l <loation> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>Развертывание службы
Следующие сведения могут помочь определить причину возникновения ошибок во время развертывания или вызова веб-службы.

### <a name="service-logs"></a>Журналы служб
Параметр `logs` интерфейса командной строки службы предоставляет данные журнала из Docker и Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Для дополнительных настроек журнала используйте параметр `--help` (или `-h`).

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>Параметры Debug и Verbose
Флаг `--debug` позволяет отображать журналы отладки по мере развертывания службы.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Флаг `--verbose` позволяет просматривать дополнительные сведения по мере развертывания службы.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>Включение журнала запросов в Application Insights
Установите для флага `-l` значение true при создании веб-службы, чтобы включить ведение журнала на уровне запросов. Журналы запросов записываются в экземпляр службы Application Insights для вашей среды в Azure. Выполните поиск этого экземпляра, используя имя среды, которое использовалось при выполнении команды `az ml env setup`.

- Установите для `-l` значение true при создании службы.
- Откройте службу Application Insights на портале Azure. При поиске экземпляра Application Insights используйте имя своей среды.
- В службе Application Insights выберите "Поиск" в верхнем меню, чтобы просмотреть результаты.
- Или же последовательно выберите `Analytics` > `Exceptions` > `exceptions take | 10`.


### <a name="add-error-handling-in-scoring-script"></a>Добавление обработки ошибок в скрипт оценки
Используйте обработку исключений в функции **run** вашего скрипта `scoring.py` для возврата сообщения об ошибке в составе выходных данных веб-службы.

Пример на Python:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>Другие распространенные проблемы
- Если команду `env setup` выполнить не удается, убедитесь, что в подписке доступно достаточное количество ядер.
- Не используйте символ подчеркивания (_) в имени веб-службы (как в *my_webservice*).
- Повторите попытку, если при вызове веб-службы возникает ошибка **502 — Недопустимый шлюз**. Обычно это означает, что контейнер еще не развернут в кластере.
- Если при создании службы возникает ошибка **CrashLoopBackOff**, проверьте журналы. Она обычно является результатом отсутствующих зависимостей в функции **init**.
