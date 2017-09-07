---
title: "Общие сведения о топологии Наблюдателя за сетями Azure | Документация Майкрософт"
description: "Эта страница содержит общие сведения о топологии Наблюдателя за сетями."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e753a435-38e0-482b-846b-121cb547555c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: fa79ab0ab80481c8969c6fa653e92b10985cab3b
ms.openlocfilehash: 42443f614b76b8180ac163b9889163021adbf048
ms.contentlocale: ru-ru
ms.lasthandoff: 03/31/2017

---

# <a name="introduction-to-topology-in-azure-network-watcher"></a>Общие сведения о топологии Наблюдателя за сетями Azure

Топология возвращает граф сетевых ресурсов в виртуальной сети. На графе показаны взаимодействия между ресурсами, позволяющие оценить сквозное сетевое подключение.

![Обзор топологии][1]

На портале топология возвращает объекты ресурсов для каждой виртуальной сети. Связи показаны в виде линий между ресурсами. Ресурсы находятся за пределами региона Наблюдателя за сетями, даже если это не отображается в группе ресурсов. Ресурсы, возвращенные на портале, представляют собой подмножество сетевых компонентов, показанных на графе. Полный список сетевых ресурсов можно просмотреть с помощью [PowerShell](network-watcher-topology-powershell.md) или [REST](network-watcher-topology-rest.md).

> [!NOTE]
> Экземпляр Наблюдателя за сетями требуется в каждом регионе, в котором необходимо запустить топологию.

После возвращения ресурсов между ними моделируется подключение по двум связям.

- **Вложение.** Например, виртуальная сеть содержит подсеть, которая, в свою очередь, содержит сетевую карту.
- **Связь.** Например, сетевая карта связана с виртуальной машиной.

### <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о получении представления топологии с помощью PowerShell см. в статье [Просмотр топологии Наблюдателя за сетями с помощью PowerShell](network-watcher-topology-powershell.md).

<!--Image references-->

[1]: ./media/network-watcher-topology-overview/topology.png

