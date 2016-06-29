<properties pageTitle="Ресурсы-контейнеры переменных в службе автоматизации Azure | Microsoft Azure" description="Ресурсы-контейнеры переменных — это значения, которые доступны для всех модулей Runbook и конфигураций DSC в службе автоматизации Azure. В статье подробно рассматриваются переменные и работа с ними как в текстовых, так и в графических модулях." services="automation" documentationCenter="" authors="mgoedtel" manager="jwhit"" editor="tysonn" />
<tags  
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/24/2016"
   ms.author="magoedte;bwren" />

# Средства переменных в службе автоматизации Azure

Ресурсы-контейнеры переменных — это значения, которые доступны для всех модулей Runbook и конфигураций DSC в учетной записи службы автоматизации. Средства можно создавать, изменять или получать с помощью портала Azure, Windows PowerShell, из модуля Runbook или конфигурации DSC. Переменные автоматизации полезны в следующих случаях:

- совместное использование значения несколькими модулями Runbook или конфигурациями DSC;

- совместное использование значения несколькими заданиями из одного модуля Runbook или конфигурации DSC;

- управление значением из портала или из командной строки Windows PowerShell, используемой модулями Runbook или конфигурациями DSC.

Переменные службы автоматизации сохраняются, чтобы они были доступными после сбоя модуля Runbook или конфигурации DSC. Это также позволяет модулю Runbook задать значение, которое может быть использовано другим модулем или этим же модулем либо конфигурацией DSC при следующем запуске.

При создании переменной можно указать, что она должна храниться в зашифрованном виде. Если переменная зашифрована, она безопасно хранится в службе автоматизации Azure и ее значение не может быть получено с помощью командлета [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx), который входит в модуль Azure PowerShell. Единственный способ получить зашифрованное значение — использовать действие **Get-AutomationVariable** в модуле Runbook или конфигурации DSC.

>[AZURE.NOTE]Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Ключ шифруется главным сертификатом и хранится в службе автоматизации Azure. Прежде чем сохранять безопасное средство, ключ учетной записи в службе автоматизации дешифруется с помощью главного сертификата и используется для шифрования средства.

## Типы переменных

При создании переменной с помощью портала Azure выберите тип данных с помощью раскрывающегося списка, чтобы портал мог вывести соответствующий элемент управления для ввода значения переменной. Переменная не ограничивается этим типом данных, однако следует задать значение с помощью Windows PowerShell, если значение должно иметь другой тип. Если указать **не определено**, переменная получит значение **$null**, и значение необходимо указать с помощью командлета [Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx) или с помощью процесса **Set-AutomationVariable**. Невозможно создать или изменить значение для переменной сложного типа на портале, но можно указать значение любого типа с помощью Windows PowerShell. Сложные типы возвращаются в виде [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx).

Можно сохранить несколько значений в отдельной переменной, создав массив или хэш-таблицу и сохранив ее в переменную.

## Командлеты и рабочие процессы

Командлеты, представленные в следующей таблице, используются для создания переменных и управления ими с помощью Windows PowerShell в службе автоматизации Azure. Они входят в состав [модуля Azure PowerShell](../powershell-install-configure.md), доступного в модулях Runbook и конфигурации DSC службы автоматизации.

|Командлеты|Описание|
|:---|:---|
|[Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx)|Получает значение существующей переменной.|
|[New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx)|Создает новую переменную и устанавливает ее значение.|
|[Remove-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913775.aspx)|Удаляет существующую переменную.|
|[Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx)|Получает значение существующей переменной.|

Действия рабочего процесса в следующей таблице используются для доступа к переменным автоматизации в модуле Runbook. Они доступны для использования в модуле Runbook или конфигурации DSC и не поставляются вместе с модулем Azure PowerShell.

|Действия рабочего процесса|Описание|
|:---|:---|
|Get-AutomationVariable|Получает значение существующей переменной.|
|Set-AutomationVariable|Получает значение существующей переменной.|

