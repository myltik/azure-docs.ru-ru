---
title: Руководство по устранению неполадок развертывания службы "Машинное обучение Azure" | Документация Майкрософт
description: Руководство по устранению неполадок при развертывании и создании службы
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 7b8d2f4fb52c6d4faed8e813779e3b01d1817355
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>Устранение неполадок при развертывании службы и настройке среды
Следующие сведения помогут вам определить причину возникновения ошибок при настройке среды управления моделями.

## <a name="model-management-environment"></a>Среда управления моделями
### <a name="contributor-permission-required"></a>Разрешения уровня участника
Чтобы настроить кластер для развертывания веб-служб, вам нужен доступ к подписке или группе ресурсов уровня участника.

### <a name="resource-availability"></a>Доступность ресурсов
Чтобы подготовить ресурсы среды, в подписке должно быть достаточное количество ресурсов.

### <a name="subscription-caps"></a>Ограничения подписки
В вашей подписке может быть установлено ограничение на выставление счетов, которое может помешать вам подготовить ресурсы среды. Снимите это ограничение, чтобы выполнить подготовку.

### <a name="enable-debug-and-verbose-options"></a>Включение параметров debug и verbose
Чтобы во время подготовки среды отображались сведения об отладке и трассировке, используйте флаги `--debug` и `--verbose` в команде установки.

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
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
- Если на компьютере с ОС Windows pip-установка azure-cli-ml завершается с ошибкой `cannot find the path specified`, необходимо включить поддержку длинного формата пути. См. https://blogs.msdn.microsoft.com/jeremykuhne/2016/07/30/net-4-6-2-and-long-paths-on-windows-10/. 
- Если происходит сбой команды `env setup` с сообщением `LocationNotAvailableForResourceType`, возможно, используется неверное расположение (регион) для ресурсов машинного обучения. Убедитесь, что с параметром `-l` задано расположение `eastus2`, `westcentralus` или `australiaeast`.
- Если происходит сбой команды `env setup` с сообщением `Resource quota limit exceeded`, убедитесь, что в подписке достаточное количество ядер и что ресурсы не используются в других процессах.
- Если происходит сбой команды `env setup` с сообщением `Invalid environment name. Name must only contain lowercase alphanumeric characters`, убедитесь, что имя службы не содержит заглавных букв, знаков или символа подчеркивания (_) (как в *my_environment*).
- Если происходит сбой команды `service create` с сообщением `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)`, убедитесь, что имя службы содержит от 3 до 32 знаков, начинается и заканчивается строчными буквенно-цифровыми знаками, не содержит заглавных букв, знаков, кроме дефиса (-) и точки (. ), или символ подчеркивания (_) (как в *my_webservice*).
- Повторите попытку, если при вызове веб-службы возникает ошибка `502 Bad Gateway`. Обычно это означает, что контейнер еще не развернут в кластере.
- Если при создании службы возникает ошибка `CrashLoopBackOff`, проверьте журналы. Она обычно является результатом отсутствующих зависимостей в функции **init**.
