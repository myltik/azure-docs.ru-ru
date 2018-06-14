---
title: 'Наблюдатель за сетями Azure: обнаружение сетевого вторжения с помощью средств с открытым исходным кодом | Документация Майкрософт'
description: В этой статье описывается, как использовать службу наблюдения за сетями Azure и средства с открытым исходным кодом для обнаружения сетевого вторжения
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: aff1b5f9e8860d3b8dc09b37684bb8a4ac2bf134
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "23036739"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Обнаружение сетевого вторжения с помощью Наблюдателя за сетями Azure и средств с открытым исходным кодом

Запись пакетов — это ключевой компонент для реализации систем обнаружения сетевых вторжений и для мониторинга безопасности сети. Доступны разные средства с открытым исходным кодом для обнаружения сетевых вторжений, которые обрабатывают запись пакетов для поиска известных сигнатур потенциальных сетевых вторжений и вредоносных действий. Используя функцию записи пакетов службы наблюдения за сетями (Наблюдатель за сетями), вы можете анализировать сеть для поиска вредоносных вторжений и уязвимостей.

В числе средств с открытым исходным кодом можно назвать систему обнаружения сетевых вторжений Suricata, которая использует наборы правил для отслеживания сетевого трафика и создает оповещения при обнаружении подозрительных событий. Механизм Suricata является многопоточным, что позволяет еще быстрее и эффективнее выполнять анализ сетевого трафика. Дополнительные сведения о системе Suricata и ее возможностях см. на веб-сайте https://suricata-ids.org/.

## <a name="scenario"></a>Сценарий

В этой статье описывается настройка среды для обнаружения сетевых вторжений с помощью Наблюдателя за сетями, Suricata и Elastic Stack. Наблюдатель за сетями предоставляет возможность записи пакетов, которая используется для обнаружения сетевых вторжений. Suricata обрабатывает запись пакетов и формирует оповещения, если обнаруживает пакеты, соответствующие набору правил для известных угроз. Эти предупреждения сохраняются в файле журнала на локальном компьютере. Elastic Stack позволяет индексировать журналы, созданные Suricata, и использовать их для создания панели мониторинга Kibana, которая визуализирует информацию из журналов для быстрого анализа потенциальных уязвимостей сети.  

![Простой сценарий для веб-приложения][1]

Оба средства с открытым исходным кодом можно настроить на виртуальной машине Azure, что позволяет выполнять анализ непосредственно в привычной сетевой среде Azure.

## <a name="steps"></a>Действия

### <a name="install-suricata"></a>Установка Suricata

Другие методы установки описаны на странице http://suricata.readthedocs.io/en/latest/install.html.

1. В окне командной строки на виртуальной машине выполните следующие команды:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Чтобы проверить установку, выполните команду `suricata -h`, которая должна отобразить полный список команд.

### <a name="download-the-emerging-threats-ruleset"></a>Скачивание набора правил Emerging Threats

Пока у нас нет никаких правил, с которыми можно запустить Suricata. Вы можете создать собственные правила, если планируете обнаруживать в сети конкретные угрозы, либо применить наборы правил, разработанные несколькими поставщиками, например Emerging Threats, или правила Snort VRT. Для примера мы применим бесплатный набор правил Emerging Threats:

Скачайте набор правил и поместите их в этот каталог:

```
wget http://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Обработка записи пакетов с помощью Suricata

Чтобы поручить Suricata обработку записи пакетов, выполните следующую команду:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Чтобы проверить созданные оповещения, изучите файл fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Настройка Elastic Stack

Хотя журналы, созданные Suricata, содержат полезную информацию о ситуации в сети, эти файлы журналов не очень легко изучать. Подключив Suricata к Elastic Stack, мы сможем создать панель мониторинга Kibana, которая позволяет выполнять поиск, строить диаграммы и анализировать созданные журналы.

#### <a name="install-elasticsearch"></a>Установка Elasticsearch

1. Для Elastic Stack версии 5.0 и более поздних версий требуется Java 8. Выполните команду `java -version`, чтобы проверить установленную версию. Если компонент Java не установлен, обратитесь к документации на [веб-сайте компании Oracle](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html).
1. Скачайте правильный двоичный пакет для своей системы.

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Другие методы установки можно найти в [документации по установке Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html).

1. Убедитесь, что Elasticsearch выполняется, выполнив следующую команду.

    ```
    curl http://127.0.0.1:9200
    ```

    Вы должны увидеть ответ следующего вида.

    ```
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

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Теперь нужно настроить Logstash для чтения выходного файла eve.json. Создайте файл logstash.conf следующим образом:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Добавьте в этот файл следующее содержимое (укажите правильный путь к файлу eve.json):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Не забудьте предоставить правильные разрешения для файла eve.json, чтобы программа Logstash смогла выполнить обработку.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Чтобы запустить Logstash, выполните команду:

    ```
    sudo /etc/init.d/logstash start
    ```

