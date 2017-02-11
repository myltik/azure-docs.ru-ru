---
title: "Использование SAP на виртуальных машинах Windows | Документация Майкрософт"
description: "Узнайте об использовании SAP на виртуальных машинах Windows в Microsoft Azure."
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: timlt
editor: 
tags: azure-service-management
keywords: 
ms.assetid: 1b455be4-c02f-43e3-8d39-c2d5f216e646
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: campaign-page
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 9d98b1c9978132870c3bbf7b134cb44389f59391


---
# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>Использование SAP на виртуальных машинах Windows в Azure
Облачные вычисления — это широко используемый термин, который приобретает все большее значение в отрасли ИТ, начиная от малых компаний и заканчивая крупными и транснациональными корпорациями. Microsoft Azure — это созданная корпорацией Майкрософт платформа облачных служб, предоставляющая широкий спектр новых возможностей. Теперь клиенты могут быстро подготавливать и отзывать приложения в формате облачных служб без технических или бюджетных ограничений. Вместо того, чтобы тратить время и средства на создание и поддержку аппаратной инфраструктуры, компании могут сконцентрироваться на приложениях, бизнес-процессах, а также связанных преимуществах для клиентов и пользователей.

Корпорация Майкрософт предоставляет виртуальные машины Microsoft Azure как комплексную платформу "инфраструктура как услуга" (IaaS). Виртуальные машины Azure (IaaS) поддерживают приложения на основе SAP NetWeaver. Приведенная ниже техническая документация описывает планирование и реализацию приложений на основе SAP NetWeaver в виртуальных машинах Windows в Azure. Приложения на основе SAP NetWeaver можно также реализовать в [виртуальных машинах Linux](virtual-machines-linux-classic-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>SAP NetWeaver в Azure — высокая доступность
Заголовок: «SAP NetWeaver в Azure — кластеризация в Azure экземпляров SAP ASCS/SCS с помощью отказоустойчивых кластеров Windows Server с использованием SIOS DataKeeper»

Сводка: в этом документе описывается использование SIOS DataKeeper для настройки в Azure конфигурации SAP ASCS/SCS с высокой доступностью. SAP защищает компоненты, являющиеся единой точкой отказа, например SAP ASCS/SCS или службы Enqueue Replication Services, с помощью конфигураций отказоустойчивых кластеров Windows Server, для которых используются общие диски. Эти компоненты SAP являются критическими для функционирования всей системы SAP. Поэтому обеспечение высокого уровня доступности является залогом работоспособности этих компонентов при сбое сервера или виртуальной машины. Конфигурации кластеров Windows Server выполняют эту задачу для аппаратной среды и среды Hyper-V. По состоянию на август 2015 г. среда Azure не может предоставлять общие диски, необходимые для создания высокодоступных конфигураций на основе Windows, которые требуются для работы этих важных компонентов SAP. Однако с помощью продукта DataKeeper компании SIOS вы можете создавать на платформе Azure IaaS конфигурации отказоустойчивых кластеров Windows Server, которые нужны для работы SAP ASCS/SCS. В этом документе описан пошаговый процесс установки в Azure конфигурации отказоустойчивого кластера Windows Server с помощью общего диска, предоставленного решением SIOS DataKeeper. В документе приводятся сведения о настройках Azure, Windows и SAP, которые позволяют добиться оптимальной работы высокодоступной среды. Это руководство дополняет документацию по установке SAP и комментарии по SAP, которые являются основными ресурсами по установке и развертыванию SAP на упомянутых платформах.

Последнее обновление: август 2015 г.

[Загрузить руководство](http://go.microsoft.com/fwlink/?LinkId=613056)




<!--HONumber=Nov16_HO3-->


