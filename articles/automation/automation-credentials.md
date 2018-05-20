---
title: Ресурсы учетных данных в службе автоматизации Azure
description: Ресурсы-контейнеры учетных данных службы автоматизации Azure содержат учетные данные безопасности, которые могут использоваться для проверки подлинности при доступе к ресурсам с помощью модуля Runbook или конфигурации DSC. В этой статье описывается, как создать ресурсы-контейнеры учетных данных и использовать их в модуле Runbook или конфигурации DSC.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 825afb1e27dd8e2091a893aea825c0c0e54fa448
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="credential-assets-in-azure-automation"></a>Ресурсы учетных данных в службе автоматизации Azure

В ресурсе-контейнере учетных данных службы автоматизации хранится объект [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential), содержащий учетные данные безопасности, например имя пользователя и пароль. Модули Runbook и конфигурации DSC могут использовать командлеты, которые принимают объект PSCredential для проверки подлинности или извлекать имя пользователя и пароль объекта PSCredential, чтобы предоставить их каким-либо приложениям или службам, требующим проверку подлинности. Свойства для учетных данных безопасно хранятся в службе автоматизации Azure. К ним можно получить доступ в модуле Runbook или конфигурации DSC с помощью действия [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Этот ключ хранится в Key Vault. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса.

## <a name="azure-classic-powershell-cmdlets"></a>Командлеты PowerShell для классического развертывания Azure

Командлеты, представленные в следующей таблице, используются для создания ресурсов учетных данных службы автоматизации и управления ими с помощью Windows PowerShell.  Они входят в состав [модуля Azure PowerShell](/powershell/azure/overview) , доступного в модулях Runbook и конфигурациях DSC службы автоматизации.

| Командлеты | ОПИСАНИЕ |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |Извлекает сведения о ресурсе учетных данных. Сами учетные данные можно извлечь только с помощью действия **Get-AutomationPSCredential** . |
| [New-AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Создает новые учетные данные службы автоматизации. |
| [Remove- AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Удаляет учетные данные службы автоматизации. |
| [Set- AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Задает свойства для существующих учетных данных службы автоматизации. |

## <a name="azurerm-powershell-cmdlets"></a>Командлеты PowerShell для AzureRM

Для AzureRM командлеты, представленные в следующей таблице, используются для создания ресурсов учетных данных службы автоматизации и управления ими с помощью Windows PowerShell.  Они входят в состав [модуля AzureRM.Automation](/powershell/azure/overview), доступного в модулях Runbook и конфигурациях DSC службы автоматизации.

| Командлеты | ОПИСАНИЕ |
|:--- |:--- |
| [Get-AzureRMAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |Извлекает сведения о ресурсе учетных данных.  |
| [New-AzureRMAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |Создает новые учетные данные службы автоматизации. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |Удаляет учетные данные службы автоматизации. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |Задает свойства для существующих учетных данных службы автоматизации. |

## <a name="activities"></a>Действия

Действия в следующей таблице используются для доступа к учетным данным в модуле Runbook и конфигурациях DSC.

| Действия | ОПИСАНИЕ |
|:--- |:--- |
| Get-AutomationPSCredential |Получает учетные данные для использования в модуле Runbook или в конфигурации DSC. Возвращает объект [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) . |

> [!NOTE]
> Не следует использовать переменные в параметре –Name действия Get-AutomationPSCredential, так как это может усложнить обнаружение зависимостей между модулями Runbook или конфигурациями DSC и ресурсами-контейнерами учетных данных во время разработки.

## <a name="python2-functions"></a>Функции Python2

Функция, приведенная в следующей таблице, используется для доступа к учетным данным в модуле Runbook Python2.

| Функция | ОПИСАНИЕ |
|:---|:---|
| automationassets.get_automation_credential | Извлекает сведения о ресурсе учетных данных. |

> [!NOTE]
> Для доступа к функциям ресурсов, необходимо импортировать модуль automationassets в верхнюю часть модуля Runbook Python.

## <a name="creating-a-new-credential-asset"></a>Создание нового ресурса учетных данных

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Создание нового ресурса учетных данных на портале Azure

1. Из учетной записи службы автоматизации щелкните **Ресурсы**, чтобы открыть колонку **Ресурсы**.
2. Щелкните элемент **Учетные данные**, чтобы открыть колонку **Учетные данные**.
3. Щелкните **Добавить учетные данные** в верхней части колонки.
4. Заполните форму и нажмите кнопку **Создать** для сохранения новых учетных данных.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Создание нового ресурса учетных данных с помощью Windows PowerShell

Команды, приведенные ниже в примере, демонстрируют создание новых учетных данных службы автоматизации. Сначала создается объект PSCredential с именем и паролем, а затем он используется для создания ресурса учетных данных. Кроме того, можно использовать командлет **Get-Credential** , чтобы отобразить запрос имени и пароля.

```azurepowershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Использование учетных данных PowerShell

Для получения ресурса-контейнера учетных данных из модуля Runbook или конфигурации DSC используется действие **Get-AutomationPSCredential**. Оно возвращает [объект PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx), который можно использовать в действии или командлете, требующем параметр PSCredential. Можно также получить свойства объекта учетных данных, чтобы использовать их по отдельности. Объект имеет свойства для имени пользователя и надежного пароля. Также можно использовать метод **GetNetworkCredential** для возврата объекта [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx), предоставляющего небезопасную версию пароля.

### <a name="textual-runbook-sample"></a>Пример текстового Runbook

Команды в приведенном ниже примере демонстрируют использование учетных данных PowerShell в Runbook. В этом примере извлекаются учетные данные, и имя пользователя и пароль из них присваиваются переменным.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

### <a name="graphical-runbook-sample"></a>Пример графического Runbook

Можно добавить действие **Get-AutomationPSCredential** в графический Runbook, щелкнув правой кнопкой мыши учетные данные в области "Библиотека" графического редактора и выбрав пункт **Добавить на холст**.

![Добавление учетных данных на холст](media/automation-credentials/credential-add-canvas.png)

На следующем рисунке показан пример использования учетных данных в графическом Runbook.  В этом случае они используются для аутентификации Runbook для доступа к ресурсам Azure, как описано в разделе [Authenticate Runbooks with Azure AD User account](automation-create-aduser-account.md)(Проверка подлинности модулей Runbook с помощью учетной записи пользователя Azure AD).  Первое действие получает учетные данные с доступом к подписке Azure.  Затем действие **Add-AzureAccount** использует эти учетные данные для аутентификации любых последующих действий.  Здесь используется [конвейерная связь](automation-graphical-authoring-intro.md#links-and-workflow) , так как **Get-AutomationPSCredential** ожидает один объект.  

![Добавление учетных данных на холст](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Использование учетных данных PowerShell в DSC

Хотя конфигурации DSC в службе автоматизации Azure могут ссылаться на ресурсы-контейнеры учетных данных с помощью командлета **Get-AutomationPSCredential**, при необходимости эти ресурсы-контейнеры можно передавать и в качестве параметров. Дополнительные сведения см. в статье [Компилирование конфигураций в Azure Automation DSC](automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Использование учетных данных в Python2

Ниже приведен пример доступа к учетным данным в модулях Runbook Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о графической разработке см. в разделе [Связи и рабочий процесс](automation-graphical-authoring-intro.md#links-and-workflow).
* Сведения о различных методах аутентификации в службе автоматизации см. в статье [Обеспечение безопасности в службе автоматизации Azure](automation-security-overview.md).
* Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](automation-first-runbook-graphical.md).
* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, обратитесь к статье [Мой первый модуль Runbook рабочего процесса PowerShell](automation-first-runbook-textual.md) 
* Сведения о том, как начать работу с модулями Runbook Python2, см. в статье [My first Python runbook](automation-first-runbook-textual-python2.md) (Мой первый модуль Runbook Python). 

