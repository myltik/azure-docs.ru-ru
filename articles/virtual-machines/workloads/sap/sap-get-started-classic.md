---
title: "Использование SAP на виртуальных машинах Linux | Документация Майкрософт"
description: "Узнайте об использовании SAP на виртуальных машинах в Microsoft Azure."
services: virtual-machines-linux,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: timlt
editor: 
tags: azure-service-management
keywords: 
ms.assetid: f9cd93dc-71ad-48a4-8778-4e48aec484a6
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: campaign-page
ms.tgt_pltfrm: vm-linux
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 66eb53f99ce4b30ec283243deb3649c9ca897a2b
ms.lasthandoff: 04/03/2017


---
# <a name="using-sap-on-linux-virtual-machines-in-azure"></a>Использование SAP в виртуальных машинах Linux в Azure
Облачные вычисления — это широко используемый термин, который приобретает все большее значение в отрасли ИТ, начиная от малых компаний и заканчивая крупными и транснациональными корпорациями. Microsoft Azure — это созданная корпорацией Майкрософт платформа облачных служб, предоставляющая широкий спектр новых возможностей. Теперь клиенты могут быстро подготавливать и отзывать приложения в формате облачных служб без технических или бюджетных ограничений. Вместо того, чтобы тратить время и средства на создание и поддержку аппаратной инфраструктуры, компании могут сконцентрироваться на приложениях, бизнес-процессах, а также связанных преимуществах для клиентов и пользователей.

Корпорация Майкрософт предоставляет виртуальные машины Microsoft Azure как комплексную платформу "инфраструктура как услуга" (IaaS). Виртуальные машины Azure (IaaS) поддерживают приложения на основе SAP NetWeaver. Приведенная ниже техническая документация описывает планирование и реализацию приложений на основе SAP NetWeaver в виртуальных машинах Windows в Azure. Приложения на основе SAP NetWeaver можно также реализовать на [виртуальных машинах Windows](../../virtual-machines-windows-classic-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure-suse-linux-virtual-machines"></a>SAP NetWeaver на виртуальных машинах Azure под управлением SUSE Linux
Заголовок. Тестирование SAP NetWeaver на виртуальных машинах Microsoft Azure под управлением SUSE Linux

Содержание. На данный момент времени официальной поддержки SAP для запуска SAP NetWeaver на виртуальных машинах Azure под управлением Linux нет. Тем не менее клиентам может потребоваться выполнить тестирование или демонстрационный запуск SAP или систем обучения на виртуальных машинах Azure Linux при условии, что нет необходимости для обращения в службу поддержки SAP. Эта статья поможет выполнить настройку виртуальных машин Azure под управлением SUSE Linux для запуска SAP и предоставляет некоторые базовые советы, которые помогут избежать потенциальных подводных камней.

Последнее обновление: декабрь 2015 г.

[Эту статью можно найти здесь](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


