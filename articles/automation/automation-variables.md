---
title: "Ресурсы-контейнеры переменных в службе автоматизации Azure | Документация Майкрософт"
description: "Ресурсы-контейнеры переменных — это значения, которые доступны для всех модулей Runbook и конфигураций DSC в службе автоматизации Azure.  В статье подробно рассматриваются переменные и работа с ними как в текстовых, так и в графических модулях."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: b880c15f-46f5-4881-8e98-e034cc5a66ec
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/09/2017
ms.author: magoedte;bwren
ms.openlocfilehash: e38d2b751090cfdc078de4e8c683c6bb9b48fac3
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2018
---
# <a name="variable-assets-in-azure-automation"></a>Средства переменных в службе автоматизации Azure

Ресурсы-контейнеры переменных — это значения, которые доступны для всех модулей Runbook и конфигураций DSC в учетной записи службы автоматизации. Средства можно создавать, изменять или получать с помощью портала Azure, Windows PowerShell, из модуля Runbook или конфигурации DSC. Переменные автоматизации полезны в следующих случаях:

- совместное использование значения несколькими модулями Runbook или конфигурациями DSC;

- совместное использование значения несколькими заданиями из одного модуля Runbook или конфигурации DSC;

- управление значением из портала или из командной строки Windows PowerShell, используемой модулями Runbook или конфигурациями DSC; это может быть набор типичных элементов конфигурации, такой как список имен виртуальных машин, определенная группа ресурсов, доменное имя AD и т. д.  

Переменные службы автоматизации сохраняются, чтобы они были доступными после сбоя модуля Runbook или конфигурации DSC.  Это также позволяет модулю Runbook задать значение, которое может быть использовано другим модулем или этим же модулем либо конфигурацией DSC при следующем запуске.

При создании переменной можно указать, что она должна храниться в зашифрованном виде.  Если переменная зашифрована, она безопасно хранится в службе автоматизации Azure и ее значение не может быть получено с помощью командлета [Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx), который входит в модуль Azure PowerShell.  Единственный способ получить зашифрованное значение — использовать действие **Get-AutomationVariable** в модуле Runbook или конфигурации DSC.

> [!NOTE]
> Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Ключ шифруется главным сертификатом и хранится в службе автоматизации Azure. Прежде чем сохранять безопасное средство, ключ учетной записи в службе автоматизации дешифруется с помощью главного сертификата и используется для шифрования средства.

## <a name="variable-types"></a>Типы переменных

При создании переменной с помощью портала Azure выберите тип данных из раскрывающегося списка, чтобы портал мог вывести соответствующий элемент управления для ввода значения переменной. Переменная не ограничивается этим типом данных, однако следует задать значение с помощью Windows PowerShell, если значение должно иметь другой тип. Если указать **Не задан**, то переменная получит значение **$null**, и значение необходимо указать с помощью командлета [Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx) или с помощью действия **Set-AutomationVariable**.  Невозможно создать или изменить значение для переменной сложного типа на портале, но можно указать значение любого типа с помощью Windows PowerShell. Сложные типы возвращаются в виде [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx).

Можно сохранить несколько значений в отдельной переменной, создав массив или хэш-таблицу и сохранив ее в переменную.

Ниже перечислены типы переменных, которые доступны в службе автоматизации.

* Строка
* Целое число 
* Datetime
* Логическое
* Null

## <a name="scripting-the-creation-and-management-of-variables"></a>Написание скриптов для создания переменных и управления ими

Командлеты, представленные в следующей таблице, используются для создания переменных и управления ими с помощью Windows PowerShell в службе автоматизации Azure. Они входят в состав [модуля Azure PowerShell](../powershell-install-configure.md) , доступного в модулях Runbook и конфигурации DSC службы автоматизации.

|Командлеты|ОПИСАНИЕ|
|:---|:---|
|[Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx)|Получает значение существующей переменной.|
|[New-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603613.aspx)|Создает новую переменную и устанавливает ее значение.|
|[Remove-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt619354.aspx)|Удаляет существующую переменную.|
|[Set-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603601.aspx)|Получает значение существующей переменной.|

Действия рабочего процесса в следующей таблице используются для доступа к переменным автоматизации в модуле Runbook. Они доступны для использования в модуле Runbook или конфигурации DSC и не поставляются вместе с модулем Azure PowerShell.

|Действия рабочего процесса|ОПИСАНИЕ|
|:---|:---|
|Get-AutomationVariable|Получает значение существующей переменной.|
|Set-AutomationVariable|Получает значение существующей переменной.|

> [!NOTE] 
> Не следует использовать переменные в параметре –Name действия **Get-AutomationVariable** в модуле Runbook или конфигурации DSC, поскольку это усложняет определение зависимостей между модулями Runbook или конфигурацией DSC и переменными службы автоматизации во время разработки.

Функции, приведенные в следующей таблице, используются для доступа к переменным и их извлечения в модуле Runbook Python2. 

|Функции Python2|ОПИСАНИЕ|
|:---|:---|
|automationassets.get_automation_variable|Получает значение существующей переменной. |
|automationassets.set_automation_variable|Получает значение существующей переменной. |

> [!NOTE] 
> Для доступа к функциям ресурсов, необходимо импортировать модуль automationassets в верхнюю часть модуля Runbook Python.

## <a name="creating-a-new-automation-variable"></a>Создание новой переменной автоматизации

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Создание новой переменной на портале Azure

