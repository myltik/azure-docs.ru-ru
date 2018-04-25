---
title: Поиск следующего прыжка с помощью возможности определения следующего прыжка Наблюдателя за сетями Azure (портал Azure) | Документация Майкрософт
description: В этой статье описано, как определить тип следующего прыжка и IP-адрес, используя возможность определения следующего прыжка на портале Azure.
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: ''
ms.assetid: 7b459dcf-4077-424e-a774-f7bfa34c5975
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 445ec8c7eeb8dd715d3778b44372d16666da7fb8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2018
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-the-portal"></a>Определите тип следующего прыжка, используя возможность определения следующего прыжка Наблюдателя за сетями на портале Azure.

> [!div class="op_single_selector"]
> - [портал Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [Интерфейс командной строки 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Azure REST API](network-watcher-check-next-hop-rest.md)

Определение следующего прыжка — это возможность Наблюдателя за сетями, позволяющая определить тип следующего прыжка и IP-адрес на основе указанной виртуальной машины. Эта возможность используется, чтобы определить путь передачи исходящего трафика виртуальной машины (шлюз, Интернет или виртуальные сети) к месту назначения.

## <a name="before-you-begin"></a>Перед началом работы

В этом сценарии предполагается, что вы создали Наблюдатель за сетями в соответствии с инструкциями в статье [Create a Network Watcher](network-watcher-create.md) (Создание Наблюдателя за сетями). Предполагается также, что у вас имеется группа ресурсов с допустимой виртуальной машиной.

## <a name="scenario"></a>Сценарий

В сценарии, описанном в этой статье, используется возможность определения следующего прыжка, которая позволяет определить тип следующего перехода и IP-адрес ресурса. Дополнительные сведения об определении следующего прыжка см. в [этой статье](network-watcher-next-hop-overview.md).

Вам предстоит:

* получить следующий прыжок виртуальной машины.

## <a name="get-next-hop"></a>Получение следующего прыжка

### <a name="step-1"></a>Шаг 1

На портале Azure перейдите к своему ресурсу Наблюдателя за сетями.

### <a name="step-2"></a>Шаг 2

В области навигации щелкните **Следующий прыжок**, выберите интерфейс виртуальной машины или сетевой интерфейс, введите IP-адрес источника и места назначения, а затем нажмите кнопку **Следующий прыжок**.

> [!NOTE]
> Для получения следующего прыжка необходимо, чтобы ресурс виртуальной машины был выделен.

![Обзор получения следующего прыжка][1]

### <a name="step-3"></a>Шаг 3.

По завершении задания выводятся результаты. Здесь вы можете просмотреть IP-адрес устройства и тип следующего прыжка. Ниже приведен список доступных значений, возвращенных на портал.

**Типы следующего прыжка**

* Интернет
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

Если для маршрутизации трафика использовался определяемый пользователем маршрут, в результатах также отображаются сведения о нем.

![Получение результатов следующего прыжка][2]

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как просмотреть параметры группы безопасности сети программным образом, в статье [NSG Auditing with Network Watcher](network-watcher-nsg-auditing-powershell.md) (Выполнение аудита групп безопасности сети с помощью Наблюдателя за сетями).

[1]: ./media/network-watcher-check-next-hop-portal/figure1.png
[2]: ./media/network-watcher-check-next-hop-portal/figure2.png














