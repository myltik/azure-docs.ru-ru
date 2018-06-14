---
title: Настройка требуемого состояния серверов и управление смещением с помощью службы автоматизации Azure
description: Руководство по управлению настройками сервера с помощью DSC службы автоматизации Azure
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
manager: carmonm
ms.topic: conceptual
ms.date: 09/25/2017
ms.openlocfilehash: 6203cc8679561fa342a2e15a3819dd5513774dde
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195215"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Настройка требуемого состояния серверов и управление смещением

Настройка требуемого состояния (DSC) службы автоматизации Azure позволяет задавать настройки для серверов и убедиться, что через время они находятся в указанном состоянии.



> [!div class="checklist"]
> * Подключение виртуальной машины для управления с помощью DSC службы автоматизации Azure.
> * Передача конфигурации в службу автоматизации Azure.
> * Компиляция конфигурации в конфигурации узла.
> * Назначение конфигурации узла управляемому узлу.
> * Проверка состояния соответствия управляемого узла

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим учебником требуется:

* Учетная запись службы автоматизации Azure. Указания по созданию учетной записи запуска от имени пользователя для службы автоматизации Azure см. в статье [Проверка подлинности модулей Runbook в Azure с помощью учетной записи запуска от имени](automation-sec-configure-azure-runas-account.md).
* Виртуальная машина Azure Resource Manager (неклассическая) под управлением Windows Server 2008 R2 или более поздней версии. Инструкции по созданию виртуальной машины см. в статье [Создание первой виртуальной машины Windows на портале Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
* Azure PowerShell 3.6.0 или более поздней версии. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Опыт работы с DSC. Дополнительные сведения о DSC см. в статье [Общие сведения о службе настройки требуемого состояния Windows PowerShell](https://docs.microsoft.com/powershell/dsc/overview).

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Connect-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Создание и передача конфигурации в службе автоматизации Azure

В этом руководстве используется простая конфигурация DSC, которая гарантирует, что на виртуальной машине установлены службы IIS.

Дополнительные сведения о конфигурации DSC см. в статье [Конфигурации DSC](https://docs.microsoft.com/powershell/dsc/configurations).

В текстовом редакторе введите следующую команду и сохраните ее локально как `TestConfig.ps1`.

```powershell
configuration TestConfig {

   Node WebServer {

      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

Вызовите командлет `Import-AzureRmAutomationDscConfiguration`, чтобы отправить конфигурацию в учетную запись службы автоматизации:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Компиляция конфигурации в конфигурации узла

Прежде чем назначать конфигурацию DSC узлу, ее нужно скомпилировать в конфигурации узла.

Дополнительные сведения о компилировании конфигураций см. в статье [Конфигурации DSC](https://docs.microsoft.com/powershell/dsc/configurations).

Вызовите командлет `Start-AzureRmAutomationDscCompilationJob`, чтобы скомпилировать конфигурацию `TestConfig` в конфигурацию узла:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

В вашей учетной записи службы автоматизации будет создана конфигурация узла с именем `TestConfig.WebServer`.

## <a name="register-a-vm-to-be-managed-by-dsc"></a>Регистрация виртуальной машины, управляемой с помощью DSC

DSC службы автоматизации Azure можно применять для управления виртуальными машинами Azure (классическими или Resource Manager), локальными виртуальными машинами, компьютерами Linux, виртуальными машинами AWS и локальными физическими компьютерами. В этом разделе рассматривается регистрация только виртуальных машин Azure Resource Manager.
Дополнительные сведения о регистрации машин других типов см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).

Вызовите командлет `Register-AzureRmAutomationDscNode`, чтобы зарегистрировать виртуальную машину в DSC службы автоматизации Azure.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName "MyResourceGroup" -AutomationAccountName "myAutomationAccount" -AzureVMName "DscVm"
```

В вашей учетной записи службы автоматизации Azure будет зарегистрирована виртуальная машина в качестве узла DSC.

### <a name="specify-configuration-mode-settings"></a>Указание параметров режима конфигурации

При регистрации виртуальной машины в качестве управляемого узла можно также указать свойства конфигурации.
Например, вы можете указать, что состояние машины должно применяться только один раз (DSC не будет пытаться применить конфигурацию после начальной проверки), указав `ApplyOnly` в качестве значения свойства **ConfigurationMode**.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationMode 'ApplyOnly'
```

Вы также можете указать, как часто DSC нужно проверять состояние конфигурации, с помощью свойства **ConfigurationModeFrequencyMins**.

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationModeFrequencyMins 60
```

Дополнительные сведения о настройке свойств конфигурации для управляемого узла см. в статье о [Register-AzureRmAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-4.3.1&viewFallbackFrom=azurermps-4.2.0).

Дополнительные сведения о настройках конфигурации DSC см. в статье [Настройка локального диспетчера конфигураций](https://docs.microsoft.com/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Назначение конфигурации узла управляемому узлу

Теперь можно назначить конфигурации скомпилированного узла для виртуальной машины, которую необходимо настроить.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -Id $node.Id
```

При этом конфигурация узла с именем `TestConfig.WebServer` присваивается зарегистрированному узлу DSC с именем `DscVm`.
По умолчанию узел DSC проверяется на соответствие конфигурации узла каждые 30 минут.
Сведения о том, как изменить интервал проверки соответствия, см. в разделе [Настройка локального диспетчера конфигураций](https://docs.microsoft.com/PowerShell/DSC/metaConfig)

## <a name="check-the-compliance-status-of-a-managed-node"></a>Проверка состояния соответствия управляемого узла

Отчеты о состоянии соответствия узла DSC можно получить, вызвав командлет `Get-AzureRmAutomationDscNodeReport`.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Id $node.Id

# Display the most recent report
$reports[0]
```

## <a name="next-steps"></a>Дополнительная информация

* Сведения о развертывании узлов для управления с помощью Azure Automation DSC см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).
* Дополнительные сведения об использовании DSC службы автоматизации Azure см. в статье [Приступая к работе с DSC службы автоматизации Azure](automation-dsc-getting-started.md).
* Сведения о компиляции конфигураций DSC, которые затем можно назначить целевым узлам, см. в статье [Компиляция конфигураций в Azure Automation DSC](automation-dsc-compile.md).
* Справочник по командлетам PowerShell для Azure Automation DSC приводится в статье [Azure​RM.​Automation](/powershell/module/azurerm.automation/#automation).
* Сведения о ценах см. на [странице с ценами на Azure Automation DSC](https://azure.microsoft.com/pricing/details/automation/).
* Пример использования DSC службы автоматизации Azure в конвейере непрерывного развертывания см. в статье [Пример использования. Непрерывное развертывание на виртуальных машинах с помощью Automation DSC и Chocolatey](automation-dsc-cd-chocolatey.md).
