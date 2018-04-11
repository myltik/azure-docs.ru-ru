---
title: Управление журналами потоков для групп безопасности сети с помощью наблюдателя за сетями | Документы Майкрософт
description: На этой странице объясняется, как управлять журналами потоков для групп безопасности сети с помощью Наблюдателя за сетями.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: cb41781c5ac8fb759cecea01402c08dd716bf7d7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Управление журналами потоков для групп безопасности сети на портале Azure

> [!div class="op_single_selector"]
> - [портал Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Интерфейс командной строки 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Журналы потоков для групп безопасности сети — это компонент наблюдателя за сетями, который позволяет просматривать сведения о входящем и исходящем IP-трафике через группу безопасности сети. Эти журналы потоков сохраняются в формате JSON и содержат следующие важные сведения: 

- Входящие и исходящие потоки для каждого правила.
- Сетевой адаптер, к которому относится данный поток.
- 5 видов информации о потоке (исходный и конечный IP-адрес, исходный и конечный порт, протокол).
- Информация о том, разрешен или запрещен трафик.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить действия, описанные в этой статье, необходимо иметь следующие ресурсы:

- Наблюдатель за сетями. Сведения о создании Наблюдателя за сетями см. в статье [Создание экземпляра Наблюдателя за сетями Azure](network-watcher-create.md).
- Группа ресурсов с допустимой виртуальной машиной. Если у вас еще нет виртуальной машины, ознакомьтесь со статьями по созданию виртуальной машины [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) или [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="register-insights-provider"></a>Регистрация поставщика Microsoft Insights

Для успешного ведения журналов потоков должен быть зарегистрирован поставщик **Microsoft.Insights**. Чтобы зарегистрировать поставщик, выполните следующее: 

1. Перейдите в раздел **Подписки** и выберите подписку, для которой требуется включить журналы потока. 
2. В колонке **Подписки** выберите **Поставщики ресурсов**. 
3. Просмотрите список поставщиков и убедитесь, что поставщик **microsoft.insights** зарегистрирован. Если он не зарегистрирован, то выберите **Зарегистрировать**.

![Просмотр поставщиков][providers]

## <a name="enable-flow-logs"></a>Включение журналов потоков

Чтобы включить журналы потоков в группе безопасности сети, выполните приведенные ниже действия.

### <a name="step-1"></a>Шаг 1

Перейдите к экземпляру наблюдателя за сетями и выберите **Журналы потоков для группы безопасности сети (NSG)**.

![Обзор журналов потоков][1]

### <a name="step-2"></a>Шаг 2

Выберите в списке нужную группу безопасности сети.

![Обзор журналов потоков][2]

### <a name="step-3"></a>Шаг 3. 

В колонке **Параметры журналов потоков**  установите для состояния значение **Вкл.** и настройте учетную запись хранения. Выберите имеющуюся учетную запись хранения (с установленным параметром **Все сети** (по умолчанию) в разделе **Брандмауэры и виртуальные сети**) в разделе **Параметры** для учетной записи хранения. Закончив, нажмите кнопку **ОК**, а затем — **Сохранить**.

![Обзор журналов потоков][3]

## <a name="download-flow-logs"></a>Скачивание журналов потоков

Журналы потоков хранятся в учетной записи хранения. Для просмотра журналов потоков их необходимо скачать.

### <a name="step-1"></a>Шаг 1

Чтобы скачать журналы потоков, выберите **Журналы потоков можно скачать в настроенных учетных записях хранения**. После этого в представлении учетной записи хранения выберите, какие журналы следует скачать.

![Параметры журналов потоков][4]

### <a name="step-2"></a>Шаг 2

Перейдите к нужной учетной записи хранения. Выберите **Контейнеры** > **insights-log-networksecuritygroupflowevent**.

![Параметры журналов потоков][5]

### <a name="step-3"></a>Шаг 3.

Перейдите к расположению журнала потока, выберите его, затем выберите **Скачать**.

![Параметры журналов потоков][6]

Сведения о структуре журнала см. в разделе [Обзор журнала потоков для группы безопасности сети](network-watcher-nsg-flow-logging-overview.md).

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как [визуализировать журналы потоков для группы безопасности сети с помощью PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png
