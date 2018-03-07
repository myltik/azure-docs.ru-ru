---
title: "Поддерживаемые операционные системы на виртуальной машине для Azure Stack | Документация Майкрософт"
description: "В Azure Stack можно использовать эти операционные системы на виртуальной машине."
services: azure-stack
documentationcenter: 
author: Brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: Brenduns
ms.reviewer: JeffGoldner
ms.openlocfilehash: 3eceb740b8115d2eaca517017f6158744d6e8e58
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2018
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Поддерживаемые операционные системы на виртуальной машине для Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

## <a name="windows"></a>Windows
Azure Stack поддерживает операционные системы на виртуальной машине Windows, перечисленные в таблице ниже. Образы в Marketplace доступны для скачивания в Azure Stack. В Marketplace недоступны клиентские образы версий Windows.

Во время развертывания Azure Stack внедряет подходящую версию гостевого агента в образ.

| Операционная система | ОПИСАНИЕ | ИЗДАТЕЛЬ | тип ОС; | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Windows Server 2008 R2 с пакетом обновления 1 | 64-разрядная | Microsoft | Windows | Центр обработки данных |
| Windows Server 2012 | 64-разрядная | Microsoft | Windows | Центр обработки данных |
| Windows Server 2012 R2 | 64-разрядная | Microsoft | Windows | Центр обработки данных |
| Windows Server 2016 | 64-разрядная | Microsoft | Windows | Центр обработки данных, ядро центра обработки данных, центр обработки данных с контейнерами |
| Windows 10 *(см. примечание 1)* | 64-разрядная версия, Pro и Корпоративная | Microsoft | Windows | Нет  |

***Примечание 1.*** *Чтобы развернуть клиентские операционные системы Windows 10 в Azure Stack, требуется [лицензирование Windows "на пользователя"](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx). Лицензию можно приобрести у соответствующего поставщика услуг мультитенантного размещения ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).*


## <a name="linux"></a>Linux

Дистрибутивы Linux включают в себя необходимый агент Windows Azure Linux (WALA).

> [!NOTE]   
> Образы, созданные с помощью WALA версии выше 2.2.3, *не* поддерживаются и вряд ли будут развернуты. Известно, что некоторые версии агента WALA не работают на виртуальных машинах Azure Stack, включая версии 2.2.12 и 2.2.13.
>
> [cloud-init](https://cloud-init.io/) поддерживается только для дистрибутивов Ubuntu в Azure Stack.

| Дистрибутив | ОПИСАНИЕ | ИЗДАТЕЛЬ | Marketplace |
| --- | --- | --- | --- | --- | --- |
| Контейнер Linux |  64-разрядная | CoreOS | Stable |
| Версия 6.9 на основе CentOS | 64-разрядная | Rogue Wave | Yes |
| Версия 7.4 на основе CentOS | 64-разрядная | Rogue Wave | Yes |
| Debian 8 "Jessie" | 64-разрядная | credativ |  Yes |
| Debian 9 "Stretch" | 64-разрядная | credativ | Yes |
| Red Hat Enterprise Linux 7.x (ожидается) | 64-разрядная | Red Hat | Нет  |
| SLES 11SP4 | 64-разрядная | SUSE | Yes |
| SLES 12SP3 | 64-разрядная | SUSE | Yes |
| Ubuntu 14.04-LTS | 64-разрядная | Canonical | Yes |
| Ubuntu 16.04-LTS | 64-разрядная | Canonical | Yes |

В будущем возможна поддержка других дистрибутивов Linux.
