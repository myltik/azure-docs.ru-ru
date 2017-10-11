---
title: "Мониторинг кластера DC/OS Azure с помощью Operations Management | Документация Майкрософт"
description: "Мониторинг кластера DC/OS в Службе контейнеров Azure с помощью Microsoft Operations Management Suite."
services: container-service
documentationcenter: 
author: keikhara
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 11/17/2016
ms.author: keikhara
ms.custom: mvc
ms.openlocfilehash: 9b8f96b34b53982c469273a3df9751ceb7930d60
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2017
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-operations-management-suite"></a>Мониторинг кластера DC/OS в Службе контейнеров Azure с помощью Operations Management Suite

Microsoft Operations Management Suite (OMS) — это облачное решение Майкрософт для управления ИТ-средой, которое помогает управлять локальной и облачной инфраструктурой и защищать ее. Контейнер OMS — это решение в OMS Log Analytics, которое помогает просматривать сведения, касающиеся инвентаризации и производительности контейнеров, а также соответствующие журналы в одном расположении. Оно позволяет выполнять аудит и устранять неполадки контейнеров, просматривая журналы в централизованном расположении, а также находить контейнеры с высоким уровнем потребления ресурсов на узле.

![](media/container-service-monitoring-oms/image1.png)

Дополнительные сведения об этом решении см. в статье [Решение "Контейнеры" (предварительная версия) в Log Analytics](../../log-analytics/log-analytics-containers.md).

## <a name="setting-up-oms-from-the-dcos-universe"></a>Настройка OMS в среде DC/ОS


В этой статье предполагается, что вы настроили DC/OS и развернули простые приложения веб-контейнера в кластере.

### <a name="pre-requisite"></a>Предварительные требования
- [Подписка Microsoft Azure](https://azure.microsoft.com/free/) — ее можно получить бесплатно.  
- Настройка рабочей области Microsoft OMS — см. раздел "Шаг 3" ниже.
- Установленный [интерфейс командной строки DC/OS](https://dcos.io/docs/1.8/usage/cli/install/).

1. На панели мониторинга DC/OS щелкните "Вселенная" и выполните поиск по запросу "OMS", как показано ниже.

![](media/container-service-monitoring-oms/image2.png)

2. Щелкните **Install**(Установить). Отобразится всплывающее окно со сведениями о версии OMS и кнопкой **Установить пакет** или **Advanced Installation** (Расширенная установка). Нажав кнопку **Advanced Installation** (Расширенная установка), вы перейдете на страницу **OMS specific configuration properties** (Свойства конфигурации OMS).

![](media/container-service-monitoring-oms/image3.png)

![](media/container-service-monitoring-oms/image4.png)

3. Здесь вам будет предложено ввести `wsid` (идентификатор рабочей области OMS) и `wskey` (первичный ключ OMS для идентификатора рабочей области). Чтобы получить `wsid` и `wskey`, необходимо создать учетную запись OMS, перейдя по адресу <https://mms.microsoft.com>. Следуйте инструкциям, чтобы создать учетную запись. После создания учетной записи необходимо получить `wsid` и `wskey`, щелкнув **Параметры**, **Подключенные источники**, а затем — **Серверы с Linux**, как показано ниже.

 ![](media/container-service-monitoring-oms/image5.png)

4. Выберите необходимое количество экземпляров OMS и нажмите кнопку "Просмотреть и установить". Как правило, требуется количество экземпляров OMS, равное количеству виртуальных машин в кластере агента. Агент OMS для Linux устанавливается в качестве отдельных контейнеров на каждой виртуальной машине, о которой необходимо собирать сведения для мониторинга и ведения журнала.

## <a name="setting-up-a-simple-oms-dashboard"></a>Настройка простой панели мониторинга OMS

Сразу после установки агента OMS для Linux на виртуальных машинах необходимо настроить панель мониторинга OMS. Это можно сделать двумя способами: на портале OMS или портале Azure.

### <a name="oms-portal"></a>Портал OMS 

Войдите на портал OMS (<https://mms.microsoft.com>) и выберите **Коллекция решений**.

![](media/container-service-monitoring-oms/image6.png)

Войдя в **коллекцию решений**, выберите **Контейнеры**.

![](media/container-service-monitoring-oms/image7.png)

После выбора решения контейнера на странице обзорной панели мониторинга OMS отобразится плитка. Как только принятые данные контейнера будут проиндексированы, вы увидите плитку, заполненную сведениями на плитках представления решения.

![](media/container-service-monitoring-oms/image8.png)

### <a name="azure-portal"></a>Портал Azure 

Войдите на портал Azure по адресу <https://portal.microsoft.com/>. Выберите **Marketplace**, **Мониторинг и управление** и **Показать все**. Затем в поле поиска введите `containers`. В результатах отобразится "контейнеры". Выберите **Контейнеры** и нажмите кнопку **Создать**.

![](media/container-service-monitoring-oms/image9.png)

После нажатия кнопки **Создать** вам будет предложено выбрать рабочую область. Выберите свою рабочую область, а если у вас ее нет, создайте ее.

![](media/container-service-monitoring-oms/image10.PNG)

Выбрав рабочую область, щелкните **Создать**.

![](media/container-service-monitoring-oms/image11.png)

Дополнительные сведения о решении контейнера OMS см. в статье [Решение "Контейнеры" (предварительная версия) в Log Analytics](../../log-analytics/log-analytics-containers.md).

### <a name="how-to-scale-oms-agent-with-acs-dcos"></a>Масштабирование агента OMS с помощью среды DC/OS службы ACS 

Если установлен агент OMS с недостаточным количеством фактических узлов или требуется выполнить масштабирование VMSS путем добавления дополнительных виртуальных машин, можно масштабировать службу `msoms`.

Вы можете перейти в Marathon или на вкладку пользовательских служб DC/OS и масштабировать количество узлов.

![](media/container-service-monitoring-oms/image12.PNG)

Другие узлы, на которых еще не установлен агент OMS, также будут развернуты.

## <a name="uninstall-ms-oms"></a>Удаление MS OMS

Чтобы удалить MS OMS, введите следующую команду:

```bash
$ dcos package uninstall msoms
```

## <a name="let-us-know"></a>Сообщите нам!
Что работает? Чего не хватает? Что еще необходимо улучшить, чтобы этот инструмент был полезным для вас? Сообщите нам по адресу <a href="mailto:OMSContainers@microsoft.com">OMSContainers</a>.

## <a name="next-steps"></a>Дальнейшие действия

 Теперь, когда вы настроили OMS для мониторинга контейнеров, [просмотрите свою панель мониторинга контейнера](../../log-analytics/log-analytics-containers.md).
