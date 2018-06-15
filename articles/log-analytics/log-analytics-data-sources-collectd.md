---
title: Сбор данных CollectD в OMS Log Analytics | Документы Майкрософт
description: CollectD — управляющая программа Linux с открытым исходным кодом, которая периодически собирает данные приложений и системные данные.  В этой статье приведены сведения о сборе данных CollectD в OMS Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: magoedte
ms.openlocfilehash: a63b15ca5126b45451f0694c9ee75d7b67b1ceaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23020859"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Сбор данных CollectD с помощью агентов Linux в Log Analytics
[CollectD](https://collectd.org/) — управляющая программа Linux с открытым исходным кодом, которая периодически собирает метрики производительности приложений и системные данные. К примерам таких приложений относятся виртуальная машина Java (JVM), сервер MySQL и Nginx. В этой статье приводятся сведения о сборе данных производительности CollectD в Log Analytics.

Полный список доступных подключаемых модулей можно найти в [таблице подключаемых модулей](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Обзор CollectD](media/log-analytics-data-sources-collectd/overview.png)

В состав агента OMS для Linux включена следующая конфигурация CollectD, которая перенаправляет данные CollectD в агент OMS для Linux.

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Если используется CollectD версии 5.5 или более ранней версии, используйте следующую конфигурацию вместо указанной.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

В конфигурации CollectD для отправки данных производительности агенту OMS для Linux через порт 26000 по умолчанию используется подключаемый модуль `write_http`. 

> [!NOTE]
> При желании номер порта можно изменить.

Агент OMS для Linux также прослушивает порт 26000 для сбора метрик CollectD, а затем преобразует эти метрики в метрики схемы OMS. Ниже приведена конфигурация агента OMS для Linux `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Поддерживаемые версии
- Log Analytics сейчас поддерживает CollectD версии 4.8 и более поздней версии.
- Для сбора метрик CollectD необходим агент OMS для Linux версии 1.1.0-217 или более поздней версии.


## <a name="configuration"></a>Параметр Configuration
Ниже приведены основные шаги по настройке сбора данных CollectD в Log Analytics.

1. Настройте отправку данных CollectD в агент OMS для Linux с помощью подключаемого модуля write_http.  
2. Настройте агент OMS для Linux для прослушивания данных CollectD на соответствующем порту.
3. Перезапустите CollectD и агент OMS для Linux.

### <a name="configure-collectd-to-forward-data"></a>Настройка пересылки данных в CollectD 

1. Для пересылки данных CollectD агенту OMS для Linux необходимо добавить файл `oms.conf` в каталог конфигурационных файлов CollectD. Местоположение этого файла зависит от используемого дистрибутива Linux.

    Если конфигурационные файлы CollectD находятся в папке /etc/collectd.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Если конфигурационные файлы CollectD находятся в папке /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Если используется CollectD версии 5.5 или более ранней версии, потребуется изменить теги в `oms.conf`, как показано выше.
    >

2. Скопируйте файл collectd.conf в конфигурационный каталог omsagent в соответствующей рабочей области.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Перезапустите CollectD и агент OMS для Linux, выполнив следующие команды.

    sudo service collectd restart  sudo /opt/microsoft/omsagent/bin/service_control restart

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>Метрики CollectD для преобразования схемы Log Analytics
Для сохранения знакомой модели между метриками инфраструктуры, уже собранными агентом OMS для Linux, и новыми метриками, собранными CollectD, используется следующая схема сопоставления:

| Поле метрики CollectD | Поле Log Analytics |
|:--|:--|
| host | Компьютер |
| plugin | Нет |
| plugin_instance | Имя экземпляра<br>Если **plugin_instance** имеет значение *null*, то InstanceName="*_Total*" |
| Тип | ObjectName |
| type_instance | CounterName<br>Если **type_instance** имеет значение *null*, то CounterName=**blank** |
| dsnames[] | CounterName |
| dstypes | Нет |
| values[] | CounterValue |

## <a name="next-steps"></a>Дополнительная информация
* Узнайте больше об [операциях поиска по журналу](log-analytics-log-searches.md) , которые можно применять для анализа данных, собираемых из источников данных и решений. 
* Используйте [настраиваемые поля](log-analytics-custom-fields.md) для анализа данных из записей системного журнала в отдельных полях.

