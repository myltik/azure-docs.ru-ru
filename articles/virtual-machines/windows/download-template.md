---
title: Скачивание шаблона для виртуальной машины Azure | Документация Майкрософт
description: Автоматизация развертываний в модели развертывания с помощью Resource Manager с использованием скачанного шаблона для виртуальной машины.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 93ed84cb146119c877c3a143c5f7af9ca8ba0656
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2017
ms.locfileid: "26055795"
---
# <a name="download-the-template-for-a-vm"></a>Скачивание шаблона для виртуальной машины
При создании виртуальной машины в Azure с помощью портала или PowerShell автоматически создается шаблон Resource Manager. Этот шаблон можно использовать для быстрого дублирования развертывания. Шаблон содержит сведения обо всех ресурсах в группе ресурсов. Для виртуальной машины это означает, что шаблон содержит все, что создается для поддержки виртуальных машин в этой группе ресурсов, включая сетевые ресурсы.

## <a name="download-the-template-using-the-portal"></a>Скачивание шаблона с помощью портала
1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Виртуальные машины**.
3. Затем выберите виртуальную машину из списка.
4. Выберите элемент **Сценарий автоматизации**.
5. Выберите **Скачать** в меню сверху и сохраните ZIP-файл на локальном компьютере.
6. Откройте ZIP-файл и извлеките файлы в папку. ZIP-файл содержит:
   
   * deploy.ps1;
   * deploy.sh; 
   * deployer.rb;
   * DeploymentHelper.cs;
   * parameters.json;
   * template.json.

Файл template.json является шаблоном.

## <a name="download-the-template-using-powershell"></a>Скачивание шаблона с помощью PowerShell
JSON-файл шаблона можно также скачать с помощью командлета [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx). Имя файла и путь к JSON-файлу можно указать с помощью параметра `-path`. В этом примере показано, как скачать шаблон для группы ресурсов с именем **myResourceGroup** в папку **C:\users\public\downloads** на локальном компьютере.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о развертывании ресурсов с помощью шаблонов см. в статье [Resource Manager template walkthrough](../../azure-resource-manager/resource-manager-template-walkthrough.md) (Пошаговое руководство по шаблону Resource Manager).

