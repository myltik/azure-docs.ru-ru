---
title: Мониторинг на уровне платформы службы Azure Service Fabric | Документы Майкрософт
description: Ознакомьтесь с событиями на уровне платформы и журналами, используемыми для мониторинга и диагностики кластеров Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 31f23e3f8e792c6b61870c640f99ec3392a940d3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
---
# <a name="monitoring-the-cluster-and-platform"></a>Мониторинг кластера и платформы

Мониторинг на уровне платформы важен для определения правильной работы оборудования и кластера. Хотя Service Fabric может поддерживать работу приложений во время сбоя оборудования, важно понять, где произошел сбой: в самом приложении или в базовой инфраструктуре. Кроме того, необходимо наблюдать за своими кластерами, чтобы оптимально планировать производительность, что поможет принимать решение о добавлении или удалении оборудования.

Service Fabric предоставляет несколько событий структурированной платформы, например [события Service Fabric](service-fabric-diagnostics-events.md), с помощью EventStore и различных готовых журналов каналов. 

EventStore позволяет получить доступ к событиям кластера для каждой сущности (включая кластер, узлы, приложения, службы, секции, реплики и контейнеры) и предоставляет эти события через интерфейсы REST API и клиентскую библиотеку Service Fabric. EventStore позволяет отслеживать кластеры для разработки и тестирования, а также получать сведения о состоянии кластеров в рабочей среде в определенный момент времени. Дополнительные сведения см. в [обзоре EventStore](service-fabric-diagnostics-eventstore.md).

Кроме того, Service Fabric предоставляет следующие готовые каналы журнала для настройки конвейера мониторинга для кластеров в рабочей среде:

* [**Операционный канал:**](service-fabric-diagnostics-event-generation-operational.md)  
данные высокоуровневых операций, выполняемых Service Fabric и кластером, включая ожидаемые события для узла, данные нового развертываемого приложения или отката обновления и т. д.

* **Операционный канал (подробные сведения):**  
отчеты о работоспособности и решения для балансировки нагрузки.

* **Канал данных и обмена сообщениями:**  
критически важные журналы и события, создаваемые в системе обмена сообщениями (сейчас только ReverseProxy) и расположении данных (модели Reliable Services).

* **Канал данных и обмена сообщениями (подробные сведения):**  
некритические журналы с данными и сведениями системы обмена сообщениями в кластере (этот канал содержит очень большое число событий).

Кроме этих каналов, есть еще два структурированных канала "Источники событий", а также журналы, которые мы собираем для предоставления поддержки.

* [События Reliable Services](service-fabric-reliable-services-diagnostics.md)  
определенные события модели программирования.

* [События Reliable Actors](service-fabric-reliable-actors-diagnostics.md)  
определенные события модели программирования и счетчики производительности.

* Журналы поддержки:  
системные журналы, созданные Service Fabric только для использования нами при предоставлении поддержки.

Эти различные каналы охватывают большую часть компонентов ведения журнала на уровне платформы, которое рекомендуется использовать. Чтобы оптимизировать ведение журнала на уровне платформы, важно разобраться с моделью работоспособности, добавить настраиваемые отчеты о работоспособности и настраиваемые **счетчики производительности**, чтобы в режиме реального времени отслеживать влияние служб и приложений на кластер.

