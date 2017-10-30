---
title: "Расширение виртуальной машины для диагностики производительности Azure для Windows | Документация Майкрософт"
description: "Эта статья содержит сведения о расширении виртуальной машины для диагностики производительности Azure для Windows."
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: 85d4764534c77ea0e4d999e249abe456d0234d75
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Расширение виртуальной машины для диагностики производительности Azure для Windows

## <a name="summary"></a>Сводка
Расширение виртуальной машины для диагностики производительности Azure позволяет собирать данные диагностики производительности с виртуальных машин Windows, выполнять анализ и предоставляет отчет с выводами и рекомендациями для определения и решения проблем производительности виртуальных машин. Это расширение устанавливает инструмент устранения неполадок [PerfInsights](http://aka.ms/perfinsights).

## <a name="prerequisites"></a>Предварительные требования
### <a name="operating-systems"></a>Операционные системы
Это расширение можно установить на Windows Server 2008 R2, 2012, 2012 R2, 2016, а также операционных системах Windows 8.1 и Windows 10.

## <a name="extension-schema"></a>Схема расширения
В указанном ниже объекте JSON показана схема для расширения системы диагностики производительности Azure. Для этого расширения требуется имя и ключ для учетной записи хранения, чтобы хранить выходные данные и отчет диагностики. Эти значения конфиденциальные и должны храниться внутри защищенной конфигурации параметров. Данные защищенных параметров расширения виртуальной машины Azure зашифрованы. Они расшифровываются только на целевой виртуальной машине. Обратите внимание, что в storageAccountName и storageAccountKey учитывается регистр. Другие необходимые параметры указаны в разделе ниже.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
```

### <a name="property-values"></a>Значения свойств

|   **Имя**   |**Значение и пример**|       **Описание**      |
|--------------|-------------------|----------------------------|
|версия_API|2015-06-15|Версия API
|publisher|Microsoft.Azure.Performance.Diagnostics|Пространство имен издателя для расширения
|type|AzurePerformanceDiagnostics|Тип расширения виртуальной машины
|typeHandlerVersion|1.0|Версия обработчика расширения
|performanceScenario|basic|Сценарии производительности для сбора данных. Допустимые значения: **basic**, **vmslow**, **azurefiles** и **custom**.
|traceDurationInSeconds|300|Длительность трассировки, если выбран любой из параметров трассировки.
|DiagnosticsTrace|d|Параметр, позволяющий включить трассировку диагностики. Допустимые значения: **d** или пустое значение. Если вы не хотите записывать данные трассировки, просто оставьте значение пустым.
|perfCounterTrace|p|Параметр, позволяющий включить трассировку счетчиков производительности. Допустимые значения: **p** или пустое значение. Если вы не хотите записывать данные трассировки, просто оставьте значение пустым.
|networkTrace|n|Параметр, позволяющий включить трассировку Netmon. Допустимые значения: **n** или пустое значение. Если вы не хотите записывать данные трассировки, просто оставьте значение пустым.
|xperfTrace|x|Параметр, позволяющий включить трассировку XPerf. Допустимые значения: **x** или пустое значение. Если вы не хотите записывать данные трассировки, просто оставьте значение пустым.
|storPortTrace|s|Параметр, позволяющий включить трассировку StorPort. Допустимые значения: s или пустое значение. Если вы не хотите записывать данные трассировки, просто оставьте значение пустым.
|srNumber|123452016365929|Номер запроса в службу поддержки (если доступно). Если его нет, оставьте это поле пустым.
|requestTimeUtc|9/2/2017 11:06:00 PM|Текущая дата и время в формате UTC. Если вы устанавливали расширение с помощью портала, это значение можно не указывать.
|storageAccountName|mystorageaccount|Имя учетной записи хранения для хранения журналов диагностики и результатов.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|Ключ для учетной записи хранения.

## <a name="install-the-extension"></a>Установка расширения

Выполните указанные ниже действия, чтобы установить расширение виртуальной машины на виртуальных машинах Windows.

1. Войдите на [портал Azure](http://portal.azure.com).
2. Выберите виртуальную машину, на которой необходимо установить расширение.

    ![Выберите виртуальную машину.](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Выберите колонку **Расширения** и нажмите кнопку **Добавить**.

    ![Выбор меню "Расширения"](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Выберите расширение системы диагностики производительности Azure, просмотрите условия и нажмите кнопку **Создать**.

    ![Создание расширения системы диагностики производительности Azure](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Укажите значения параметров для установки и нажмите кнопку **ОК**, чтобы установить расширение. Дополнительные сведения о поддерживаемых сценариях устранения неполадок см. [здесь](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Установка расширения](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. После успешной установки отобразится сообщение, указывающее, что подготовка прошла успешно.

    ![Сообщение об успешной подготовке](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > Выполнение расширения начнется, когда подготовка будет успешно завершена. Потребуется несколько минут, чтобы выполнить базовый сценарий. Для других сценариев он будет выполняться по времени, указанному во время установки.

## <a name="remove-the-extension"></a>Удаление расширения
Чтобы удалить расширение с виртуальной машины, сделайте следующее:

1. Войдите на [портал Azure](http://portal.azure.com), выберите виртуальную машину, в которой вы хотите удалить расширение, а затем выберите колонку "Расширения". 
2. В списке строки расширения системы диагностики производительности щелкните (**...**) и выберите "Удалить".

    ![Удаление расширения](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Вы также можете выбрать запись в списке, а затем — параметр "Удалить".

## <a name="template-deployment"></a>Развертывание шаблона
Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Для запуска расширения системы диагностики производительности Azure во время развертывания шаблона Azure Resource Manager в нем можно использовать схему JSON, описанную в предыдущем разделе. Далее приведен пример шаблона, который можно использовать с шаблоном развертывания.

````
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "diagnosticsTrace": {
      "type": "string",
      "defaultValue": "d"
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>Развертывание с помощью PowerShell
Команду `Set-AzureRmVMExtension` можно использовать для развертывания расширения виртуальной машины для диагностики производительности Azure на имеющейся виртуальной машине. Перед выполнением команды необходимо сохранить открытые и закрытые конфигурации в хэш-таблице PowerShell.

PowerShell

````
$PublicSettings = @{ "performanceScenario" = "basic"; "traceDurationInSeconds" = 300; "diagnosticsTrace" = "d"; "perfCounterTrace" = "p"; "networkTrace" = ""; "xperfTrace" = ""; "storPortTrace" = ""; "srNumber" = ""; "requestTimeUtc" = "2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountName" = "mystorageaccount" ; "storageAccountKey" = "mystoragekey"}

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
````

## <a name="information-on-the-data-captured"></a>Сведения о собранных данных
Инструмент PerfInsights собирает различные журналы, конфигурации, диагностические данные и т.д. в зависимости от выбранного сценария. Дополнительные сведения о данных, собранных в одном сценарии, см. в [документации по PerfInsights](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Просмотр результатов и предоставление к ним общего доступа

По умолчанию выходные данные расширения хранятся в папке log_collection на временном диске (обычно D:\log_collection). В этой папке вы увидите ZIP-файлы, содержащие журналы диагностики и отчет с выводами и рекомендациями.

Созданный ZIP-файл также передается в учетную запись хранения, указанную во время установки. Его можно совместно использовать на протяжении 30 дней с помощью [подписанного URL-адреса (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Текстовый файл с именем *zipfilename*_saslink.tx также создается в папке log_collection. Этот файл содержит ссылку SAS, созданную для скачивания ZIP-файла. Любой пользователь, у которого есть эта ссылка, сможет скачать ZIP-файл.

Корпорация Майкрософт может использовать ссылку SAS, чтобы скачать данные диагностики для дальнейшего изучения сотрудником службы поддержки, работающим над вашим запросом на поддержку.

Чтобы просмотреть отчет, просто извлеките ZIP-файл и откройте файл **PerfInsights Report.html**.

Вы также сможете скачать ZIP-файл непосредственно на портале, выбрав расширение.

![Просмотр подробных сведений о состоянии](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Ссылка SAS, отображаемая на портале, иногда может не работать из-за неправильно сформированного URL-адреса (причиной может быть использование специальных символов) во время кодирования и декодирования операции. Чтобы обойти эту проблему, можно получить ссылку непосредственно из файла *_saslink.txt виртуальной машины.

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка
### <a name="troubleshoot"></a>Устранение неполадок

- Состояние развертывания расширения (в области уведомлений) может отображать сообщение "Выполняется развертывание", даже если подготовка расширения успешно завершена.

    Эту проблему можно спокойно проигнорировать, если состояние расширения указывает, что подготовка расширения успешно завершена.
- Некоторые проблемы во время установки можно устранить с помощью журналов расширений. Выходные данные выполнения расширения регистрируются в файле, расположенном в следующем каталоге:

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