>[AZURE.NOTE] Не следует использовать переменные в параметре –Name действия **Get-AutomationVariable** в модуле Runbook или конфигурации DSC, поскольку это усложняет определение зависимостей между модулями Runbook или конфигурацией DSC и переменными автоматизации во время разработки.

## Создание новой переменной автоматизации

### Создание новой переменной на портале Azure

1. Из учетной записи службы автоматизации щелкните **Активы** в верхней части окна.
1. Нажмите кнопку **Добавить параметр** в нижней части окна.
1. Щелкните **Добавить переменную**.
1. Завершите работу мастера и установите флаг, чтобы сохранить новую переменную.


### Создание новой переменной на портале Azure

1. Из учетной записи автоматизации щелкните **Средства**, чтобы открыть колонку **Средства**.
1. Щелкните **Переменные**, чтобы открыть колонку **Переменные**.
1. Щелкните **Добавить переменную** в верхней части колонки.
1. Заполните форму и нажмите кнопку **Создать** для сохранения новой переменной.


### Создание переменной с помощью Windows PowerShell

Командлет [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx) создает переменную и задает ее исходное значение. Значение можно получить с помощью [Get-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913772.aspx). Если значение простого типа, возвращается тот же тип. Если это сложный тип, возвращается **PSCustomObject**.

Приведенные ниже примеры команд демонстрируют создание переменной строкового типа и возврат ее значения.


	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' –Encrypted $false –Value 'My String'
	$string = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

Приведенные ниже примеры команд демонстрируют создание переменной сложного типа и возврат ее свойств. В данном случае используется объект виртуальной машины из **Get-AzureVM**.

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	New-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
	
	$vmValue = (Get-AzureAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
	$vmName = $vmValue.Name
	$vmIpAddress = $vmValue.IpAddress



## Использование переменной в модуле Runbook или конфигурации DSC

Используйте действие **Set-AutomationVariable**, чтобы задать значение переменной службы автоматизации в модуле Runbook, и действие **Get-AutomationVariable**, чтобы получить его. Не следует использовать командлеты **Set-AzureAutomationVariable** и **Get-AzureAutomationVariable** в модуле Runbook или конфигурации DSC, поскольку они менее эффективны, чем действия рабочего процесса. Также с помощью **Get-AzureAutomationVariable** нельзя получить значение безопасных переменных. Единственный способ создать переменную из модуля Runbook или конфигурации DSC — использовать командлет [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx).


### Текстовые примеры модуля Runbook

#### Установка и получение простого значения из переменной

Приведенные ниже примеры команд демонстрируют задание и получение переменной в текстовом модуле Runbook. В примере предполагается, что целочисленные переменные *NumberOfIterations* и *NumberOfRunnings* и строковая переменная *SampleMessage* уже созданы.

	$NumberOfIterations = Get-AutomationVariable -Name 'NumberOfIterations'
	$NumberOfRunnings = Get-AutomationVariable -Name 'NumberOfRunnings'
	$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
	
	Write-Output "Runbook has been run $NumberOfRunnings times."
	
	for ($i = 1; $i -le $NumberOfIterations; $i++) {
	   Write-Output "$i`: $SampleMessage"
	}
	Set-AutomationVariable –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)


#### Задание и получение сложного объекта в переменной

