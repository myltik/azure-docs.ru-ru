---
title: "Мониторинг кластера DC/OS Azure с помощью Datadog | Документация Майкрософт"
description: "Мониторинг кластера службы контейнеров Azure с помощью Datadog. Использование веб-интерфейса DC/OS для развертывания агентов Datadog в кластере."
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Контейнеры, DC/OS, Docker Swarm, Azure"
ms.assetid: ed00635d-4569-4f87-ab63-e307b2690b42
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 07/28/2016
ms.author: saudas
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 42e1743b67a46c9eac6cce44852ea3a48fd38e5a
ms.contentlocale: ru-ru
ms.lasthandoff: 07/25/2017

---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>Мониторинг кластера DC/OS в Службе контейнеров Azure с помощью Datadog
В этой статье описывается развертывание агентов Datadog на всех узлах агентов в кластере службы контейнеров Azure. Для работы с этой конфигурацией вам понадобится учетная запись с Datadog. 

## <a name="prerequisites"></a>Предварительные требования
[Разверните](container-service-deployment.md) и [подключите](../container-service-connect.md) кластер, настроенный службой контейнеров Azure. Изучите [пользовательский интерфейс Marathon](container-service-mesos-marathon-ui.md). Перейдите на страницу [http://datadoghq.com](http://datadoghq.com) для настройки учетной записи Datadog. 

## <a name="datadog"></a>Datadog
Datadog представляет собой службу мониторинга, которая собирает данные мониторинга из контейнеров в кластере службы контейнеров Azure. Datadog имеет панель мониторинга интеграции с Docker, в которой вы можете увидеть некоторые метрики своих контейнеров. Метрики контейнеров собраны в несколько групп: ЦП, память, сеть и ввод-вывод. Datadog разделяет метрики по контейнерам и образам. Ниже представлен пример пользовательского интерфейса для метрик использования ЦП.

![Пользовательский интерфейс Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Настройка развертывания Datadog с помощью Marathon
Ниже описано, как настраивать и развертывать в кластере приложения Datadog с помощью Marathon. 

Откройте пользовательский интерфейс DC/OS по адресу [http://localhost:80/](http://localhost:80/). В этом интерфейсе щелкните Universe (Среда) в левом нижнем углу, выполните поиск по запросу "Datadog" и щелкните "Установить".

![Пакет Datadog в среде DC/ОС](./media/container-service-monitoring/datadog1.png)

Чтобы завершить настройку, вам понадобится обычная или бесплатная пробная учетная запись Datadog. Войдите на веб-сайт Datadog с этой учетной записью, и на левой панели щелкните "Integrations" (Интеграции) > [APIs](https://app.datadoghq.com/account/settings#api) (Интерфейсы API). 

![Ключ API Datadog](./media/container-service-monitoring/datadog2.png)

Введите ключ API в конфигурацию Datadog в разделе Universe (Среда) DC/OS. 

![Datadog: конфигурация в разделе Universe (Среда) DC/OS](./media/container-service-monitoring/datadog3.png) 

В примере выше для количества экземпляров задано значение 10000000, чтобы при каждом добавлении узла в кластер служба Datadog автоматически выполняла развертывание агента в этом узле. Это промежуточное решение. После установки пакета вернитесь на веб-сайт Datadog и найдите раздел [Dashboards](https://app.datadoghq.com/dash/list) (Панели мониторинга). Здесь вы увидите пользовательские панели мониторинга и панели интеграции. [Панель мониторинга Docker](https://app.datadoghq.com/screen/integration/docker) содержит все метрики контейнера, необходимые для наблюдения за кластером. 


