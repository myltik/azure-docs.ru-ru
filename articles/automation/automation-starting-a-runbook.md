---
title: Запуск модуля Runbook в службе автоматизации Azure
description: Кратко рассматриваются различные методы, которые позволяют запускать модуль Runbook в службе автоматизации Azure с помощью портала Azure и Windows PowerShell.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0bc414d42acd665e52f3f76037dffe225344b23d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195300"
---
# <a name="starting-a-runbook-in-azure-automation"></a>Запуск модуля Runbook в службе автоматизации Azure
Следующая таблица поможет определить метод запуска модуля runbook в службе автоматизации Azure, наиболее подходящий для конкретной ситуации. Данная статья содержит сведения о запуске модуля Runbook с помощью портала Azure и Windows PowerShell. Сведения об остальных методах приведены в другой документации, доступной по ссылкам, которые указаны ниже.

| **МЕТОД** | **ХАРАКТЕРИСТИКИ** |
| --- | --- |
| [портал Azure](#starting-a-runbook-with-the-azure-portal) |<li>Простейший способ с интерактивным пользовательским интерфейсом.<br> <li>Форма для предоставления значений простых параметров.<br> <li>Легко отслеживать состояние задания.<br> <li>Доступ с аутентификацией в Azure. |
| [Windows PowerShell](https://msdn.microsoft.com/library/dn690259.aspx) |<li>Вызов из командной строки с помощью командлетов Windows PowerShell.<br> <li>Может быть добавлено в автоматическое решение, состоящее из нескольких шагов.<br> <li>Запрос проходит проверку подлинности с помощью сертификата или по протоколу OAuth.<br> <li>Необходимо предоставить значения простых и сложных параметров.<br> <li>Отслеживание состояния задания.<br> <li>Для поддержки командлетов PowerShell необходим клиент. |
| [API-интерфейс в службе автоматизации Azure](https://msdn.microsoft.com/library/azure/mt662285.aspx) |<li>Наиболее гибкий и наиболее сложный метод.<br> <li>Вызов из любого пользовательского кода, который может выполнять HTTP-запросы.<br> <li>Запрос проходит проверку подлинности с помощью сертификата или по протоколу OAuth.<br> <li>Необходимо предоставить значения простых и сложных параметров. *При вызове модуля runbook Python с помощью API полезные данные JSON необходимо сериализовать.*<br> <li>Отслеживание состояния задания. |
| [Объекты Webhook](automation-webhooks.md) |<li>Запуск Runbook из одного HTTP-запроса.<br> <li>Проверка подлинности с помощью токена безопасности в URL-адресе.<br> <li>Клиент не может переопределять значения параметров, указанные при создании Webhook. Runbook может определить один параметр, который содержит сведения об HTTP-запросе.<br> <li>Отсутствует возможность отслеживать состояния задания через URL-адрес webhook. |
| [Ответ на оповещение Azure](../log-analytics/log-analytics-alerts.md) |<li>Запуск Runbook в ответ на оповещение Azure.<br> <li>Настройка веб-перехватчика для Runbook и ссылки на оповещение.<br> <li>Проверка подлинности с помощью токена безопасности в URL-адресе. |
| [Расписание](automation-schedules.md) |<li>Автоматический запуск Runbook по ежечасному, ежедневному, еженедельному или ежемесячному расписанию.<br> <li>Управление расписанием с помощью портала Azure, командлетов PowerShell или API-интерфейса Azure.<br> <li>Предоставление значений параметров, которые будут использованы в расписании. |
| [Из другого модуля Runbook](automation-child-runbooks.md) |<li>Использование одного модуля Runbook в качестве процесса в другом модуле Runbook.<br> <li>Подходит для функций, используемых в нескольких модулях Runbook.<br> <li>Предоставление значений параметров для дочернего модуля Runbook и использование выходных данных в родительском модуле Runbook. |

На следующем рисунке показан подробный пошаговый процесс жизненного цикла модуля Runbook. Он включает различные способы запуска модуля Runbook в службе автоматизации Azure, компоненты, необходимые для выполнения модулей Runbook для службы автоматизации Azure в гибридной рабочей роли, и взаимодействие между различными компонентами. Дополнительные сведения о выполнении модулей Runbook службы автоматизации Azure в центре обработки данных см. в статье о [гибридных рабочих ролях Runbook](automation-hybrid-runbook-worker.md).

![Архитектура Runbook](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="starting-a-runbook-with-the-azure-portal"></a>Запуск Runbook с помощью портала Azure
1. На портале Azure щелкните **Служба автоматизации**, а затем выберите имя учетной записи службы автоматизации.
2. В меню концентратора выберите **Модули Runbook**.
3. На странице **Модули Runbook** выберите модуль runbook и щелкните **Запустить**.
4. Если модуль runbook содержит параметры, вам будет предложено указать значения в текстовом поле для каждого параметра. Дополнительные сведения о параметрах см. в разделе [Параметры модуля Runbook](#Runbook-parameters) ниже.
5. На странице **Задание** можно просмотреть состояние задания runbook.

## <a name="starting-a-runbook-with-windows-powershell"></a>Запуск модуля Runbook с помощью Windows PowerShell
Чтобы запустить модуль Runbook с помощью Windows PowerShell, воспользуйтесь командлетом [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) . В следующем примере кода будет запущен модуль Runbook с именем Test-Runbook.

```
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Командлет Start-AzureRmAutomationRunbook возвращает объект задания, который позволяет отслеживать его состояние после запуска. Используйте командлет [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619440.aspx), чтобы определить состояние задания, и [Get-AzureRmAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx), чтобы получить его выходные данные. В следующем примере кода будет запущен модуль Runbook с именем Test-Runbook и после его завершения выводится результат.

```
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Если для модуля Runbook требуются параметры, то необходимо указать их в виде [хэш-таблицы](http://technet.microsoft.com/library/hh847780.aspx), где ключ в хэш-таблице совпадает с именем параметра, а значение — это значение параметра. В следующем примере показано, как запустить модуль Runbook с двумя строковыми параметрами FirstName и LastName, целочисленным параметром RepeatCount и логическим параметром Show. Дополнительные сведения о параметрах см. в разделе [Параметры модуля Runbook](#Runbook-parameters) ниже.

```
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Параметры модуля Runbook
При запуске модуля runbook с помощью портала управления Azure или Windows PowerShell инструкция отправляется через веб-службу автоматизации Azure. Эта служба не поддерживает параметры со сложными типами данных. Если необходимо указать значение для сложного параметра, его необходимо вызвать из другого модуля Runbook, как описано в разделе [Дочерние модули Runbook в службе автоматизации Azure](automation-child-runbooks.md).

Веб-служба автоматизации Azure предоставляет специальные функции для параметров, в которых используются определенные типы данных, как описано в следующих разделах.

### <a name="named-values"></a>Именованные значения
Если параметр имеет тип данных [object], используйте следующий формат JSON, чтобы передать в параметр список именованных значений: *{имя1:'значение1', имя2:'значение2', имя3:'значение3'}*. Значения должны быть простого типа. Модуль runbook получает параметр в виде объекта [PSCustomObject](https://msdn.microsoft.com/library/system.management.automation.pscustomobject%28v=vs.85%29.aspx), свойства которого соответствуют каждому именованному значению.

Рассмотрим следующий тестовый модуль Runbook, который принимает параметр user.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

Для параметра user может быть использован следующий текст.

```
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Результат должен быть таким:

```
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Массивы
Если параметр представляет собой массив, например [array] или [string[]], используйте следующий формат JSON, чтобы отправить его в виде списка значений: *[значение1, значение2, значение3]*. Значения должны быть простого типа.

Рассмотрим следующий тестовый модуль Runbook, который принимает параметр *user*.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

Для параметра user может быть использован следующий текст.

```
["Joe","Smith",2,true]
```

Результат должен быть таким:

```
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Учетные данные
Если параметр относится к типу данных **PSCredential**, можно предоставить имя [учетных данных](automation-credentials.md)в службе автоматизации Azure. Модуль runbook получит учетные данные с указанным именем.

Рассмотрим следующий тестовый модуль Runbook, который принимает параметр credential.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Следующий текст можно использовать для параметра user, если используется учетная запись *My Credential*.

```
My Credential
```

Если имя пользователя в параметре credential — *jsmith*, будет получен следующий результат:

```
jsmith
```

## <a name="next-steps"></a>Дополнительная информация
* В текущей статье описана общая архитектура Runbook для управления ресурсами в Azure и локальной среде с помощью гибридной рабочей роли Runbook. Дополнительные сведения о выполнении модулей Runbook службы автоматизации Azure в центре обработки данных см. в статье о [гибридных рабочих ролях Runbook](automation-hybrid-runbook-worker.md).
* Чтобы узнать больше о создании модульных Runbook, используемых другими модулями Runbook для выполнения конкретных или общих функций, ознакомьтесь с [дочерними модулями Runbook](automation-child-runbooks.md).

