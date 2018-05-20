---
title: Компилирование конфигураций в Azure Automation DSC
description: В этой статье описывается, как компилировать конфигурации службы настройки требуемого состояния (DSC) для службы автоматизации Azure.
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: d10a11987aa8ca1cfa5bbda3ee611ea329be3bd4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Компилирование конфигураций в Azure Automation DSC

Вы можете компилировать конфигурации службы настройки требуемого состояния (DSC) двумя способами: на портале Azure или с помощью PowerShell. Приведенная ниже таблица поможет определить, когда и какой метод использовать с учетом характеристик каждого метода:

### <a name="azure-portal"></a>Портал Azure

* Простейший способ с интерактивным пользовательским интерфейсом.
* Форма для предоставления значений простых параметров.
* Легко отслеживаемое состояние задания.
* Доступ с проверкой подлинности в Azure.

### <a name="windows-powershell"></a>Windows PowerShell

* Вызов из командной строки с помощью командлетов Windows PowerShell.
* Может быть добавлено в автоматизированное решение, состоящее из нескольких шагов.
* Необходимо предоставить значения простых и сложных параметров.
* Отслеживание состояния задания
* Для поддержки командлетов PowerShell необходим клиент.
* Передача данных ConfigurationData.
* Компилирование конфигураций, использующих учетные данные.

После выбора метода компиляции выполните процедуры, описанные ниже, чтобы начать компилирование.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Компилирование конфигурации DSC с помощью портала Azure

