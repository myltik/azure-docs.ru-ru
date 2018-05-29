---
title: Мониторинг кластера DC/OS Azure с помощью Dynatrace
description: Мониторинг кластера DC/OS Службы контейнеров Azure с помощью Dynatrace. Развертывание Dynatrace OneAgent с помощью панели мониторинга DC/OS.
services: container-service
author: MartinGoodwell
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 3d1bfc3bb61781d487c40831edd5da6fcb5a7df9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32162047"
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>Мониторинг кластера DC/OS в Службе контейнеров Azure с помощью Dynatrace SaaS/Managed

В этой статье мы покажем, как развернуть [Dynatrace](https://www.dynatrace.com/) OneAgent для отслеживания всех агентов узлов в кластере Службы контейнеров Azure. Для работы с этой конфигурацией вам понадобится учетная запись с Dynatrace SaaS/Managed. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/Managed
DynaTrace — облачное решение для мониторинга, предназначенное для высокодинамичных сред контейнеров и кластеров. Оно позволяет оптимизировать развертывание контейнеров и выделение памяти с помощью данных об использовании, получаемых в режиме реального времени. Это решение способно автоматически выявлять проблемы приложений и инфраструктуры, обеспечивая автоматизированное задание базовых показателей, обобщение проблем и определение первопричин.

На следующем рисунке показан пользовательский интерфейс Dynatrace.

![Пользовательский интерфейс Dynatrace](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>предварительным требованиям 
[Разверните](container-service-deployment.md) кластер, настроенный Службой контейнеров Azure, и [подключитесь](./../container-service-connect.md) к нему. Изучите [пользовательский интерфейс Marathon](container-service-mesos-marathon-ui.md). Перейдите на сайт [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) для настройки учетной записи Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Настройка развертывания Dynatrace с использованием Marathon
Ниже описано, как настраивать и развертывать в кластере приложения Dynatrace с помощью Marathon.

1. Откройте пользовательский интерфейс DC/OS по адресу [http://localhost:80/](http://localhost:80/). В пользовательском интерфейсе DC/OS перейдите на вкладку **Universe** (Вселенная) и найдите **Dynatrace**.

    ![Dynatrace на вкладке "Universe" (Вселенная) DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Чтобы завершить настройку, вам понадобится облачная учетная запись Dynatrace SaaS или бесплатная пробная учетная запись. После входа в панель мониторинга Dynatrace выберите **Deploy Dynatrace** (Развернуть Dynatrace).

    ![Настройка интеграции PaaS для Dynatrace](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. На странице выберите **Set up PaaS integration** (Настройка интеграции PaaS). 

    ![Маркер API Dynatrace](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Добавьте маркер API в конфигурацию Dynatrace OneAgent на вкладке "Universe" (Вселенная) DC/OS. 

    ![Конфигурация Dynatrace OneAgent на вкладке "Universe" (Вселенная) DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Настройте столько экземпляров, сколько планируется запускать узлов. Можно также настроить больше экземпляров, но DC/OS будет продолжать попытки найти новые экземпляры, пока это число не будет достигнуто. При необходимости вы можете указать такое значение, как 1000000. В этом случае при каждом добавлении нового узла в кластер Dynatrace будет автоматически развертывать на нем агент, однако при этом DC/OS будет непрерывно пытаться развертывать дополнительные экземпляры.

    ![Конфигурация Dynatrace на вкладке "Universe" (Вселенная) DC/OS. Экземпляры](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Дополнительная информация

После установки пакета вернитесь к панели мониторинга Dynatrace. Вы можете просмотреть различные метрики использования контейнеров в кластере. 