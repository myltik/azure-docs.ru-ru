---
title: "Использование шаблонов Azure Resource Manager в Azure Stack | Документация Майкрософт"
description: "Узнайте, как использовать шаблоны Azure Resource Manager в Azure Stack для подготовки ресурсов."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 7648855011e8f77c35713d2d2ae50f2e474a08a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Использование шаблонов диспетчера ресурсов Azure в Azure Stack

*Область применения: интегрированные системы Azure Stack и пакет средств разработки Azure Stack*

Используя шаблоны Azure Resource Manager, можно развернуть и подготовить все ресурсы для приложения в рамках одной скоординированной операции. Кроме того, можно повторно развернуть шаблоны, чтобы внести изменения в ресурсы в группе ресурсов.

Эти шаблоны можно развертывать с помощью портала Microsoft Azure Stack, PowerShell, командной строки и Visual Studio.

В [GitHub](http://aka.ms/azurestackgithub) доступны следующие шаблоны быстрого запуска.

## <a name="deploy-sharepoint-non-high-availability"></a>Развертывание SharePoint (с обычным уровнем доступности)
Используйте расширение PowerShell DSC для создания фермы SharePoint 2013, которая включает в себя следующие ресурсы:

* виртуальную сеть;
* три учетные записи хранения;
* два внешних балансировщика нагрузки;
* одну виртуальную машину, настроенную в качестве контроллера домена для нового леса с одним доменом;
* одну виртуальную машину, настроенную в качестве изолированного сервера SQL Server 2014;
* одну виртуальную машину, настроенную в качестве фермы SharePoint 2013 с одним компьютером.

## <a name="deploy-ad-non-high-availability"></a>Развертывание AD (с обычным уровнем доступности)
Используйте расширение PowerShell DSC для создания сервера контроллера домена AD, который включает в себя следующие ресурсы:

* виртуальную сеть;
* одна учетная запись хранения;
* один внешний балансировщик нагрузки;
* одну виртуальную машину, настроенную в качестве контроллера домена для нового леса с одним доменом;

## <a name="deploy-adsql-non-high-availability"></a>Развертывание AD или SQL (с обычным уровнем доступности)
Используйте расширение PowerShell DSC для создания изолированного сервера SQL Server 2014, который включает в себя следующие ресурсы:

* виртуальную сеть;
* две учетные записи хранения;
* один внешний балансировщик нагрузки;
* одну виртуальную машину, настроенную в качестве контроллера домена для нового леса с одним доменом;
* одну виртуальную машину, настроенную в качестве изолированного сервера SQL Server 2014;

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server
Используйте расширение PowerShell DSC, чтобы настроить локальный диспетчер конфигурации (LCM) существующей виртуальной машины и зарегистрировать ее на опрашивающем сервере DSC учетных записей службы автоматизации Azure.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Создание виртуальной машины из пользовательского образа
Можно создать виртуальную машину из настраиваемого пользовательского образа. Этот шаблон также развертывает виртуальную сеть (с DNS), общедоступный IP-адрес и сетевой интерфейс.

## <a name="simple-vm"></a>Простая виртуальная машина
Этот шаблон развертывает виртуальную машину Windows с виртуальной сетью (с DNS), общедоступным IP-адресом и сетевым интерфейсом.

## <a name="cancel-a-running-template-deployment"></a>Отмена выполняющегося развертывания шаблона
Чтобы отменить выполняющееся развертывание шаблона, используйте командлет PowerShell `Stop-AzureRmResourceGroupDeployment`.

## <a name="next-steps"></a>Дальнейшие действия
[Развертывание шаблонов с помощью портала](azure-stack-deploy-template-portal.md)

[Общие сведения об Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)

