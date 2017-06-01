---
title: "Управление журналами потоков для групп безопасности сети с помощью Наблюдателя за сетями | Документация Майкрософт"
description: "На этой странице объясняется, как управлять журналами потоков для групп безопасности сети с помощью Наблюдателя за сетями."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: bbea08798a601989d06774475cb25ee67e99add6
ms.contentlocale: ru-ru
ms.lasthandoff: 05/26/2017


---

# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Управление журналами потоков для групп безопасности сети на портале Azure

> [!div class="op_single_selector"]
> - [Портал Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Интерфейс командной строки 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Журналы потоков для групп безопасности сети — это компонент Наблюдателя за сетями, который позволяет просматривать сведения о входящем и исходящем IP-трафике через группу безопасности сети. Эти журналы потоков записываются в формате JSON. В них отображаются входящие и исходящие потоки по каждому правилу, сетевая карта, с которой связан поток, сведения о 5 кортежах потока (IP-адрес источника и места назначения, порт источника и места назначения, протокол), а также сведения о состоянии трафика (разрешен или запрещен).

## <a name="before-you-begin"></a>Перед началом работы

В этом сценарии предполагается, что вы создали Наблюдатель за сетями в соответствии с инструкциями в статье [Create an Azure Network Watcher instance](network-watcher-create.md) (Наблюдатель за сетями: создание экземпляра службы). Предполагается также, что у вас есть группа ресурсов с допустимой виртуальной машиной.

## <a name="register-insights-provider"></a>Регистрация поставщика Microsoft Insights

Для успешного ведения журналов потоков должен быть зарегистрирован поставщик **Microsoft.Insights**. Чтобы зарегистрировать поставщика, щелкните **Подписки** и выберите подписку, для которой требуется включить журналы потока. В колонке **Подписки** выберите **Поставщики ресурсов**. Перейдите в список поставщиков и убедитесь, что поставщик **microsoft.insights** зарегистрирован. Если нет, нажмите кнопку **Зарегистрировать**.

![Просмотр поставщиков][providers]

## <a name="enable-flow-logs"></a>Включение журналов потоков

Чтобы включить журналы потоков в группе безопасности сети, выполните приведенные ниже действия.

### <a name="step-1"></a>Шаг 1

Перейдите к экземпляру Наблюдателя за сетями и выберите **Журналы последовательностей**.

![Обзор журналов потоков][1]

### <a name="step-2"></a>Шаг 2

Выберите в списке необходимую группу безопасности сети.

![Обзор журналов потоков][2]

### <a name="step-3"></a>Шаг 3. 

В колонке **Параметры журналов последовательностей** установите для состояния значение **Вкл.** и настройте учетную запись хранения.  По завершении нажмите кнопку **ОК** и **Сохранить**.

![Обзор журналов потоков][3]

## <a name="download-flow-logs"></a>Скачивание журналов потоков

Журналы потоков хранятся в учетной записи хранения. Чтобы просмотреть журналы потоков, их необходимо скачать.

### <a name="step-1"></a>Шаг 1

Чтобы скачать журналы потоков, щелкните **Журналы последовательностей можно скачать в настроенных учетных записях хранения**.  После этого откроется представление учетной записи хранения, где можно скачать журнал.

![Параметры журналов потоков][4]

### <a name="step-2"></a>Шаг 2

Перейдите к правильной учетной записи хранения, а затем выберите **Контейнеры** > **insights-log-networksecuritygroupflowevent**.

![Параметры журналов потоков][5]

### <a name="step-3"></a>Шаг 3.

Перейдите к расположению журнала потоков, выберите необходимый журнал и щелкните **Скачать**.

![Параметры журналов потоков][6]

Сведения о структуре журнала см. в статье [Introduction to flow logging for Network Security Groups](network-watcher-nsg-flow-logging-overview.md) (Введение в журналы потоков для групп безопасности сети).

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [визуализировать сведения журналов потоков группы безопасности сети с помощью PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md).

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png