Дополнительные инструкции по установке Logstash см. в [официальной документации](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-kibana"></a>Установка Kibana

1. Введите следующие команды для установки Kibana.

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Запустите Kibana, выполнив следующие команды.

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Чтобы открыть веб-интерфейс Kibana, перейдите к разделу `http://localhost:5601`.
1. В нашем примере для журналов Suricata используется шаблон индексирования logstash-*.

1. Чтобы просматривать панель мониторинга Kibana удаленно, создайте правило NSG для доступа к **порту 5601**.

### <a name="create-a-kibana-dashboard"></a>Создание панели мониторинга Kibana

Для этой статьи мы подготовили пример панели мониторинга для просмотра тенденций и подробных сведений об оповещениях.

1. Файл панели мониторинга вы можете скачать [здесь](https://aka.ms/networkwatchersuricatadashboard), файл визуализации — [здесь](https://aka.ms/networkwatchersuricatavisualization), а сохраненный файл поиска — [здесь](https://aka.ms/networkwatchersuricatasavedsearch).

1. На вкладке **Management** (Управление) в Kibana перейдите к элементу **Saved Objects** (Сохраненные объекты) и импортируйте все три файла. Затем откройте вкладку **Dashboard** (Панель мониторинга) и загрузите пример панели мониторинга.

Вы можете также создать собственные визуализации и панели мониторинга, адаптированные к интересующим вас метрикам. Дополнительные сведения о визуализациях Kibana можно получить в [официальной документации по Kibana](https://www.elastic.co/guide/en/kibana/current/visualize.html).

![Панель мониторинга Kibana][2]

### <a name="visualize-ids-alert-logs"></a>Визуализация журналов оповещений IDS

Пример панели мониторинга содержит несколько визуализаций для журналов оповещений Suricata:

1. Оповещения по GeoIP — эта карта распределения оповещений по странам, составленная по данным о географическом расположении (определяется по IP-адресу)

    ![Географическое распределение IP-адресов][3]

1. 10 основных оповещений — сводка из 10 наиболее часто создаваемых оповещений с описаниями. Щелкнув конкретное оповещение, можно отфильтровать информацию на панели мониторинга и просмотреть сведения, имеющие отношение к этому оповещению.

    ![рисунок 4][4]

1. Число оповещений — общее число оповещений, созданных по набору правил

    ![Рис. 5][5]

1. 20 основных исходных/целевых IP-адресов/портов — эти круговые диаграммы представляют информацию об IP-адресах и портах, для которых создано больше всего оповещений. Можно отфильтровать информацию по конкретным IP-адресам или портам, чтобы увидеть количество и типы оповещений по ним.

    ![Рис. 6][6]

1. Сводка оповещения — таблица с подробными сведениями по каждому конкретному оповещению. Можно настроить эту таблицу для отображения разных параметров по каждому оповещению.

    ![Рис. 7][7]

Дополнительную документацию по созданию пользовательских визуализаций и панелей мониторинга см. в [официальной документации по Kibana](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Заключение

Сочетая возможность записи пакетов, реализованную в Наблюдателе за сетями, и функции таких средств с открытым исходным кодом, как Suricata, вы можете определять сетевые вторжения самого разного толка. Такие панели мониторинга позволяют быстро выявлять тенденции и аномалии в сети, а также подробно изучать данные для анализа основных причин оповещений, включая активность злоумышленников или уязвимость портов. На основе полученных данных вы сможете принять взвешенные решения о методах реагирования и защиты сети от вредоносных попыток вторжения, а также создать новые правила для предотвращения будущих сетевых вторжений.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о функции записи пакетов на основе оповещений см. в статье [Use packet capture to do proactive network monitoring with Azure Functions](network-watcher-alert-triggered-packet-capture.md) (Использование записи пакетов для упреждающего мониторинга сети с помощью функций Azure).

Дополнительные сведения см. в статье [Визуализация журналов потоков для групп безопасности сети с помощью Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