1. В учетной записи службы автоматизации щелкните **DSC Configurations** (Конфигурации DSC).
2. Щелкните конфигурацию, чтобы открыть ее колонку.
3. Нажмите кнопку **Компилировать**.
4. Если нет параметров конфигурации, нужно подтвердить ее компиляцию. Если параметры конфигурации есть, отобразится колонка **Compile Configuration** (Компиляция конфигурации), в которой можно указать значения параметров. Дополнительные сведения о параметрах см. в разделе [**Базовые параметры**](#basic-parameters) ниже.
5. Откроется колонка **Задание компилирования** , где вы можете отследить статус задания компилирования, а также конфигурации узла (документы конфигурации MOF), которые это задание расположило на опрашивающем сервере Azure Automation DSC.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Компилирование конфигурации DSC с помощью Windows PowerShell

Чтобы начать компилирование с помощью Windows PowerShell, вы можете использовать [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) . В следующем примере кода запускается компилирование конфигурации DSC под именем **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` возвращает объект задания компилирования, с помощью которого вы можете отслеживать состояние. После этого вы можете использовать этот объект задания компилирования с помощью [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob), чтобы определить статус задания компилирования, или с помощью [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput), чтобы просматривать его потоки (выходные данные). В следующем примере кода мы запускаем компилирование конфигурации **SampleConfig** , ждем, пока оно завершится, а затем отображаем его потоки.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Базовые параметры
Объявление параметров, в том числе типов и свойств параметров, в конфигурациях DSC выполняется так же, как и в модулях Runbook службы автоматизации Azure. Дополнительные сведения о параметрах модуля Runbook см. в статье [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

Чтобы определить значения свойств в конфигурации узла **ParametersExample.sample**, созданной во время компилирования, в следующем примере используются два параметра — **FeatureName** и **IsPresent**.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]

        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = "Present"
    if($IsPresent -eq $false)
    {
        $EnsureString = "Absent"
    }

    Node "sample"
    {
        WindowsFeature ($FeatureName + "Feature")
        {
            Ensure = $EnsureString
            Name = $FeatureName
        }
    }
}
```

Вы можете компилировать конфигурации DSC, использующие базовые параметры, на портале Azure Automation DSC или с помощью Azure PowerShell:

### <a name="portal"></a>Microsoft Azure

Чтобы ввести значения параметров на портале, нажмите кнопку **Компилировать**.

![замещающий текст](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

Для модуля PowerShell нужны параметры в таблице [hashtable](http://technet.microsoft.com/library/hh847780.aspx) , в которой раздел соответствует имени параметра, а значение — значению параметра.

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

Сведения о передаче учетных данных PSCredentials в качестве параметров см. в разделе <a href="#credential-assets">**Активы учетных данных**</a> ниже.

## <a name="composite-resources"></a>Составные ресурсы

**Составные ресурсы** позволяют использовать конфигурации DSC в качестве вложенных ресурсов в рамках конфигурации. Благодаря этому можно применить несколько конфигураций к одному ресурсу. См. статью [Составные ресурсы: использование DSC как ресурса](https://docs.microsoft.com/powershell/dsc/authoringresourcecomposite) для получения дополнительных сведений о **составных ресурсах**

> [!NOTE]
> Для правильной компиляции **составных ресурсов** убедитесь, что все ресурсы DSC, которые использует составной ресурс, установлены в репозитории модулей учетной записи службы автоматизации Azure. Иначе импорт не будет выполнен правильно.

Чтобы добавить **составной ресурс** DSC, необходимо добавить модуль ресурса в архив (*.zip). Перейдите в репозиторий модулей в вашей учетной записи службы автоматизации Azure. Затем нажмите кнопку "Добавить модуль".

![Добавление модуля](./media/automation-dsc-compile/add_module.png)

Перейдите в каталог с архивом. Выберите файл архива и нажмите кнопку "ОК".

![Выбор модуля](./media/automation-dsc-compile/select_dscresource.png)

Вы вернетесь к каталогу модулей, где можно отслеживать состояние **составного ресурса** по мере его распаковки и регистрации в службе автоматизации Azure.

![Импорт составного ресурса](./media/automation-dsc-compile/register_composite_resource.png)

Зарегистрированный модуль можно щелкнуть и проверить, что **составные ресурсы** теперь доступны для использования в конфигурации.

![Проверка составного ресурса](./media/automation-dsc-compile/validate_composite_resource.png)

Затем вы сможете вызвать **составной ресурс** в своей конфигурации следующим образом:

```powershell

    Node ($AllNodes.Where{$_.Role -eq "WebServer"}).NodeName
    {
            
            JoinDomain DomainJoin
            {
                DomainName = $DomainName
                Admincreds = $Admincreds
            }

            PSWAWebServer InstallPSWAWebServer
            {
                DependsOn = "[JoinDomain]DomainJoin"
            }        
    }

```

## <a name="configurationdata"></a>ConfigurationData
Параметр **ConfigurationData** позволяет при использовании PowerShell DSC отделить конфигурацию структуры от любой конфигурации среды. Дополнительные сведения о **ConfigurationData** см. в публикации блога [Separating "What" from "Where" in PowerShell DSC](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) (Разница между "что" и "где" в DSC PowerShell).

> [!NOTE]
> Вы можете использовать **ConfigurationData**, когда выполняете компилирование на платформе Azure Automation DSC с помощью Azure PowerShell. Не следует использовать этот параметр на портале Azure.

В приведенном ниже примере конфигурации DSC параметр **ConfigurationData** используется через ключевые слова **$ConfigurationData** и **$AllNodes**. Для этого примера также нужен [модуль **xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/):

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
        xWebsite Site
        {
            Name = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure   = "Present"
        }
    }
}
```

Вы можете компилировать конфигурацию DSC, показанную выше, с помощью PowerShell. Следующая команда PowerShell добавляет две конфигурации узла в опрашивающий сервер службы автоматизации Azure: **ConfigurationDataSample.MyVM1** и **ConfigurationDataSample.MyVM3**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "MyVM1"
            Role = "WebServer"
        },
        @{
            NodeName = "MyVM2"
            Role = "SQLServer"
        },
        @{
            NodeName = "MyVM3"
            Role = "WebServer"
        }
    )

    NonNodeData = @{
        SomeMessage = "I love Azure Automation DSC!"
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData
```

## <a name="assets"></a>Активы

Ссылки на активы одинаковы в конфигурациях и модулях Runbook платформы Azure Automation DSC. Дополнительную информацию см. в следующих статьях:

* [Certificates](automation-certificates.md)
* [Подключения](automation-connections.md)
* [Учетные данные](automation-credentials.md)
* [Переменные](automation-variables.md)

### <a name="credential-assets"></a>Активы учетных данных

Хотя конфигурации DSC в службе автоматизации Azure могут ссылаться на ресурсы-контейнеры учетных данных с помощью командлета **Get-AutomationPSCredential**, при необходимости эти ресурсы-контейнеры можно передавать и в качестве параметров. Если конфигурация принимает параметр типа **PSCredential** , в качестве значения этого параметра нужно использовать имя строки ресурса учетных данных (используется в службе автоматизации Azure), а не объект PSCredential. Названный этим именем актив учетных данных, которые используются для службы автоматизации Azure, будет в фоновом режиме извлечен и передан в конфигурацию.

Чтобы обеспечить безопасность учетных данных в конфигурациях узла (документы конфигурации MOF), учетные данные нужно зашифровать в MOF-файле конфигурации узла. Служба автоматизации Azure делает больше — она зашифровывает MOF-файл целиком. Но сейчас в модуле DSC PowerShell нужно подтверждать, что учетные данные можно отображать в формате обычного текста во время создания MOF-файла конфигурации узла. Это связано с тем, что модулю DSC PowerShell неизвестно, что, когда MOF-файл будет создан с помощью задачи компилирования, служба автоматизации Azure будет шифровать его целиком.

Вы можете подтвердить в модуле DSC PowerShell, что учетные данные можно отобразить в формате обычного текста в MOF-файлах конфигурации узлов, использующих [**ConfigurationData**](#configurationdata). `PSDscAllowPlainTextPassword = $true` следует передать через **ConfigurationData** для каждого имени блока узла, которое отображается в конфигурации DSC и для которого нужны учетные данные.

В следующем примере показана конфигурация DSC, использующая актив учетных данных автоматизации.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential "SomeCredentialAsset"

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath = "\\Server\share\path\file.ext"
            DestinationPath = "C:\destinationPath"
            Credential = $Cred
        }
    }
}
```

Вы можете компилировать конфигурацию DSC, показанную выше, с помощью PowerShell. При помощи следующей команды PowerShell в опрашивающий сервер Azure Automation DSC добавляются две конфигурации узла: **CredentialSample.MyVM1** и **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "*"
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = "MyVM1"
        },
        @{
            NodeName = "MyVM2"
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData
```

