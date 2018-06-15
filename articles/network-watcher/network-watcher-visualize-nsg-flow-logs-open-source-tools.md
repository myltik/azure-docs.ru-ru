---
title: Визуализация журнала потоков для групп безопасности сети Наблюдателя за сетями Azure с помощью инструментов с открытым кодом | Документация Майкрософт
description: На этой странице описывается, как использовать инструменты с открытым кодом для визуализации журналов потоков для групп безопасности сети (NSG).
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f7d51352aa8411e36f4224804c90c2554d4ef9e6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29394176"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Визуализация журнала потоков для групп безопасности сети Наблюдателя за сетями Azure с помощью инструментов с открытым кодом

Журналы потоков для групп безопасности сети содержат информацию о входящем и исходящем IP-трафике групп безопасности сети. Эти журналы потоков отображают сведения о входящем и исходящем потоках на основе правил, сетевой карте, к которой относится поток, 5 кортежах потока (исходные IP-адрес и порт, конечные IP-адрес и порт, тип протокола), а также информацию о том, был поток запрещен или разрешен.

Данные журналы потоков могут быть трудны для анализа вручную и получения информации. Однако существует несколько инструментов с открытым кодом, которые могут помочь визуализировать эти данные. В этой статье описывается решение для визуализации этих журналов с помощью Elastic Stack, что позволит быстро индексировать и визуализировать журналы потоков на панели мониторинга Kibana.

## <a name="scenario"></a>Сценарий

В этой статье мы настроим решение, которое позволит визуализировать журналы потоков для групп безопасности сети с помощью Elastic Stack.  Подключаемый модуль ввода Logstash будет получать журналы потоков непосредственно из большого двоичного объекта службы хранилища, настроенного для хранения журналов потоков. Затем с помощью Elastic Stack журналы потоков будут индексированы и использованы для создания панели мониторинга Kibana для отображения информации.

![сценарий][scenario]

## <a name="steps"></a>Действия

### <a name="enable-network-security-group-flow-logging"></a>Включение ведения журнала потоков для групп безопасности сети
В рамках данного сценария вам необходимо включить ведение журнала потоков по меньшей мере для одной группы безопасности сети в своей учетной записи. Инструкции по включению журналов потоков для групп безопасности сети представлены в статье [Общие сведения о ведении журнала потоков для групп безопасности сети](network-watcher-nsg-flow-logging-overview.md).


### <a name="set-up-the-elastic-stack"></a>Настройка Elastic Stack
Подключив журналы потоков NSG к Elastic Stack, мы сможем создать панель мониторинга Kibana, которая позволяет использовать журналы для поиска информации, создания диаграмм, анализа и изучения.

#### <a name="install-elasticsearch"></a>Установка Elasticsearch

1. Для Elastic Stack версии 5.0 и более поздних версий требуется Java 8. Выполните команду `java -version`, чтобы проверить установленную версию. Если компонент Java не установлен, обратитесь к документации на [веб-сайте компании Oracle](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html).
1. Скачайте правильный двоичный пакет для своей системы.

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Другие методы установки можно найти в [документации по установке Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html).

