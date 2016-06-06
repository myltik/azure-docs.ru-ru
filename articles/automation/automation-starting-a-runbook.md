<properties
   pageTitle="Запуск модуля Runbook в службе автоматизации Azure | Microsoft Azure"
   description="Кратко рассматриваются различные методы, которые позволяют запускать модуль Runbook в службе автоматизации Azure с помощью портала Azure и Windows PowerShell."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/23/2016"
   ms.author="magoedte;bwren"/>

# Запуск модуля Runbook в службе автоматизации Azure

Следующая таблица поможет определить метод для запуска модуля Runbook в службе автоматизации Azure, наиболее подходящий для конкретной ситуации. Данная статья содержит сведения о запуске модуля Runbook с помощью портала Azure и Windows PowerShell. Сведения об остальных методах приведены в другой документации, доступной по ссылкам, которые указаны ниже.

| **МЕТОД** | **ХАРАКТЕРИСТИКИ** |
|-------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Портал Azure](#starting-a-runbook-with-the-azure-portal) | <li>Простейший способ с интерактивным пользовательским интерфейсом.<br> <li>Форма для предоставления значений простых параметров.<br> <li>Простое отслеживание состояния задания.<br> <li>Доступ с аутентификацией в Azure. |
| [Windows PowerShell](https://msdn.microsoft.com/library/dn690259.aspx) | <li>Вызов из командной строки с помощью командлетов Windows PowerShell.<br> <li>Возможность добавления в автоматическое решение, состоящее из нескольких шагов.<br> <li>Запрос аутентифицируется с помощью сертификата или субъекта-пользователя либо субъекта-службы OAuth.<br> <li>Необходимо предоставить значения простых и сложных параметров.<br> <li>Отслеживание состояния заданий.<br> <li>Клиентское приложение должно поддерживать командлеты PowerShell. |
| [API-интерфейс в службе автоматизации Azure](https://msdn.microsoft.com/library/azure/mt662285.aspx) | <li>Наиболее гибкий и наиболее сложный метод.<br> <li>Вызов из любого пользовательского кода, который может выполнять HTTP-запросы.<br> <li>Запрос аутентифицируется с помощью сертификата или субъекта-пользователя либо субъекта-службы OAuth.<br> <li>Необходимо предоставить значения простых и сложных параметров.<br> <li>Отслеживание состояния заданий. |
| [Объекты Webhook](automation-webhooks.md) | <li>Запуск Runbook из одного HTTP-запроса.<br> <li>Аутентификация с помощью маркера безопасности в URL-адресе.<br> <li>Клиент не может переопределять значения параметров, указанные при создании веб-перехватчика. Runbook может определить один параметр, который содержит сведения об HTTP-запросе.<br> <li>Отсутствует возможность отслеживать состояния задания через URL-адрес веб-перехватчика. |
| [Ответ на оповещение Azure](automation-webhooks.md) | <li>Запуск Runbook в ответ на оповещение Azure.<br> <li>Настройка веб-перехватчика для Runbook и ссылки на оповещение.<br> <li>Аутентификация с помощью маркера безопасности в URL-адресе.<br> <li>Сейчас поддерживаются оповещения только для метрик. |
| [Расписание](automation-scheduling-a-runbook.md) | <li>Автоматический запуск Runbook по ежечасному, ежедневному или еженедельному расписанию.<br> <li>Управление расписанием с помощью портала Azure, командлетов PowerShell или API Azure.<br> <li>Необходимо предоставить значения параметров, которые будут использованы в расписании. |
| [Из другого модуля Runbook](automation-child-runbooks.md) | <li>Использование одного модуля Runbook в качестве процесса в другом модуле Runbook.<br> <li>Подходит для функций, используемых в нескольких модулях Runbook.<br> <li>Необходимо предоставить значения для дочернего модуля Runbook и использовать вывод в родительском модуле Runbook. |

На следующем рисунке показан подробный пошаговый процесс жизненного цикла модуля Runbook. Он включает различные способы запуска модуля Runbook в службе автоматизации Azure, компоненты, необходимые для выполнения модулей Runbook для службы автоматизации Azure в гибридной рабочей роли, и взаимодействие между различными компонентами. Дополнительные сведения о выполнении модулей Runbook службы автоматизации Azure в центре обработки данных см. в статье о [гибридных компонентах Runbook Worker](automation-hybrid-runbook-worker.md).

![Архитектура Runbook](media/automation-starting-runbook/runbooks-architecture.png)

## Запуск Runbook с помощью портала Azure

1.	На портале Azure щелкните **Служба автоматизации** и затем имя учетной записи службы автоматизации.
2.	Выберите вкладку **Модули Runbook**.
3.	Выберите модуль Runbook и нажмите кнопку **Пуск**.
4.	Если модуль Runbook имеет параметры, будет предложено предоставить значения в текстовом поле для каждого параметра. Дополнительные сведения о параметрах см. в разделе [Параметры модуля Runbook](#Runbook-parameters) ниже.
5.	Чтобы просмотреть состояние задания Runbook, щелкните **Просмотр задания** рядом с сообщением Runbook **Пуск** или перейдите на вкладку **Задания** для модуля Runbook.

## Запуск Runbook с помощью портала Azure

1.	Из учетной записи автоматизации щелкните **Модули Runbook**, чтобы открыть колонку **Модули Runbook**.
2.	Выберите модуль Runbook, чтобы открыть колонку **Модуль Runbook**.
3.	Нажмите **Запуск**.
4.	Если модуль не имеет параметров, нужно будет подтвердить его запуск. Если у модуля Runbook есть параметры, откроется область **Запуск модуля Runbook**, в которой нужно указать значения параметров. Дополнительные сведения о параметрах см. в разделе [Параметры модуля Runbook](#Runbook-parameters) ниже.
5.	Открывается область **Задание**, в которой можно отслеживать состояние задания.

## Запуск модуля Runbook с помощью Windows PowerShell

Чтобы запустить модуль Runbook с помощью Windows PowerShell, воспользуйтесь командлетом [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx). В следующем примере кода будет запущен модуль Runbook с именем Test-Runbook.

```
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Командлет Start-AzureRmAutomationRunbook возвращает объект задания, который позволяет отслеживать его состояние после запуска. Используйте командлеты [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619440.aspx), чтобы определить состояние задания, и [Get-AzureRmAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx), чтобы получить его вывод. В следующем примере кода будет запущен модуль Runbook с именем Test-Runbook и после его завершения выводится результат.

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

Если для модуля Runbook требуются параметры, то необходимо указать их в виде [hashtable](http://technet.microsoft.com/library/hh847780.aspx), где ключ в хэш-таблице совпадает с именем параметра, а значение — это значение параметра. В следующем примере показано, как запустить модуль Runbook с двумя строковыми параметрами FirstName и LastName, целочисленным параметром RepeatCount и логическим параметром Show. Дополнительные сведения о параметрах см. в разделе [Параметры Runbook](#Runbook-parameters) ниже.

```
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## Параметры модуля Runbook

При запуске модуля Runbook из портала управления Azure или Windows PowerShell инструкция отправляется через веб-службы автоматизации Azure. Эта служба не поддерживает параметры со сложными типами данных. Если необходимо указать значение для сложного параметра, его необходимо вызвать из другого модуля Runbook, как описано в разделе [Дочерние модули Runbook в службе автоматизации Azure](automation-child-runbooks.md).

Веб-службы автоматизации Azure предоставляют специальные функции для параметров, которые используют определенные типы данных, как описано в следующих разделах.

### Именованные значения

Если параметр имеет тип данных [объект], используйте следующий формат JSON, чтобы отправить его в виде списка именованных значений: *{"имя1":значение1,"имя2":значение2, "имя3":значение3}*. Значения должны быть простого типа. Модуль Runbook получает параметр в виде объекта [PSCustomObject] (https://msdn.microsoft.com/library/system.management.automation.pscustomobject(v=vs.85).aspx), у которого свойства соответствуют каждому именованному значению.

Рассмотрим следующий тестовый модуль Runbook, который принимает параметр user.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    if ($user.Show) {
        foreach ($i in 1..$user.RepeatCount) {
            $user.FirstName
            $user.LastName
        }
    }
}
```

Для параметра user может быть использован следующий текст.

```
{"FirstName":"Joe","LastName":"Smith","RepeatCount":2,"Show":true}
```

Результат должен быть следующим.

```
Joe
Smith
Joe
Smith
```

### Массивы

Если параметр представляет собой [массив] или [строку], используйте следующий формат JSON, чтобы отправить его в виде списка значений: *[значение1,значение2,значение3]*. Значения должны быть простого типа.

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

Результат должен быть следующим.

```
Joe
Smith
Joe
Smith
```

### Учетные данные

Если параметр относится к типу данных **PSCredential**, можно предоставить имя [учетных данных](automation-credentials.md) в службе автоматизации Azure. Модуль Runbook получит учетную запись с указанным именем.

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

Если имя пользователя в параметре credential — *jsmith*, будет получен следующий результат.

```
jsmith
```

## Дальнейшие действия

-	Архитектура Runbook в этой статье содержит подробное описание гибридных модулей Runbook. Дополнительные сведения см. в разделе [Дочерние модули Runbook в службе автоматизации Azure](automation-child-runbooks.md).

<!---HONumber=AcomDC_0525_2016-->