> [!NOTE]
> После завершения компиляции может появиться следующее сообщение об ошибке: **The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported** (Модуль Microsoft.PowerShell.Management не импортирован, так как уже импортирована оснастка Microsoft.PowerShell.Management). Это предупреждение можно спокойно проигнорировать.

## <a name="importing-node-configurations"></a>Импорт конфигураций узлов

Вы также можете импортировать конфигурации узла (MOF-файлы), скомпилированные вне Azure. Одним из преимуществ является то, что конфигурации узла могут быть заверены.
Заверенная конфигурация узла проверяется локально на управляемом узле агентом DSC. Тем самым гарантируется, что конфигурация, применяемая к узлу, передана из авторизованного источника.

> [!NOTE]
> Заверенные конфигурации можно импортировать в учетную запись службы автоматизации Azure, но служба автоматизации Azure в настоящее время не поддерживает компиляцию заверенных конфигураций.

> [!NOTE]
> Размер файла конфигурации узла не должен превышать 1 МБ, чтобы его можно было импортировать в службу автоматизации Azure.

Чтобы узнать, как подписывать конфигурации узла, перейдите по адресу https://msdn.microsoft.com/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Импорт конфигурации узла на портале Azure

1. В учетной записи службы автоматизации щелкните **Конфигурации узла DSC** в разделе **Управление конфигурацией**.

    ![Конфигурации узла DSC](./media/automation-dsc-compile/node-config.png)
2. В колонке **DSC node configurations** (Конфигурации узла DSC) щелкните **Add a NodeConfiguration** (Добавить конфигурацию узла).
3. В колонке **Import** (Импорт) щелкните значок папки рядом с текстовым полем **Node Configuration File** (Файл конфигурации узла), чтобы найти файл конфигурации узла (MOF) на локальном компьютере.

    ![Поиск локального файла](./media/automation-dsc-compile/import-browse.png)
4. В текстовом поле **Configuration Name** (Имя конфигурации) введите имя. Это имя должно совпадать с именем конфигурации, из которой была скомпилирована данная конфигурация узла.
5. Последовательно выберите **ОК**.

### <a name="importing-a-node-configuration-with-powershell"></a>Импорт конфигурации узла с помощью PowerShell

Для импорта конфигурации узла в учетную запись службы автоматизации можно использовать командлет [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration).

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```