1. В учетной записи службы автоматизации щелкните плитку **Ресурсы**, а затем в колонке **Ресурсы** выберите **Переменные**.
2. На плитке **Переменные** выберите **Добавить переменную**.
3. Укажите параметры в колонке **Новая переменная** и нажмите кнопку **Создать**, чтобы сохранить новую переменную.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Создание переменной с помощью Windows PowerShell

Командлет [New-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603613.aspx) создает переменную и задает ее исходное значение. Значение можно получить с помощью [Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx). Если значение простого типа, возвращается тот же тип. Если это сложный тип, возвращается **PSCustomObject** .

Приведенные ниже примеры команд демонстрируют создание переменной строкового типа и возврат ее значения.

    New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
    –Encrypted $false –Value 'My String'
    $string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

Приведенные ниже примеры команд демонстрируют создание переменной сложного типа и возврат ее свойств. В данном случае используется объект виртуальной машины из **Get-AzureRmVm**.

    $vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
    New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
    
    $vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
    $vmName = $vmValue.Name
    $vmIpAddress = $vmValue.IpAddress



## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Использование переменной в модуле Runbook или конфигурации DSC

Используйте действие **Set-AutomationVariable**, чтобы задать значение переменной службы автоматизации в модуле Runbook PowerShell или конфигурации DSC, и действие **Get-AutomationVariable**, чтобы получить его.  Не следует использовать командлеты **Set-AzureAutomationVariable** и **Get-AzureAutomationVariable** в модуле Runbook или конфигурации DSC, поскольку они менее эффективны, чем действия рабочего процесса.  Также с помощью **Get-AzureAutomationVariable**нельзя получить значение безопасных переменных.  Единственный способ создать переменную из модуля Runbook или конфигурации DSC — использовать командлет [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx).


### <a name="textual-runbook-samples"></a>Текстовые примеры модуля Runbook

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Установка и получение простого значения из переменной

Приведенные ниже примеры команд демонстрируют задание и получение переменной в текстовом модуле Runbook. В примере предполагается, что целочисленные переменные *NumberOfIterations* и *NumberOfRunnings* и строковая переменная *SampleMessage* уже созданы.

    $NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
    $NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
    $SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
    
    Write-Output "Runbook has been run $NumberOfRunnings times."
    
    for ($i = 1; $i -le $NumberOfIterations; $i++) {
       Write-Output "$i`: $SampleMessage"
    }
    Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>Задание и получение сложного объекта в переменной

В следующем примере кода показано, как добавить в переменную сложное значение в текстовом модуле Runbook. В этом примере виртуальная машина Azure вызывается с помощью команды **Get-AzureVM** и сохраняется в существующую переменную автоматизации.  Как указано в разделе [Типы переменных](#variable-types), значение сохраняется в виде PSCustomObject.

    $vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
    Set-AutomationVariable -Name "MyComplexVariable" -Value $vm

В следующем коде значение возвращается из переменной и используется для запуска виртуальной машины.

    $vmObject = Get-AutomationVariable -Name "MyComplexVariable"
    if ($vmObject.PowerState -eq 'Stopped') {
       Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
    }


#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>Задание и получение коллекции в переменной

В следующем примере кода показано, как использовать переменную с набором сложных значений в текстовом модуле Runbook. В этом примере имена нескольких виртуальных машин Azure возвращаются с помощью команды **Get-AzureVM** и затем сохраняются в существующую переменную автоматизации.  Как указано в разделе [Типы переменных](#variable-types), значение сохраняется в виде коллекции PSCustomObject.

    $vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

В следующем коде коллекция значений возвращается из переменной и используется для запуска виртуальной машины.

    $vmValues = Get-AutomationVariable -Name "MyComplexVariable"
    ForEach ($vmValue in $vmValues)
    {
       if ($vmValue.PowerState -eq 'Stopped') {
          Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
       }
    }
    
#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Задание и получение переменной в Python2
В следующем примере кода показано, как использовать и задать переменную, а также обрабатывать исключение для несуществующей переменной в модуле Runbook Python2.

    import automationassets
    from automationassets import AutomationAssetNotFound

    # get a variable
    value = automationassets.get_automation_variable("test-variable")
    print value

    # set a variable (value can be int/bool/string)
    automationassets.set_automation_variable("test-variable", True)
    automationassets.set_automation_variable("test-variable", 4)
    automationassets.set_automation_variable("test-variable", "test-string")

    # handle a non-existent variable exception
    try:
        value = automationassets.get_automation_variable("non-existing variable")
    except AutomationAssetNotFound:
        print "variable not found"


### <a name="graphical-runbook-samples"></a>Графические примеры для модуля Runbook

Чтобы добавить команды **Get-AutomationVariable** и **Set-AutomationVariable** в графическом модуле Runbook, щелкните правой кнопкой мыши переменную в области библиотеки графического редактора и выберите необходимый процесс.

![Добавление переменной в полотно](media/automation-variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Задание значений в переменной
На следующем рисунке приведен пример добавления в переменную простого значения в графическом модуле Runbook. В этом примере имя одной виртуальной машины Azure возвращается с помощью команды **Get-AzureRmVM**, а имя компьютера сохраняется в существующую переменную службы автоматизации строкового типа.  Не имеет значения, [является ли ссылка конвейером или последовательностью](automation-graphical-authoring-intro.md#links-and-workflow) , так как ожидается только одно значение в выводе.

![Задание простой переменной](media/automation-variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о соединении действий в графической разработке см. в разделе [Использование связей при создании графических модулей](automation-graphical-authoring-intro.md#links-and-workflow).
* Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](automation-first-runbook-graphical.md). 

