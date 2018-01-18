---
title: "Поддерживаемые операционные системы на виртуальной машине для Azure Stack | Документация Майкрософт"
description: "В Azure Stack можно использовать эти операционные системы на виртуальной машине."
services: azure-stack
documentationcenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: JeffGoldner
ms.openlocfilehash: 0a31da6cbc2c245b959825a4e715d0dc7511ba99
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Поддерживаемые операционные системы на виртуальной машине для Azure Stack

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*

## <a name="windows"></a>Windows
Azure Stack поддерживает следующие операционные системы на виртуальной машине Windows. Образы в Marketplace доступны для скачивания в Azure Stack. В Marketplace недоступны клиентские образы версий Windows.

Во время развертывания Azure Stack гарантирует, что в образе установлена подходящая версия гостевого агента.

| Операционная система | ОПИСАНИЕ | ИЗДАТЕЛЬ | тип ОС; | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 с пакетом обновления 1 | 64-разрядная | Microsoft | Windows | Центр обработки данных |
| Windows Server 2012 | 64-разрядная | Microsoft | Windows | Центр обработки данных |
| Windows Server 2012 R2 | 64-разрядная | Microsoft | Windows | Центр обработки данных |
| Windows Server 2016 | 64-разрядная | Microsoft | Windows | Центр обработки данных, ядро центра обработки данных, центр обработки данных с контейнерами |
| Windows 7 | 64-разрядная версия, Профессиональная и Корпоративная | Microsoft | Windows | Нет  |
| Windows 8.1 | 64-разрядная версия, Профессиональная и Корпоративная | Microsoft | Windows | Нет  |
| Windows 10 | 64-разрядная версия, Профессиональная и Корпоративная | Microsoft | Windows | Нет  |

## <a name="linux"></a>Linux

Дистрибутивы Linux включают в себя необходимый агент Windows Azure Linux (WALA). 

> [!NOTE]
> Образы, созданные с помощью WALA версии выше 2.2.3, *не* поддерживаются и вряд ли будут развернуты.

| Дистрибутив | ОПИСАНИЕ | ИЗДАТЕЛЬ | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Контейнер Linux |  64-разрядная | CoreOS | Stable |
| Версия 6.9 на основе CentOS | 64-разрядная | Rogue Wave | Yes |
| Версия 7.3 на основе CentOS | 64-разрядная | Rogue Wave | Yes |
| Версия 7.4 на основе CentOS | 64-разрядная | Rogue Wave | Yes |
| Debian 8 "Jessie" | 64-разрядная | credativ |  Yes |
| Debian 9 "Stretch" | 64-разрядная | credativ | Yes |
| Oracle Linux | 64-разрядная | Oracle | Нет  |
| Red Hat Enterprise Linux 7.x | 64-разрядная | Red Hat | Нет  |
| SLES 11SP4 | 64-разрядная | SUSE | Yes |
| SLES 12SP3 | 64-разрядная | SUSE | Yes |
| Ubuntu 14.04-LTS | 64-разрядная | Canonical | Yes |
| Ubuntu 16.04-LTS | 64-разрядная | Canonical | Yes |

В будущем возможна поддержка других дистрибутивов Linux.




