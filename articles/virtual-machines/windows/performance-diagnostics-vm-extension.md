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
ms.openlocfilehash: 5a7dc313f1d6453562e4d5a11ceca03e4459b043
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Расширение виртуальной машины для диагностики производительности Azure для Windows

Azure расширения ВМ диагностики производительности позволяет собирать диагностические данные производительности из виртуальных машин Windows. Модуль выполняет анализ и формирует отчет о результаты и рекомендации для выявления и устранения проблем с производительностью на виртуальной машине. Это расширение устанавливает инструмент устранения неполадок [PerfInsights](http://aka.ms/perfinsights).

## <a name="prerequisites"></a>Технические условия

Это расширение можно установить на Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 и Windows Server 2016. Он также может устанавливаться на Windows 8.1 и Windows 10.

## <a name="extension-schema"></a>Схема расширения
Следующий JSON показана схема для расширений ВМ для диагностики производительности Azure. Этот модуль требует имя и ключ для учетной записи хранилища для хранения выходных данных диагностики и отчетов. Эти значения вводятся с учетом и должны быть сохранены в конфигурации защищенного параметр. Azure VM параметра модуля защищенные данные шифруются и расшифровываются только на целевой виртуальной машине. Обратите внимание, что **storageAccountName** и **storageAccountKey** чувствительны к регистру. Другие необходимые параметры перечислены в следующем разделе.

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
|версия_API|2015-06-15|Версия API.
|publisher|Microsoft.Azure.Performance.Diagnostics|Пространство имен издателя расширения.
|Тип|AzurePerformanceDiagnostics|Тип расширения виртуальной Машины.
|typeHandlerVersion|1.0|Версия обработчика расширений.
|performanceScenario|basic|Сценарий производительности, для которого требуется собрать данные. Допустимые значения: **basic**, **vmslow**, **azurefiles** и **custom**.
|traceDurationInSeconds|300|Длительность трассировки, если выбраны параметры трассировки.
|perfCounterTrace|p|Параметр, позволяющий включить трассировку счетчиков производительности. Допустимые значения: **p** или пустое значение. Если вы не хотите записать Эта трассировка, оставьте значение в поле пустым.
|networkTrace|n|Возможность включения трассировки сети. Допустимые значения: **n** или пустое значение. Если вы не хотите записать Эта трассировка, оставьте значение в поле пустым.
|xperfTrace|x|Параметр, позволяющий включить трассировку XPerf. Допустимые значения: **x** или пустое значение. Если вы не хотите записать Эта трассировка, оставьте значение в поле пустым.
|storPortTrace|s|Параметр, позволяющий включить трассировку StorPort. Допустимые значения: **s** или пустое значение. Если вы не хотите записать Эта трассировка, оставьте значение в поле пустым.
|srNumber|123452016365929|Номер билета поддержки, если он доступен. Оставьте значение считается пустым, если у вас его нет.
|storageAccountName|mystorageaccount|Имя учетной записи хранилища для хранения журналов диагностики и результатов.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|Ключ для учетной записи хранилища.

## <a name="install-the-extension"></a>Установка расширения

Выполните следующие действия, чтобы установить расширение на виртуальных машинах.

1. Войдите на [портале Azure](http://portal.azure.com).
2. Выберите виртуальную машину, на которой необходимо установить расширение.

    ![Снимок экрана Azure portal с виртуальными машинами выделен](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Выберите **расширения** и выберите **добавить**.

    ![Снимок экрана расширения колонке с выделенным Добавление](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Выберите **диагностики производительности Azure**, ознакомьтесь с условиями, а затем выберите **создать**.

    ![Снимок экрана нового экрана ресурсов, с выделенной диагностики производительности Azure](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Укажите значения параметров для установки и выберите **ОК** для установки расширения. Дополнительные сведения о поддерживаемых сценариях см. в разделе [способы использования PerfInsights](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Снимок экрана установить расширение диалоговое окно](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Если установка выполнена успешно, появится сообщение, указывающее на это состояние.

    ![Снимок экрана для подготовки успешно сообщения](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > После успешного завершения подготовки выполняется расширение. Он принимает две минуты или меньше, чтобы завершить для базового сценария. Для других сценариев расширение выполняется в течение периода, указанного во время установки.

## <a name="remove-the-extension"></a>Удаление расширения
Чтобы удалить расширение с виртуальной машины, сделайте следующее:

1. Войдите в [портал Azure](http://portal.azure.com), выберите виртуальную машину, из которого вы хотите удалить это расширение, а затем выберите **расширения** колонку. 
2. Выберите (**...** ) для записи расширения диагностики производительности из списка и выберите **удаления**.

    ![Снимок экрана расширения колонке с выделенной удаления](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Можно выбрать запись для расширения и выбрать **удаления** параметр.

## <a name="template-deployment"></a>Развертывание шаблона
Расширения виртуальных машин Azure можно развернуть с помощью шаблонов диспетчера ресурсов Azure. В шаблоне диспетчера ресурсов Azure можно использовать схему JSON, описанные в предыдущем разделе. Это выполняется расширение ВМ диагностики производительности Azure во время развертывания шаблона диспетчера ресурсов Azure. Ниже приведен образец шаблона.

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
`Set-AzureRmVMExtension` Команда может использоваться для развертывания расширения виртуальной Машины для диагностики производительности Azure для существующей виртуальной машины. Перед выполнением команды, хранить открытые и закрытые конфигурации в PowerShell хэш-таблицу.

PowerShell

````
$PublicSettings = @{ "performanceScenario":"basic","traceDurationInSeconds":300,"perfCounterTrace":"p","networkTrace":"","xperfTrace":"","storPortTrace":"","srNumber":"","requestTimeUtc":"2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountName":"mystorageaccount","storageAccountKey":"mystoragekey"}

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
Средство PerfInsights собирает различные журналы, конфигурации и диагностических данных, в зависимости от выбранного сценария. Дополнительные сведения см. в разделе [PerfInsights документации](http://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Просмотреть и совместно использовать результаты

Выходные данные из модуля хранится в папке. Папка называется log_collection и можно найти на диске Temp (обычно D:\log_collection) по умолчанию. В этой папке вы увидите ZIP-файлов, содержащих журналы диагностики и отчет, содержащий результаты и рекомендации.

ZIP-файл также можно найти в учетной записи хранения, указанной во время установки. Доступны в течение 30 дней с помощью [подписи общего доступа (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Текстовый файл с именем *zipfilename*_saslink.tx также создается в папке log_collection. Этот файл содержит ссылку SAS, созданную для скачивания ZIP-файла. Любой пользователь, у которого есть эта ссылка, может скачать ZIP-файл.

Чтобы упростить задачу поддержки инженером на обращение в службу поддержки, корпорация Майкрософт может использовать эту ссылку, SAS для загрузки данных диагностики.

Чтобы просмотреть отчет, извлеките ZIP-файл и откройте **PerfInsights Report.html** файла.

Вы также сможете загрузить ZIP-файле непосредственно на портале, выбрав модуль.

![Снимок экрана диагностики производительности, сведения о состоянии](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> Ссылка на SAS, отображается на портале может не работать. Это может быть вызвано Неверный URL-адрес во время операции кодирования и декодирования. Вместо этого можно получить по ссылке непосредственно из файла *_saslink.txt из виртуальной Машины.

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

- Состояние развертывания расширения (в области уведомлений) может показывать «Выполняется развертывание», несмотря на то, что модуль успешно подготовлена.

    Эту проблему можно спокойно проигнорировать, до тех пор, пока состояние расширения указывает расширение успешно настроена.
- Можно решить некоторые проблемы во время установки с помощью журналов расширений. Выходные данные выполнения расширения регистрируются в файле, расположенном в следующем каталоге:

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Последовательно выберите пункты [сайт поддержки Azure](https://azure.microsoft.com/support/options/)и выберите **получение поддержки**. Сведения об использовании поддержки Azure см. в статье [поддержки Microsoft Azure часто задаваемые вопросы о](https://azure.microsoft.com/support/faq/).
