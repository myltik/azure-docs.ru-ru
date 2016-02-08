<properties 
   pageTitle="Запуск модуля Runbook в службе автоматизации Azure | Microsoft Azure"
   description="Кратко рассматриваются различные методы, которые позволяют запускать модуль Runbook в службе автоматизации Azure с помощью портала Azure и Windows PowerShell."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/19/2016"
   ms.author="bwren;sngun"/>

# Запуск модуля Runbook в службе автоматизации Azure

Следующая таблица поможет определить метод для запуска модуля Runbook в службе автоматизации Azure, наиболее подходящий для конкретной ситуации. Данная статья содержит сведения о запуске модуля Runbook с помощью портала Azure и Windows PowerShell. Сведения об остальных методах приведены в другой документации, доступной по ссылкам, которые указаны ниже.

<table>
 <tr>
  <td>МЕТОД</td>
  <td>ХАРАКТЕРИСТИКИ</td>
 </tr>
 <tr>           
  <td><a href="#starting-a-runbook-with-the-azure-portal">Портал Azure</a></td>
  <td>
   <ul>
    <li>Простейший способ с интерактивным пользовательским интерфейсом.</li>
    <li>Форма для предоставления значений простых параметров.</li>
    <li>Легко отслеживать состояние задания.</li>
    <li>Доступ с проверкой подлинности в Azure.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="https://msdn.microsoft.com/library/dn690259.aspx">Windows PowerShell</a></td>
  <td>
   <ul>
     <li>Вызов из командной строки с помощью командлетов Windows PowerShell.</li>
     <li>Может быть добавлено в автоматическое решение, состоящее из нескольких шагов.</li>
     <li>Запрос проходит проверку подлинности с помощью сертификата или по протоколу OAuth.</li>
     <li>Необходимо предоставить значения простых и сложных параметров.</li>
     <li>Отслеживание состояния заданий.</li>
     <li>Клиентское приложение должно поддерживать командлеты PowerShell.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://msdn.microsoft.com/library/azure/mt163849.aspx">API-интерфейс в службе автоматизации Azure</a></td>
  <td>
   <ul>
    <li>Наиболее гибкий и наиболее сложный метод.</li>
    <li>Вызов из любого пользовательского кода, который может выполнять HTTP-запросы.</li>
    <li>Запрос проходит проверку подлинности с помощью сертификата или по протоколу OAuth.</li>
    <li>Необходимо предоставить значения простых и сложных параметров.</li>
    <li>Отслеживание состояния заданий.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">Webhook</a></td>
  <td>
   <ul>
    <li>Запуск Runbook из одного HTTP-запроса.</li>
    <li>Проверка подлинности с помощью токена безопасности в URL-адресе.</li>
    <li>Клиент не может переопределять значения параметров, указанные при создании Webhook. Runbook может определить один параметр, который содержит сведения об HTTP-запросе.</li>
    <li>Отсутствует возможность отслеживать состояния задания через URL-адрес Webhook.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">Ответ на оповещение Azure</a></td>
  <td>
   <ul>
    <li>Запуск Runbook в ответ на оповещение Azure.</li>
    <li>Настройка веб-перехватчика для Runbook и ссылки на оповещение.</li>
    <li>Проверка подлинности с помощью токена безопасности в URL-адресе.</li>
    <li>В настоящее время поддерживает оповещения только для метрик.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-scheduling-a-runbook">Расписание</a></td>
  <td>
   <ul>
    <li>Автоматический запуск Runbook по ежечасному, ежедневному или еженедельному расписанию.</li>
    <li>Управление расписанием с помощью портала Azure, командлетов PowerShell или API-интерфейса Azure.</li>
    <li>Необходимо предоставить значения параметров, которые будут использованы в расписании.</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-child-runbooks/">Из другого модуля Runbook</a></td>
  <td>
   <ul>
    <li>Использование одного модуля Runbook в качестве процесса в другом модуле Runbook.</li>
    <li>Подходит для функций, используемых в нескольких модулях Runbook.</li>
    <li>Необходимо предоставить значения для дочернего модуля Runbook и использовать вывод в родительском модуле Runbook.</li>
   </ul>
  </td>
 </tr>
</table>
<br>


На следующем рисунке показаны подробные пошаговые инструкции в жизненном цикле Runbook. Он включает разные способы запуска модуля Runbook в службе автоматизации Azure, компоненты, необходимые локальному компьютеру для выполнения модулей Runbook службы автоматизации Azure, и взаимодействие между различными компонентами. Дополнительные сведения о выполнении модулей Runbook службы автоматизации Azure в центре обработки данных см. в статье [Гибридные компоненты Runbook Worker в службе автоматизации Azure](automation-hybrid-runbook-worker.md).

![Архитектура Runbook](media/automation-starting-runbook/runbooks-architecture.png)

## Запуск Runbook с помощью портала Azure