Чтобы воспользоваться преимуществами этих журналов, мы рекомендуем включать диагностику во время создания кластера. Если включить диагностику в развернутом кластере, система диагностики Microsoft Azure сможет распознавать операционный канал, каналы Reliable Services и Reliable Actors, а также хранить данные, как описано в инструкциях по [статистической обработке событий с помощью системы диагностики Azure](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Отчеты о работоспособности и нагрузке Azure Service Fabric

В Service Fabric используется собственная модель работоспособности, которая подробно описана в следующих статьях.

- [Общие сведения о наблюдении за работоспособностью системы в Service Fabric](service-fabric-health-introduction.md)
- [Проверка работоспособности службы и оповещение о проблемах](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Добавление настраиваемых отчетов о работоспособности Service Fabric](service-fabric-report-health.md)
- [Просмотр отчетов о работоспособности Service Fabric](service-fabric-view-entities-aggregated-health.md)

Мониторинг работоспособности крайне важен для многих аспектов работы службы. Отслеживать работоспособность особенно важно, когда Service Fabric выполняет обновление именованного приложения. После того как каждый домен обновления службы обновляется и становится доступен клиентам, для него проводится проверка работоспособности. Только после этого можно переходить к развертыванию службы в следующем домене обновления. Если достичь работоспособности не удается, выполняется откат развернутой службы, и приложение возвращается в известное рабочее состояние. В этот период до отката службы некоторые клиенты могут столкнуться с проблемами, но для большинства из них процесс пройдет незаметно. Устранение проблемы происходит довольно быстро и не требует вмешательства человека. Чем больше проверок работоспособности внедрено в код, тем устойчивее служба к проблемам развертывания.

Еще один аспект работоспособности службы — передача метрик из службы. Метрики очень важны в Service Fabric, так как они используются для балансировки использования ресурсов. Метрики также служат признаком работоспособности системы. Допустим, вы используете приложение с несколькими службами, в котором каждый экземпляр передает метрику количества запросов в секунду (RPS). Если одна из служб использует больше ресурсов, чем другая, Service Fabric перемещает экземпляры службы по кластеру, поддерживая равномерное использование ресурсов. Использование ресурсов подробно описано в статье [Управление потреблением ресурсов и нагрузкой в Service Fabric с помощью метрик](service-fabric-cluster-resource-manager-metrics.md).

Также метрики дают представление об эффективности службы. С их помощью вы можете убедиться, что служба работает с соблюдением заданных параметров. Например, если статистические данные показывают, что в 9:00 в понедельник выполняется в среднем 1000 запросов в секунду, вы можете настроить отчет о работоспособности, который оповестит о том, что число запросов в секунду оказалось больше 1500 или меньше 500. Возможно, все работает нормально, но такие отклонения следует проверять дополнительно, чтобы гарантировать удобство работы для ваших клиентов. Для службы можно определить набор метрик, которые будут информировать о работоспособности, но не будут использоваться для балансировки ресурсов кластера. Для этого присвойте весу метрик нулевое значение. Мы рекомендуем изначально присваивать всем метрикам нулевой вес и не увеличивать его, пока вы не будете твердо уверены, как изменение веса повлияет на балансировку ресурсов кластера.

> [!TIP]
> Не используйте много метрик с ненулевым весом. Вам будет сложно понять, почему экземпляры службы перемещаются для балансировки. Нескольких метрик вполне достаточно.

Для метрик и отчетов о работоспособности подойдет любая информация, которая указывает на работоспособность и производительность приложения. Счетчик производительности ЦП может показать, как используется узел, но он не даст информации о работоспособности определенной службы, так как на одном узле может быть запущено несколько служб. Для этих целей лучше подойдут такие метрики, как количество запросов в секунду, количество обработанных элементов или задержка выполнения запроса.

## <a name="service-fabric-support-logs"></a>Журналы поддержки Service Fabric

Если вы будете обращаться в службу поддержки корпорации Майкрософт с вопросами по управлению кластером Azure Service Fabric, вам почти наверняка потребуются журналы поддержки. Если кластер размещен в Azure, эти журналы автоматически настраиваются в процессе создания кластера, а затем собираются. Эти журналы хранятся в выделенной учетной записи хранения, размещенной в группе ресурсов вашего кластера. У этой учетной записи хранения нет фиксированного имени, но ее можно опознать по наличию таблиц и контейнеров больших двоичных объектов, имена которых начинаются с префикса *fabric*. Сведения о настройке сбора журналов для автономного кластера см. в статьях [Создание изолированного кластера под управлением Windows Server](service-fabric-cluster-creation-for-windows-server.md) и [Параметры конфигурации для автономного кластера Windows](service-fabric-cluster-manifest.md). Для автономных экземпляров Service Fabric журналы следует передавать в локальный файловый ресурс. Эти журналы **необходимы** для технической поддержки, но они не предназначены для использования кем-либо, кроме группы поддержки клиентов Майкрософт.

## <a name="measuring-performance"></a>Измерение производительности

Измерение производительности кластера помогает понять, как обрабатывать нагрузку и принимать решения для масштабирования кластера. Дополнительные сведения о масштабировании кластера см. статьях [Масштабирование кластера Service Fabric с помощью правил автомасштабирования](service-fabric-cluster-scale-up-down.md) и [Добавление узлов в автономный кластер Service Fabric под управлением Windows Server или удаление узлов из него](service-fabric-cluster-windows-server-add-remove-nodes.md). Данные о производительности также полезны при сравнении с действиями, которые вы или ваши приложения и службы могли выполнить, для последующего анализа журналов. 

Дополнительные сведения о списке счетчиков производительности, данные которых необходимо собрать при использовании Service Fabric, см. в [этой статье](service-fabric-diagnostics-event-generation-perf.md).

Ниже приведены два простых способа настройки сбора данных производительности для кластера:

* **Использование агента.**  
Это предпочтительный способ сбора данных производительности на компьютере. У агентов обычно есть список возможных метрик производительности, которые можно собрать. Процесс их выбора или изменения достаточно прост. Дополнительные сведения о настройке агента OMS (агент наблюдения, который может собирать данные производительности виртуальных машин кластера и контейнеров развертывания) см. в статье [Анализ событий и визуализация с помощью OMS](service-fabric-diagnostics-event-analysis-oms.md) и [Подключение компьютеров Windows к службе Log Analytics в Azure](../log-analytics/log-analytics-windows-agent.md).

* **Настройка диагностики для записи данных счетчиков производительности в таблицу.**  
Для кластеров Azure это означает, что конфигурация системы диагностики Azure изменяется таким образом, чтобы выполнялся сбор соответствующих данных счетчиков производительности с виртуальных машин в кластере и статистики Docker при развертывании контейнеров. Дополнительные сведения о настройке счетчиков производительности в WAD и Service Fabric для настройки их сбора см. в [этой статье](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="next-steps"></a>Дополнительная информация

Перед отправкой на платформу анализа журналы и события должны быть агрегированы. Прочтите статьи о [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) и [WAD](service-fabric-diagnostics-event-aggregation-wad.md), чтобы лучше понять некоторые рекомендуемые параметры.
