---
title: "Использование средства проверки шаблонов для Azure Stack | Документация Майкрософт"
description: "Проверка шаблонов на пригодность к развертыванию в Azure Stack"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: c99e5ebc2612e10f42bddbbd2f1c17d7404305d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>Проверьте свои шаблоны для Azure Stack с помощью средства проверки шаблонов

*Область применения: интегрированные системы Azure Stack и пакет SDK для Azure Stack*

С помощью средства проверки шаблонов вы можете убедиться, что [шаблоны](azure-stack-arm-templates.md) Azure Resource Manager готовы к развертыванию в Azure Stack. Средство проверки шаблона предоставляется в составе средств Azure Stack. Чтобы скачать средства Azure Stack с GitHub, выполните действия, описанные в [этой статье](azure-stack-powershell-download.md). 

Чтобы проверить шаблоны, вам потребуются перечисленные ниже модули PowerShell и JSON-файлы, расположенные в папках **TemplateValidator** и **CloudCapabilities**. 

 - AzureRM.CloudCapabilities.psm1 создает JSON-файл со списком возможностей облака, где перечисляются доступные в облаке (например, Azure Stack) службы и версии.
 - AzureRM.TemplateValidator.psm1 использует JSON-файл облачных возможностей для проверки шаблонов на возможность развертывания в Azure Stack.
 - По умолчанию используется файл возможностей облака AzureStackCloudCapabilities_with_AddOns_20170627.json.  Вы можете сразу создать собственный файл или потренироваться на этом. 

Следуя инструкциям в этой статье, выполните проверку шаблонов. Вы также можете создать файл возможностей облака.

## <a name="validate-templates"></a>Проверка шаблонов
Этот процесс позволяет проверить шаблоны с помощью модуля PowerShell AzureRM.TemplateValidator. Вы можете использовать собственные шаблоны или взять для примера [шаблоны быстрого запуска Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1.  Импортируйте модуль AzureRM.TemplateValidator.psm1 PowerShell:
    
    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2.  Запустите средство проверки шаблонов:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Все предупреждения и ошибки, сгенерированные в процессе проверки, выводятся в консоль PowerShell и сохраняются в HTML-файле в исходном каталоге. Ниже представлен пример выходных данных средства проверки:

![пример отчета о проверке](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Параметры

| Параметр | Описание | Обязательно |
| ----- | -----| ----- |
| TemplatePath | Указывает путь для рекурсивного поиска шаблонов Resource Manager. | Да | 
| TemplatePattern | Задает шаблон имени, по которому выбираются файлы. | Нет |
| CapabilitiesPath | Указывает путь к JSON-файлу возможностей облака | Да | 
| IncludeComputeCapabilities | Включает оценку ресурсов IaaS, например размеров и расширений виртуальных машин. | Нет |
| IncludeStorageCapabilities | Включает оценку ресурсов хранения, например типов SKU. | Нет |
| Отчет | Указывает имя создаваемого HTML-отчета. | Нет |
| Подробная информация | Выводит ошибки и предупреждения в консоль. | Нет|


### <a name="examples"></a>Примеры
В этом примере проверяются все локально сохраненные [шаблоны быстрого запуска Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates), а также размеры и расширения виртуальных машин относительно возможностей пакета SDK для Azure Stack.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="build-cloud-capabilities-file"></a>Создание файла возможностей облака
В число загруженных файлов входит стандартный файл *AzureStackCloudCapabilities_with_AddOns_20170627.json*, который описывает службы и версии, доступные в пакете SDK для Azure Stack с установленными службами PaaS.  Если вы установили дополнительные поставщики ресурсов, с помощью модуля AzureRM.CloudCapabilities PowerShell создайте новый JSON-файл, в который будут включены дополнительные службы.  

1.  Убедитесь, что у вас есть подключение к Azure Stack.  Эти шаги можно выполнить на узле пакета SDK для Azure Stack или на рабочей станции, подключенной через [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 
2.  Импортируйте модуль PowerShell AzureRM.CloudCapabilities:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  С помощью командлета Get-CloudCapabilities получите версии служб и создайте JSON-файл облачных возможностей:

    ```PowerShell
    Get-AzureRMCloudCapabilities -Location 'local' -Verbose
    ```             


## <a name="next-steps"></a>Дальнейшие действия
 - [Use Azure Resource Manager templates in Azure Stack](azure-stack-arm-templates.md) (Использование шаблонов Resource Manager в Azure Stack)
 - [Разработка шаблонов для Azure Stack](azure-stack-develop-templates.md)