1. На портале Azure щелкните **Служба автоматизации** и затем имя учетной записи службы автоматизации.
1. Выберите вкладку **Модули Runbook**.
1. Выберите модуль Runbook и нажмите кнопку **Пуск**.
1. Если модуль Runbook имеет параметры, будет предложено предоставить значения в текстовом поле для каждого параметра. Дополнительные сведения о параметрах см. в разделе [Параметры модуля Runbook](#Runbook-parameters) ниже.
1. Чтобы просмотреть состояние задания Runbook, щелкните **Просмотр задания** рядом с сообщением Runbook **Пуск** или перейдите на вкладку **Задания** для модуля Runbook.

## Запуск модуля Runbook с помощью предварительной версии портала Azure

1. Из учетной записи автоматизации щелкните **Модули Runbook**, чтобы открыть колонку **Модули Runbook**.
1. Выберите модуль Runbook, чтобы открыть колонку **Модуль Runbook**.
2. Нажмите **Запуск**.
1. Если модуль не имеет параметров, нужно будет подтвердить его запуск. Если у модуля Runbook есть параметры, откроется область **Запуск модуля Runbook**, в которой нужно указать значения параметров. Дополнительные сведения о параметрах см. в разделе [Параметры модуля Runbook](#Runbook-parameters) ниже.
3. Открывается область **Задание**, в которой можно отслеживать состояние задания.


## Запуск модуля Runbook с помощью Windows PowerShell

Чтобы запустить модуль Runbook с помощью Windows PowerShell, воспользуйтесь командлетом [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx). В следующем примере кода будет запущен модуль Runbook с именем Test-Runbook.

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

Командлет Start-AzureAutomationRunbook возвращает объект задания, который позволяет отслеживать его состояние после запуска. Используйте командлеты [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx), чтобы определить состояние задания, и [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/azure/dn690268.aspx), чтобы получить его вывод. В следующем примере кода будет запущен модуль Runbook с именем Test-Runbook и после его завершения выводится результат.

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

Если для модуля Runbook требуются параметры, то необходимо указать их в виде [hashtable](http://technet.microsoft.com/library/hh847780.aspx), где ключ в хэш-таблице совпадает с именем параметра, а значение — это значение параметра. В следующем примере показано, как запустить модуль Runbook с двумя строковыми параметрами FirstName и LastName, целочисленным параметром RepeatCount и логическим параметром Show. Дополнительные сведения о параметрах см. в разделе [Параметры Runbook](#Runbook-parameters) ниже.

	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" –Parameters $params

## Параметры модуля Runbook

При запуске модуля runbook с помощью портала управления Azure или Windows PowerShell инструкция отправляется через веб-службы автоматизации Azure. Эта служба не поддерживает параметры со сложными типами данных. Если необходимо указать значение для сложного параметра, его необходимо вызвать из другого модуля Runbook, как описано в разделе [Дочерние модули Runbook в службе автоматизации Azure](automation-child-runbooks.md).

Веб-службы автоматизации Azure предоставляют специальные функции для параметров, которые используют определенные типы данных, как описано в следующих разделах.

### Именованные значения

Если параметр имеет тип данных [объект], используйте следующий формат JSON, чтобы отправить его в виде списка именованных значений: *{"имя1":значение1,"имя2":значение2, "имя3":значение3}*. Значения должны быть простого типа. Модуль Runbook получает параметр в виде объекта [PSCustomObject] (http://msdn.microsoft.com/library/azure/system.management.automation.pscustomobject(v=vs.85).aspx), у которого свойства соответствуют каждому именованному значению.

Рассмотрим следующий тестовый модуль Runbook, который принимает параметр user.

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

Для параметра user может быть использован следующий текст.

	{"FirstName":"Joe","LastName":"Smith","RepeatCount":2,"Show":true}

Результат должен быть следующим.

	Joe
	Smith
	Joe
	Smith

### Массивы

Если параметр представляет собой [массив] или [строку], используйте следующий формат JSON, чтобы отправить его в виде списка значений: *[значение1,значение2,значение3]*. Значения должны быть простого типа.

Рассмотрим следующий тестовый модуль Runbook, который принимает параметр *user*.

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

Для параметра user может быть использован следующий текст.

	["Joe","Smith",2,true]

Результат должен быть следующим.

	Joe
	Smith
	Joe
	Smith

### Учетные данные

Если параметр относится к типу данных **PSCredential**, можно предоставить имя [учетных данных](automation-credentials.md) в службе автоматизации Azure. Модуль Runbook получит учетную запись с указанным именем.

Рассмотрим следующий тестовый модуль Runbook, который принимает параметр credential.

	Workflow Test-Parameters
	{
	   param ( 
	      [Parameter(Mandatory=$true)][PSCredential]$credential
	   )
	   $credential.UserName
	}

Следующий текст можно использовать для параметра user, если используется учетная запись *My Credential*.

	My Credential

Если имя пользователя в параметре credential — *jsmith*, будет получен следующий результат.

	jsmith

## Дальнейшие действия

- Архитектура Runbook в текущей статье содержит подробное описание о гибридных модулей Runbook. Дополнительную информацию см. в статье [Дочерние модули Runbook в службе автоматизации Azure](automation-child-runbooks.md). 

<!---HONumber=AcomDC_0128_2016-->