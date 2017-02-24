---
title: "Мониторинг кластера DC/OS Azure с помощью стека ELK | Документация Майкрософт"
description: "Мониторинг кластера DC/OS в кластере службы контейнеров Azure с помощью ELK (Elasticsearch, Logstash и Kibana)."
services: container-service
documentationcenter: 
author: sauryadas
manager: dan.lepow
editor: 
tags: acs, azure-container-service
keywords: "Контейнеры, DC/OS, Azure, мониторинг, elk"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: saudas
translationtype: Human Translation
ms.sourcegitcommit: 9433eb9f734dbe1b815074f86b9a691b4d16b5c8
ms.openlocfilehash: c372c4a04de61f1eace81418ec6dea054007ec1f


---
# <a name="monitor-an-azure-container-service-cluster-with-elk"></a>Мониторинг кластера службы контейнеров Azure с помощью ELK
В этой статье показано, как развертывать стек ELK (Elasticsearch, Logstash, Kibana) в кластере DC/OS в службе контейнеров Azure. 

## <a name="prerequisites"></a>Предварительные требования
[Развертывание](container-service-deployment.md) и [подключение](container-service-connect.md) кластера DC/OS, настроенного службой контейнеров Azure. Ознакомьтесь с панелью мониторинга DC/OS и службами Marathon [здесь](container-service-mesos-marathon-ui.md). Кроме того, установите [Балансировщик нагрузки Marathon](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
Стек ELK представляет собой сочетание Elasticsearch, Logstash и Kibana, которые формируют комплекс средств, используемых для отслеживания и анализа журналов в кластере.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Настройка стека ELK в кластере DC/OS
Перейдите к пользовательскому интерфейсу DC/OS по адресу [http://localhost:80/](http://localhost:80/). Затем в пользовательском интерфейсе DC/OS перейдите в раздел **Universe**. Найдите и установите Elasticsearch, Logstash и Kibana из DC/OS Universe именно в этом порядке. Дополнительные сведения о конфигурации см. разделе **Advanced Installation** (Дополнительные параметры установки).

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

После того как контейнеры ELK заработают, необходимо разрешить в Kibana доступ с помощью Marathon-LB. Перейдите в раздел **Службы** > **kibana** и нажмите кнопку **Изменить**, как показано ниже.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Переключитесь в **режим JSON** и перейдите ниже к разделу меток.
Необходимо добавить запись `"HAPROXY_GROUP": "external"`, как показано ниже.
После нажатия кнопки **Deploy changes** (Развернуть изменения) контейнер будет перезагружен.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Чтобы проверить, что Kibana зарегистрирован как служба в панели мониторинга HAPROXY, в кластере агента необходимо открыть порт 9090, поскольку в HAPROXY используется порт 9090.
По умолчанию в кластере агента DC/OS открыть порты 80, 8080 и 443.
Инструкции по открытию портов и предоставлению открытого доступа см. [здесь](container-service-enable-public-access.md).

Для доступа к панели мониторинга HAPROXY откройте интерфейс администрирования Marathon-LB по такому адресу: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
После перехода по этому URL-адресу вы увидите, как показано ниже, панель мониторинга HAPROXY и запись службы для Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Чтобы получить доступ к панели мониторинга Kibana, которая развернута на порту 5601, необходимо открыть порт 5601. Инструкции см. [здесь](container-service-enable-public-access.md). Затем откройте панель мониторинга Kibana по этому адресу: `http://localhost:5601`.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Elasticsearch, Logstash и Kibana см. в [документации по стеку Elastic Stack](https://www.elastic.co/guide/index.html).
 




<!--HONumber=Feb17_HO1-->