В следующем примере кода показано, как добавить в переменную сложное значение в текстовом модуле Runbook. В этом примере виртуальная машина Azure вызывается с помощью команды **Get-AzureVM** и сохраняется в существующую переменную автоматизации. Как указано в разделе [Типы переменных](#variable-types), значение сохраняется в виде PSCustomObject.

	$vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
	Set-AutomationVariable -Name "MyComplexVariable" -Value $vm


В следующем коде значение возвращается из переменной и используется для запуска виртуальной машины.

	$vmObject = Get-AutomationVariable -Name "MyComplexVariable"
	if ($vmObject.PowerState -eq 'Stopped') {
	   Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
	}


#### Задание и получение коллекции в переменной

В следующем примере кода показано, как использовать переменную с набором сложных значений в текстовом модуле Runbook. В этом примере имена нескольких виртуальных машин Azure возвращаются с помощью команды **Get-AzureVM** и затем сохраняются в существующую переменную автоматизации. Как указано в разделе [Типы переменных](#variable-types), значение сохраняется в виде коллекции PSCustomObject.

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

### Графические примеры для модуля Runbook

Чтобы добавить команды **Get-AutomationVariable** и **Set-AutomationVariable** в графическом модуле Runbook, щелкните правой кнопкой мыши переменную в области библиотеки графического редактора и выберите необходимый процесс.

![Добавление переменной в полотно](media/automation-variables/variable-add-canvas.png)

#### Задание значений в переменной

На следующем рисунке приведен пример добавления в переменную простого значения в графическом модуле Runbook. В этом примере имя одной виртуальной машины Azure возвращается с помощью команды **Get-AzureVM** и затем имя компьютера сохраняется в существующую переменную автоматизации строкового типа. Не имеет значения, [является ли ссылка конвейером или последовательностью](automation-graphical-authoring-intro.md#links-and-workflow), так как ожидается только одно значение в выводе.

![Задание простой переменной](media/automation-variables/set-simple-variable.png)

На следующем рисунке приведен пример добавления в переменную сложного значения в графическом модуле Runbook. Единственное отличие от предыдущего примера заключается в том, что не указывается **путь поля** для **вывода действия** **Set-AutomationVariable**, поэтому сохраняется объект, а не только его свойство. Как указано в разделе [Типы переменных](#variable-types), значение сохраняется в виде PSCustomObject.

![Задание сложной переменной](media/automation-variables/set-complex-variable.png)

На следующем рисунке показаны аналогичные действия, что и в предыдущем примере, однако при этом в переменную сохраняются несколько виртуальных машин. [Ссылку последовательности](automation-graphical-authoring-intro.md#links-and-workflow) необходимо использовать для того, чтобы действие **Set-AutomationVariable** получило весь набор виртуальных машин в виде одной коллекции. Если используется [ссылка конвейера](automation-graphical-authoring-intro.md#links-and-workflow), то действие **Set-AutomationVariable** срабатывает отдельно для каждого объекта, при этом в результате будет сохранено только имя последней виртуальной машины в коллекции. Как указано в разделе [Типы переменных](#variable-types), значение сохраняется в виде коллекции объектов PSCustomObject.

![Задание переменной сложной коллекции](media/automation-variables/set-complex-variable-collection.png)

#### Извлечение значений из переменной

На следующем рисунке приведен пример возврата из переменной простого значения в графическом модуле Runbook. Первое действие получает виртуальные машины, которые были сохранены в переменную в предыдущем примере. Ссылка должна быть [конвейером](automation-graphical-authoring-intro.md#links-and-workflow), чтобы действие **Start-AzureVM** было выполнено один раз для каждого объекта из действия **Get-AutomationVariable**. Функция работает одинаково, независимо от того, сохраняется в переменную один объект или несколько объектов. Действие **Start-AzureVM** использует свойства объекта PSCustomObject, который представляет каждую виртуальную машину.

![Получение сложной переменной](media/automation-variables/get-complex-variable.png)

На следующем рисунке показано, как фильтровать объекты, которые сохраняются в переменную в графическом модуле Runbook. В предыдущем примере в ссылку добавляется [условие](automation-graphical-authoring-intro.md#links-and-workflow), чтобы отфильтровать только те виртуальные машины, которые были остановлены при задании переменной.

![Получение сложной переменной с фильтром](media/automation-variables/get-complex-variable-filter.png)


## Дальнейшие действия

- Дополнительные сведения о соединении действий в графической разработке см. в разделе [Использование связей при создании графических модулей](automation-graphical-authoring-intro.md#links-and-workflow).
- Сведения о том, как начать работу с графическими модулями Runbook, см. в статье [Первый графический Runbook](automation-first-runbook-graphical.md). 

<!---HONumber=AcomDC_0615_2016-->