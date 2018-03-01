---
title: "Архитектура Пакета средств разработки Azure Stack | Документация Майкрософт"
description: "Обзор архитектуры Пакета средств разработки Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: b754ff5b5a82ac284eb59ff9b9d30a581f3d3af5
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2018
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Архитектура Пакета средств разработки Azure Stack

*Область применения: Пакет средств разработки Azure Stack*

Пакет средств разработки Azure Stack — это развертывание Azure Stack с использованием одного узла. Все компоненты устанавливаются на виртуальных машинах, запущенных на одном хост-компьютере. 

## <a name="logical-architecture-diagram"></a>Схема: логическая архитектура
На следующей схеме показана логическая архитектура Пакета средств разработки Azure Stack и его компонентов.

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Роли виртуальной машины
Пакет средств разработки Azure Stack предлагает службы, которые используют следующие виртуальные машины на узле:

| ИМЯ | ОПИСАНИЕ |
| ----- | ----- |
| **AzS-ACS01** | Службы хранилища Azure Stack.|
| **AzS-ADFS01** | Службы федерации Active Directory (AD FS).  |
| **AzS-BGPNAT01** | Пограничный маршрутизатор, а также возможности NAT и VPN для Azure Stack. |
| **AzS-CA01** | Службы центра сертификации для служб ролей Azure Stack.|
| **AzS-DC01** | Службы Active Directory, DNS и DHCP для Microsoft Azure Stack.|
| **AzS-ERCS01** | Виртуальная машина консоли аварийного восстановления. |
| **AzS-GWY01** | Службы пограничных шлюзов, включая VPN-подключения типа "сеть — сеть" для сетей клиентов.|
| **AzS-NC01** | Сетевой контроллер, который управляет сетевыми службами Azure Stack.  |
| **AzS-SLB01** | Службы мультиплексора балансировки нагрузки в Azure Stack для клиентов и служб инфраструктуры Azure Stack.  |
| **AzS-SQL01** | Внутреннее хранилище данных для ролей инфраструктуры Azure Stack.  |
| **AzS-WAS01** | Службы Azure Resource Manager и портал администрирования Azure Stack.|
| **AzS-WASP01**| Портал пользователя (клиента) Azure Stack и службы Azure Resource Manager.|
| **AzS-XRP01** | Контроллер управления инфраструктурой для Microsoft Azure Stack, включая поставщиков вычислительных и сетевых ресурсов, а также ресурсов хранения.|


## <a name="next-steps"></a>Дополнительная информация
[Развертывание Azure Stack](azure-stack-deploy.md)

[Первые пробные сценарии](azure-stack-first-scenarios.md)

