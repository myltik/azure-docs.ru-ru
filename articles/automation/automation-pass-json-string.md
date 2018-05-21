---
title: Передача объекта JSON в модуль runbook службы автоматизации Azure
description: Как передать параметры в runbook в виде объекта JSON
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: powershell, runbook, json, служба автоматизации azure
ms.openlocfilehash: b0eaa13baa3e787db14e7a6f915018c3a4f280a1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Передача объекта JSON в модуль runbook службы автоматизации Azure

Иногда может быть полезным хранить данные, которые необходимо передать в модуль runbook, в файле JSON.
Например, можно создать файл JSON, который содержит все параметры, которые необходимо передать в модуль runbook.
Чтобы сделать это, необходимо преобразовать файл JSON в строку, а затем преобразовать строку в объект PowerShell перед передачей содержимого файла в модуль runbook.

В этом примере мы создадим сценарий PowerShell, который вызывает [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) для запуска модуля runbook PowerShell, передавая содержимое файла JSON в runbook.
Модуль runbook PowerShell запускает виртуальную машину Azure, получая параметры для нее из переданного файла JSON.

## <a name="prerequisites"></a>предварительным требованиям
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
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
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
   Connect-AzureRmAccount
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
1. Создайте хэш-таблицу для параметров команды `Start-AzureRmAutomationRunbook`:
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

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о редактировании модулей Runbook PowerShell и рабочих процессов PowerShell с помощью текстового редактора см. в статье [Изменение текстовых модулей Runbook в службе автоматизации Azure](automation-edit-textual-runbook.md). 
* Дополнительные сведения о создании и импорте модулей runbook см. в статье [Создание или импорт модуля Runbook в службе автоматизации Azure](automation-creating-importing-runbook.md).