1. Убедитесь, что Elasticsearch выполняется, выполнив следующую команду.

    ```bash
    curl http://127.0.0.1:9200
    ```

    Вы должны увидеть ответ следующего вида.

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Дополнительные инструкции по установке Elasticsearch можно найти на [странице установки](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Установка Logstash

1. Введите следующие команды, чтобы установить Logstash.

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Далее необходимо настроить Logstash для получения и анализа журналов потоков. Создайте файл logstash.conf следующим образом.

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Добавьте в этот файл приведенное ниже содержимое.

  ```
input {
   azureblob
     {
         storage_account_name => "mystorageaccount"
         storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
         container => "insights-logs-networksecuritygroupflowevent"
         codec => "json"
         # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
         # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
         file_head_bytes => 12
         file_tail_bytes => 2
         # Enable / tweak these settings when event is too big for codec to handle.
         # break_json_down_policy => "with_head_tail"
         # break_json_batch_count => 2
     }
   }

   filter {
     split { field => "[records]" }
     split { field => "[records][properties][flows]"}
     split { field => "[records][properties][flows][flows]"}
     split { field => "[records][properties][flows][flows][flowTuples]"}

  mutate{
   split => { "[records][resourceId]" => "/"}
   add_field => {"Subscription" => "%{[records][resourceId][2]}"
                 "ResourceGroup" => "%{[records][resourceId][4]}"
                 "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
   convert => {"Subscription" => "string"}
   convert => {"ResourceGroup" => "string"}
   convert => {"NetworkSecurityGroup" => "string"}
   split => { "[records][properties][flows][flows][flowTuples]" => ","}
   add_field => {
               "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
               "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
               "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
               "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
               "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
               "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
               "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
               "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
                }
   convert => {"unixtimestamp" => "integer"}
   convert => {"srcPort" => "integer"}
   convert => {"destPort" => "integer"}        
  }

  date{
    match => ["unixtimestamp" , "UNIX"]
  }
 }
output {
  stdout { codec => rubydebug }
  elasticsearch {
    hosts => "localhost"
    index => "nsg-flow-logs"
  }
}  
  ```

Дополнительные инструкции по установке Logstash см. в [официальной документации](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Установка подключаемого модуля ввода Logstash для хранилища BLOB-объектов Azure

Этот подключаемый модуль Logstash позволит обращаться непосредственно к журналам потоков из заданной для них учетной записи хранения. Чтобы установить этот подключаемый модуль, из каталога установки Logstash по умолчанию (в этом случае — /usr/share/logstash/bin) выполните следующую команду.

```bash
logstash-plugin install logstash-input-azureblob
```

Чтобы запустить Logstash, выполните приведенную ниже команду.

```bash
sudo /etc/init.d/logstash start
```

Дополнительные сведения об этом подключаемом модуле см. в документации, доступной [здесь](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Установка Kibana

1. Введите следующие команды для установки Kibana.

  ```bash
  curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
  tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
  ```

1. Запустите Kibana, выполнив следующие команды.

  ```bash
  cd kibana-5.2.0-linux-x86_64/
  ./bin/kibana
  ```

1. Чтобы открыть веб-интерфейс Kibana, перейдите по адресу `http://localhost:5601`.
1. В нашем сценарии для журналов потоков используется шаблон индексирования nsg-flow-logs. Его можно изменить в разделе output файла logstash.conf.

1. Чтобы получить возможность просматривать панель мониторинга Kibana удаленно, создайте правило NSG для доступа к **порту 5601**.

### <a name="create-a-kibana-dashboard"></a>Создание панели мониторинга Kibana

Для этой статьи мы подготовили пример панели мониторинга для просмотра тенденций и подробных сведений об оповещениях.

![Рисунок 1][1]

1. Файл панели мониторинга вы можете скачать [отсюда](https://aka.ms/networkwatchernsgflowlogdashboard), файл визуализации — [отсюда](https://aka.ms/networkwatchernsgflowlogvisualizations), а сохраненный файл поиска — [отсюда](https://aka.ms/networkwatchernsgflowlogsearch).

1. На вкладке **Management** (Управление) в Kibana перейдите к элементу **Saved Objects** (Сохраненные объекты) и импортируйте все три файла. Затем откройте вкладку **Dashboard** (Панель мониторинга) и загрузите пример панели мониторинга.

Вы можете также создать собственные визуализации и панели мониторинга, адаптированные к интересующим вас метрикам. Дополнительные сведения о визуализациях Kibana можно получить в [официальной документации по Kibana](https://www.elastic.co/guide/en/kibana/current/visualize.html).

### <a name="visualize-nsg-flow-logs"></a>Визуализация журналов потоков NSG

Пример панели мониторинга содержит несколько визуализаций для журналов потоков.

1. "Flows by Decision Over Time" (Потоки по решению с течением времени) и "Flows by Direction Over Time" (Потоки по направлению с течением времени) — диаграммы распределения во времени, показывающие число потоков за указанный период времени. Для этих визуализаций можно изменить единицу измерения времени и диапазон. Диаграмма "Flows by Decision Over Time" (Потоки по решению с течением времени) показывает соотношение принятых решений разрешить или запретить поток, а диаграмма "Flows by Direction Over Time" (Потоки по направлению с течением времени) — соотношение входящего и исходящего трафика. С помощью этих визуальных элементов можно проверять тенденции трафика с течением времени и искать какие-либо пики или нестандартные шаблоны.

  ![Рисунок 2][2]

1. "Flows by Destination" (Потоки по назначению) и "Flows by Source Port" (Потоки по сходному порту) — круговые диаграммы, показывающие распределение потоков по соответствующим портам. В этом представлении можно увидеть наиболее часто используемые порты. Если щелкнуть конкретный порт на круговой диаграмме, то на остальной части панели мониторинга отобразятся только потоки этого порта.

  ![Рисунок 3][3]

1. "Number of Flows" (Число потоков) и "Earliest Log Time" (Время записи самого раннего журнала) — метрики, показывающие количество записанных потоков и дату записи самого раннего журнала.

  ![Рисунок 4][4]

1. "Flows by NSG and Rule" (Потоки по NSG и правилу) — гистограмма, показывающая распределение потоков в каждой группе безопасности сети, а также распределение правил в каждой из этих групп. Здесь можно увидеть, какие NSG и правила создают большую часть трафика.

  ![Рисунок 5][5]

1. "Top 10 Source IPs" (10 наиболее используемых исходных IP-адресов) и "Top 10 Destination IPs" (10 наиболее используемых конечных IP-адресов) — гистограммы, показывающие 10 наиболее используемых исходных и конечных IP-адресов. Эти гистограммы можно настроить для отображения большего ли меньшего числа наиболее используемых IP-адресов. Здесь можно увидеть наиболее часто встречающиеся IP-адреса, а также решение по трафику (разрешить его или запретить), принятое для каждого IP-адреса.

  ![Рисунок 6][6]

1. "Flow Tuples" (Кортежи потоков) — в этой таблице показаны сведения, содержащиеся в каждом кортеже потока, а также соответствующие NSG и правило.

  ![Рисунок 7][7]

С помощью строки запроса в верхней части панели мониторинга можно отфильтровать ее содержимое по любому параметру потоков, включая идентификатор подписки, группы ресурсов, правило или любую другую интересующую вас переменную. Дополнительные сведения о запросах и фильтрах Kibana см. в [официальной документации](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html).

## <a name="conclusion"></a>Заключение

Сочетая журналы потоков для групп безопасности сети с Elastic Stack, мы получили эффективный и настраиваемый способ визуализации сетевого трафика. Эти панели мониторинга позволяют быстро получить и предоставить информацию о сетевом трафике, а также отфильтровать ее и проверить на наличие каких-либо потенциальных нарушений. С помощью Kibana эти панели мониторинга можно адаптировать, чтобы создать определенные визуализации, соответствующие требованиям безопасности, аудита и соответствия.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в статье [Визуализация журналов потоков для групп безопасности сети с помощью Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).


<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
