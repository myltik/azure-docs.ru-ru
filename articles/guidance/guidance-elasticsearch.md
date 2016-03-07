
<properties
   pageTitle="Руководство по работе с ElasticSearch в Azure | Microsoft Azure"
   description="Руководство по работе с ElasticSearch в Azure."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>

# Руководство по работе с ElasticSearch в Azure

Elasticsearch — это высокомасштабируемая поисковая система и база данных с открытым кодом. Ее можно использовать в сценариях, требующих быстрого анализа и обнаружения информации, хранящейся в больших данных. В этом руководстве рассматриваются некоторые ключевые аспекты, которые следует учитывать при проектировании кластеров Elasticsearch:

- В статье **[Работа Elasticsearch в Azure][]** приводится краткое введение в общую структуру Elasticsearch, а также объясняется, как можно применять кластер Elasticsearch с помощью Azure.
- В статье **[Настройка производительности приема данных для Elasticsearch в Azure][]** описано, на какие параметры развертывания и конфигурации необходимо обратить внимание при создании кластера Elasticsearch с высокой скоростью приема данных.
- В статье **[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure][]** описаны рекомендации, которые следует учитывать при выборе метода оптимизации производительности поисковых операций и запросов в системе.
- В статье **[Настройка устойчивости и восстановления Elasticsearch в Azure][]** описаны параметры устойчивости и восстановления Elasticsearch, доступные при размещении в Azure.
- В статье **[Создание среды тестирования производительности Elasticsearch в Azure][]** описано, как настроить среду для тестирования производительности кластера Elasticsearch.
- В статье **[Реализация плана тестирования JMeter для Elasticsearch][]** описывается, как создать и использовать дискретизатор JUnit для создания и передачи данных в кластер Elasticsearch.
- В статье **[Развертывание дискретизатора JUnit для JMeter при тестировании производительности Elasticsearch][]** описан накопленный опыт составления и выполнения планов тестирования запросов и приема данных. 
- В статье **[Выполнение автоматических тестов устойчивости Elasticsearch][]** описывается выполнение тестов устойчивости, используемых в статье выше.
- В статье **[Выполнение автоматических тестов производительности Elasticsearch][]** описывается выполнение тестов производительности, используемых в статье выше.

> [AZURE.NOTE] В этом руководстве предполагается, что у вас уже есть основные навыки работы с Elasticsearch. Дополнительные сведения см. на [веб-сайте Elasticsearch](https://www.elastic.co/products/elasticsearch).

[Работа Elasticsearch в Azure]: guidance-elasticsearch-running-on-azure.md
[Настройка производительности приема данных для Elasticsearch в Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Создание среды тестирования производительности Elasticsearch в Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Реализация плана тестирования JMeter для Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Развертывание дискретизатора JUnit для JMeter при тестировании производительности Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Tuning Data Aggregation and Query Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Настройка устойчивости и восстановления Elasticsearch в Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Выполнение автоматических тестов устойчивости Elasticsearch]: guidance-elasticsearch-running-automated-resilience-tests.md
[Выполнение автоматических тестов производительности Elasticsearch]: guidance-elasticsearch-running-automated-performance-tests.md

<!---HONumber=AcomDC_0224_2016-->