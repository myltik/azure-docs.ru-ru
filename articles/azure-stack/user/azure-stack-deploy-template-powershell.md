---
title: "Развертывание шаблонов в Azure Stack с помощью PowerShell | Документация Майкрософт"
description: "Узнайте, как развернуть виртуальную машину с помощью шаблона Resource Manager и PowerShell."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e4837be016b569dbd0b4bf8e071e6381b8daa85f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-in-azure-stack-using-powershell"></a>Развертывание шаблонов в Azure Stack с помощью PowerShell

*Область применения: интегрированные системы Azure Stack и пакет средств разработки Azure Stack*

С помощью PowerShell можно развертывать шаблоны Azure Resource Manager в пакет средств разработки Azure Stack.  Используя шаблоны Resource Manager, можно развернуть и подготовить все ресурсы для приложения в рамках одной скоординированной операции.

## <a name="run-azurerm-powershell-cmdlets"></a>Выполнение командлетов PowerShell для AzureRM
В этом примере вы выполните скрипт для развертывания виртуальной машины в пакет средств разработки Azure Stack с помощью шаблона Resource Manager.  Прежде чем продолжить, [настройте PowerShell](azure-stack-powershell-configure-user.md).  

В этом примере шаблона в качестве виртуального жесткого диска используется WindowsServer-2012-R2-Datacenter.

1. Откройте <http://aka.ms/AzureStackGitHub>, найдите шаблон **101-simple-windows-vm** и сохраните его в следующее расположение: c:\\templates\\azuredeploy-101-simple-windows-vm.json.
2. В PowerShell выполните следующий сценарий развертывания. Замените *username* и *password* своими значениями. При последующем использовании увеличивайте значение параметра *$myNum*, чтобы избежать перезаписи развертывания.
   
   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
       New-AzureRmResourceGroupDeployment `
           -Name myDeployment$myNum `
           -ResourceGroupName $RGName `
           -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
           -NewStorageAccountName mystorage$myNum `
           -DnsNameForPublicIP mydns$myNum `
           -AdminUsername <username> `
           -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
           -VmName myVM$myNum `
           -WindowsOSVersion 2012-R2-Datacenter
   ```
3. Откройте портал Azure Stack, выберите последовательно **Обзор**, **Виртуальные машины** и найдите созданную вами новую виртуальную машину (*myDeployment001*).


## <a name="next-steps"></a>Дальнейшие действия
[Развертывание шаблонов с помощью Visual Studio](azure-stack-deploy-template-visual-studio.md)

