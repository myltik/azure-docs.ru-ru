---
title: Использование средства проверки шаблонов для Azure Stack | Документация Майкрософт
description: Проверка шаблонов на пригодность к развертыванию в Azure Stack
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 61c893848176a89b4b6ed8d7a46f27bdeff5cec1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294500"
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>Проверьте свои шаблоны для Azure Stack с помощью средства проверки шаблонов.

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

С помощью средства проверки шаблонов вы можете убедиться, что [шаблоны](azure-stack-arm-templates.md) Azure Resource Manager готовы к развертыванию в Azure Stack. Средство проверки шаблона предоставляется в составе средств Azure Stack. Чтобы скачать средства Azure Stack с GitHub, выполните действия, описанные в [этой статье](azure-stack-powershell-download.md).

## <a name="overview"></a>Обзор

Чтобы проверить шаблон, сначала нужно создать файл возможностей облака, а затем запустить средство проверки. Можно использовать приведенные ниже модули PowerShell с помощью инструментов Azure Stack.

- В папке **CloudCapabilities**:<br>         AzureRM.CloudCapabilities.psm1 создает JSON-файл со списком возможностей облака, где перечисляются доступные в облаке Azure Stack службы и версии.
- В папке **TemplateValidator**:<br>
AzureRM.TemplateValidator.psm1 использует JSON-файл облачных возможностей для проверки шаблонов на возможность развертывания в Azure Stack.

## <a name="build-the-cloud-capabilities-file"></a>Создание файла возможностей облака

Прежде чем использовать средство проверки шаблонов, запустите модуль PowerShell AzureRM.CloudCapabilities, чтобы создать файл JSON.

>[!NOTE]
>При обновлении интегрированной системы и добавлении новых служб или расширений виртуальной машины следует еще раз запустить этот модуль.

1. Убедитесь, что у вас есть подключение к Azure Stack. Эти шаги можно выполнить на узле пакета средств разработки Azure Stack или на рабочей станции, подключенной через [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).
2. Импортируйте модуль PowerShell AzureRM.CloudCapabilities:

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. С помощью командлета Get-CloudCapabilities получите версии служб и создайте JSON-файл облачных возможностей. Если вы не укажете параметр **-OutputPath**, файл AzureCloudCapabilities.Json будет создан в текущем каталоге. Укажите фактическое расположение.

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Проверка шаблонов

Этот процесс позволяет проверить шаблоны с помощью модуля PowerShell AzureRM.TemplateValidator. Вы можете использовать собственные шаблоны или взять для примера [шаблоны быстрого запуска Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Импортируйте модуль AzureRM.TemplateValidator.psm1 PowerShell:

    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Запустите средство проверки шаблонов:

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Все предупреждения и ошибки, сгенерированные в процессе проверки, выводятся в консоль PowerShell и HTML-файл в исходном каталоге. На следующем снимке экрана показан пример отчета о проверке.

![Пример отчета о проверке шаблона](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Параметры

Проверяющий элемент управления для шаблонов поддерживает следующие параметры.

| Параметр | ОПИСАНИЕ | Обязательно |
| ----- | -----| ----- |
| TemplatePath | Указывает путь для рекурсивного поиска шаблонов Azure Resource Manager. | Yes | 
| TemplatePattern | Задает шаблон имени, по которому выбираются файлы. | Нет  |
| CapabilitiesPath | Указывает путь к JSON-файлу возможностей облака | Yes | 
| IncludeComputeCapabilities | Включает оценку ресурсов IaaS, например размеров и расширений виртуальных машин. | Нет  |
| IncludeStorageCapabilities | Включает оценку ресурсов хранения, например типов SKU. | Нет  |
| Отчет | Указывает имя создаваемого HTML-отчета. | Нет  |
| Подробная информация | Выводит ошибки и предупреждения в консоль. | Нет |

### <a name="examples"></a>Примеры

Этот пример проверяет все [шаблоны быстрого запуска Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates), скачанные в локальное хранилище. Пример также проверяет размеры и расширения виртуальной машины на соответствие возможностям Пакета средств разработки Azure Stack.

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="next-steps"></a>Дополнительная информация

- [Use Azure Resource Manager templates in Azure Stack](azure-stack-arm-templates.md) (Использование шаблонов Resource Manager в Azure Stack)
- [Разработка шаблонов для Azure Stack](azure-stack-develop-templates.md)
