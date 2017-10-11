---
title: "Передача объекта JSON в модуль runbook службы автоматизации Azure | Документация Майкрософт"
description: "Как передать параметры в runbook в виде объекта JSON"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: "powershell, runbook, json, служба автоматизации azure"
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 06/15/2017
ms.author: eslesar
ms.openlocfilehash: eac0e95a46731b9d396ea0590e629d61ca6a7d70
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Передача объекта JSON в модуль runbook службы автоматизации Azure

Иногда может быть полезным хранить данные, которые необходимо передать в модуль runbook, в файле JSON.
Например, можно создать файл JSON, который содержит все параметры, которые необходимо передать в модуль runbook.
Чтобы сделать это, необходимо преобразовать файл JSON в строку, а затем преобразовать строку в объект PowerShell перед передачей содержимого файла в модуль runbook.

В этом примере мы создадим сценарий PowerShell, который вызывает [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) для запуска модуля runbook PowerShell, передавая содержимое файла JSON в runbook.
Модуль runbook PowerShell запускает виртуальную машину Azure, получая параметры для нее из переданного файла JSON.

## <a name="prerequisites"></a>Предварительные требования
Для работы с этим учебником требуется:

* Подписка Azure. Если у вас ее нет, [активируйте преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или <a href="/pricing/free-account/" target="_blank">[зарегистрируйте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Учетная запись службы автоматизации](automation-sec-configure-azure-runas-account.md) , чтобы хранить модуль Runbook и выполнять проверку подлинности ресурсов Azure.  Эта учетная запись должна иметь разрешение на запуск и остановку виртуальной машины.
* Виртуальная машина Azure. Это не должна быть рабочая виртуальная машина, так как в процессе изучения этого руководства ее нужно будет остановить и запустить заново.
* Azure PowerShell, установленный на локальном компьютере. Дополнительные сведения о получении Azure PowerShell см. в статье [Install and configure Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) (Установка и настройка Azure PowerShell).

## <a name="create-the-json-file"></a>Создание файла JSON

Введите следующий текст в текстовый файл и сохраните его как `test.json` в любом месте на своем локальном компьютере.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Создание модуля runbook

Создайте новый модуль runbook PowerShell с именем Test-Json в службе автоматизации Azure.
Дополнительные сведения о создании модуля runbook в PowerShell см. в статье [Мой первый модуль Runbook PowerShell](automation-first-runbook-textual-powershell.md).

Чтобы принять данные JSON, модуль runbook должен принять объект в качестве входного параметра.

Модуль runbook может использовать свойства, определенные в JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Сохраните и опубликуйте этот модуль runbook в учетной записи службы автоматизации.

## <a name="call-the-runbook-from-powershell"></a>Вызов модуля runbook из PowerShell

Теперь можно вызвать модуль runbook с локального компьютера с помощью Azure PowerShell.
Выполните следующие команды PowerShell:

1. Войдите в Azure:
   ```powershell
   Login-AzureRmAccount
   ```
    Вам будет предложено ввести учетные данные Azure.
1. Получите содержимое файла JSON и преобразуйте его в строку:
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath` — это путь, по которому был сохранен файл JSON.
1. Преобразуйте содержимое строки `$json` в объект PowerShell:
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. Создайте хэш-таблицу для параметров команды `Start-AzureRmAutomstionRunbook`:
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   Обратите внимание, что вы задаете значение `Parameters` объекту PowerShell, который содержит значения из файла JSON. 
1. Запуск модуля runbook
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

Модуль runbook использует значения из файла JSON для запуска виртуальной машины.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о редактировании модулей Runbook PowerShell и рабочих процессов PowerShell с помощью текстового редактора см. в статье [Изменение текстовых модулей Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md). 
* Дополнительные сведения о создании и импорте модулей runbook см. в статье [Создание или импорт модуля Runbook в службе автоматизации Azure](automation-creating-importing-runbook.md).


