
<properties
   pageTitle="Выполнение автоматических тестов производительности Elasticsearch | Microsoft Azure"
   description="Описание выполнения тестов производительности в существующей рабочей среде."
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
   
# Выполнение автоматических тестов производительности Elasticsearch

Этот материал входит в [цикл статей, посвященных вопросам Elasticsearch](guidance-elasticsearch.md).

В документах [Настройка производительности приема данных для Elasticsearch в Azure] и [Настройка объединения данных и производительности запросов для Elasticsearch в Azure] описаны несколько тестов производительности, выполненных в примере кластера Elasticsearch.

Для этих тестов был написан сценарий, позволяющий запускать их в автоматическом режиме. В этом документе содержатся сведения о том, как можно повторить тесты в собственной среде.

## Предварительные требования

Для выполнения автоматизированных тестов требуются перечисленные ниже элементы:

-  Кластер Elasticsearch.

- Подробные инструкции по настройке среды JMeter см. в документе [Создание среды тестирования производительности Elasticsearch в Azure].

- Следующее программное обеспечение устанавливается только на главной виртуальной машине JMeter.

    [Python 3.5.1](https://www.python.org/downloads/release/python-351/)


## Принцип действия тестов
Тесты выполняются с помощью JMeter. Главный сервер JMeter загружает план тестирования и передает его в набор подчиненных серверов JMeter, которые фактически выполняют тесты. Главный сервер JMeter координирует подчиненные JMeter серверы и собирает результаты.

Существуют следующие планы тестирования:

* [elasticsearchautotestplan3nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan3nodes.jmx). Этот план тестирования запускает тест приема на 3-узловом кластере.

* [elasticsearchautotestplan6nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodes.jmx). Этот план тестирования запускает тест приема на 6-узловом кластере.

* [elasticsearchautotestplan6qnodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6qnodes.jmx). Этот план тестирования запускает тест приема и тест запроса на 6-узловом кластере.

* [elasticsearchautotestplan6nodesqueryonly.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodesqueryonly.jmx). Этот план тестирования запускает только тест запроса на 6-узловом кластере.


Указанные планы тестирования можно использовать в качестве основы для собственных сценариев, если требуется больше или меньше узлов.

Для создания и отправки тестовых данных в планах тестирования применяется дискретизатор запросов JUnit. План тестирования JMeter создает и запускает этот дискретизатор, а также отслеживает данные производительности каждого узла Elasticsearch. Дополнительные сведения см. в приложениях к документам [Настройка производительности приема данных для Elasticsearch в Azure] и [Настройка объединения данных и производительности запросов для Elasticsearch в Azure].

## Создание и развертывание JAR-файлов и зависимостей JUnit
Перед выполнением тестов устойчивости следует загрузить, скомпилировать и развернуть тесты JUnit, расположенные в папке performance или junitcode. На эти тесты ссылается план тестирования JMeter. Дополнительные сведения см. в процедуре "Импорт существующего тестового проекта JUnit в Eclipse" в документе [Развертывание дискретизатора JUnit JMeter для тестирования производительности Elasticsearch].

Существует две версии тестов JUnit — [Elasticsearch1.73](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch1.73). Этот код можно использовать для выполнения тестов приема. Эти тесты используют Elasticsearch 1.73 —[Elasticsearch2](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch2). Этот код можно использовать для выполнения тестов запросов. Эти тесты используют Elasticsearch 2.1 и более поздние версии.

Скопируйте соответствующий JAR-файл вместе с остальными зависимостями на компьютеры JMeter. Этот процесс описан в статье [Развертывание дискретизатора JUnit для JMeter для тестирования производительности Elasticsearch][].

> **Важно!** После развертывания теста JUnit используйте JMeter для загрузки и настройки планов тестирования, которые ссылаются на этот тест JUnit, и убедитесь, что группа потока *BulkInsertLarge* ссылается на правильный JAR-файл, имя класса JUnit и метод тестирования:
> 
> ![](./media/guidance-elasticsearch/performance-tests-image1.png)
> 
> Перед выполнением тестов сохраните обновленные планы тестирования.

## Создание индексов теста
Каждый тест выполняет прием и (или) запросы к одному индексу, указанному при запуске теста. Для создания индекса необходимо использовать схемы, описанные в приложениях к документам [Настройка производительности приема данных для Elasticsearch в Azure] и [Настройка объединения данных и производительности запросов для Elasticsearch в Azure], и настроить их согласно сценарию тестирования (значения doc values включены или отключены, несколько реплик и т. д.). Обратите внимание, что в планах тестирования предполагается, что индекс состоит из одного типа с именем *ctip*.

## Настройка параметров сценария тестирования
Скопируйте следующие файлы параметров сценария тестирования на сервер JMeter:

* [run.properties](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/run.properties). Этот файл указывает количество используемых тестовых потоков JMeter, продолжительность теста (в секундах), IP-адрес узла (или балансировщика нагрузки) в кластере Elasticsearch и имя кластера:

  ```ini
  nthreads=3
  duration=300
  elasticip=<IP Address or DNS Name Here>
  clustername=<Cluster Name Here>
  ```
  
  Измените этот файл и укажите соответствующие значения для теста и кластера.

* [query-config-win.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-win.ini) и [query-config-nix.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-nix.ini). Эти файлы содержат одни и те же сведения, файл *win* отформатирован в соответствии с путями и именами файлов Windows, файл *nix* отформатирован в соответствии с путями и именами файлов Linux.

  ```ini
  [DEFAULT]
  debug=true #if true shows console logs.

  [RUN]
  pathreports=C:\\Users\\administrator1\\jmeter\\test-results\\ #path where tests results are saved.
  jmx=C:\\Users\\administrator1\\testplan.jmx #path to the JMeter test plan.
  machines=10.0.0.1,10.0.02,10.0.0.3 #IPs of the Elasticsearch data nodes separated by commas.
  reports=aggr,err,tps,waitio,cpu,network,disk,response,view #Name of the reports separated by commas.
  tests=idx1 #Elasticsearch index name to test.
  properties=run.properties #Name of the properties file.
  ```

  Измените этот файл, чтобы указать местоположения результатов тестов, имя запускаемого плана тестирования JMeter, IP-адреса узлов данных Elasticsearch отчеты, в которых будут находиться создаваемые необработанные данные производительности, и имя (или имена) тестируемого индекса. Если файл *run.properties* находится в другой папке или другом каталоге, укажите полный путь к этому файлу.

## Выполнение тестов

* Скопируйте файл [query-test.py](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-test.py) на компьютер сервера JMeter в папку с файлами *run.properties* и *query-config-win.ini* (*query-config-nix.ini*).

* Убедитесь, что *jmeter.bat* (Windows) или *jmeter.sh* (Linux) расположены в пути к исполняемому файлу в вашей среде.

* Из командной строки запустите сценарий *query-test.py* для выполнения тестов.

  ```cmd
  py query-test.py
  ```

* По завершении теста результаты сохраняются в виде набора CSV-файлов, указанных в файле *query-config-win.ini* (*query-config-nix.ini*). Для анализа и графического представления этих данных можно использовать приложение Excel.


[Настройка производительности приема данных для Elasticsearch в Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Настройка объединения данных и производительности запросов для Elasticsearch в Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Создание среды тестирования производительности Elasticsearch в Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Развертывание дискретизатора JUnit JMeter для тестирования производительности Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Развертывание дискретизатора JUnit для JMeter для тестирования производительности Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!----HONumber=AcomDC_0224_2016-->