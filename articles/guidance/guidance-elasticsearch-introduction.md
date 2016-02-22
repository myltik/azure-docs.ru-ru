
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
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# Руководство по работе с ElasticSearch в Azure

Elasticsearch — это высокомасштабируемая поисковая система и база данных с открытым кодом. Ее можно использовать в сценариях, требующих быстрого анализа и обнаружения информации, хранящейся в больших данных. В этом руководстве рассматриваются некоторые ключевые аспекты, которые следует учитывать при проектировании кластеров Elasticsearch:

- В **[общем руководстве][]** приводится краткое введение в общую структуру Elasticsearch, а также объясняется, как можно применять кластер Elasticsearch с помощью Azure.
- В **[руководстве по приему данных][]** описано, на какие параметры развертывания и конфигурации необходимо обратить внимание при создании кластера Elasticsearch с высокой скоростью приема данных.
- В **[руководстве по тестированию производительности][]** описано, как настроить среду для тестирования производительности кластера Elasticsearch.
- В **[руководстве по JMeter][]** описывается, как создать и использовать дискретизатор JUnit для создания и отправки данных в кластер Elasticsearch.
- В статье **[Рекомендации по JMeter][]** описан накопленный опыт составления и выполнения планов для тестирования запросов и приема данных. 

  > [AZURE.NOTE] В этом руководстве предполагается, что у вас уже есть основные навыки работы с Elasticsearch. Дополнительные сведения см. на [веб-сайте Elasticsearch](https://www.elastic.co/products/elasticsearch).

[общем руководстве]: guidance-elasticsearch.md
[руководстве по приему данных]: guidance-elasticsearch-data-ingestion.md
[руководстве по тестированию производительности]: guidance-elasticsearch-performance-testing-environment.md
[руководстве по JMeter]: guidance-elasticsearch-implementing-jmeter.md
[Рекомендации по JMeter]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0211_2016-->