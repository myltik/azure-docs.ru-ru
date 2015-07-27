<properties 
   pageTitle="Ресурсы учетных данных в службе автоматизации Azure"
   description="Ресурсы учетных данных службы автоматизации Azure содержат учетные данные безопасности, которые могут использоваться для аутентификации при доступе к ресурсам из Runbook. В этой статье описывается, как создать ресурсы учетных данных и использовать их в Runbook."
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
   ms.date="05/21/2015"
   ms.author="bwren" />

# Ресурсы учетных данных в службе автоматизации Azure

В ресурсе учетных данных службы автоматизации хранится объект [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential), содержащий учетные данные безопасности, например имя пользователя и пароль. Модули Runbook могут использовать командлеты, которые принимают объект PSCredential для аутентификации, или Runbook может извлечь имя пользователя и пароль объекта PSCredential, чтобы предоставить их каким-либо приложениям или службам, требующим аутентификации. Свойства для учетных данных безопасно хранятся в службе автоматизации Azure, и Runbook может обращаться к ним с помощью действия [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx).

>[AZURE.NOTE]К защищенным ресурсам в службе автоматизации Azure относятся учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Ключ шифруется главным сертификатом и хранится в службе автоматизации Azure. Перед сохранением защищенного ресурса ключ учетной записи службы автоматизации дешифруется с помощью главного сертификата и используется для шифрования ресурса.

## Командлеты Windows PowerShell

Командлеты, представленные в следующей таблице, используются для создания ресурсов учетных данных службы автоматизации и управления ими с помощью Windows PowerShell. Они входят в состав [модуля Azure PowerShell](../powershell-install-configure.md), доступного в модулях Runbook службы автоматизации.

|Командлеты|Описание|
|:---|:---|
|[Get-AzureAutomationCredential](http://msdn.microsoft.com/library/dn913781.aspx)|Извлекает сведения о ресурсе учетных данных. Сами учетные данные можно извлечь только с помощью действия **Get-AutomationCredential**.|
|[New-AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|Создает новые учетные данные службы автоматизации.|
|[Remove- AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|Удаляет учетные данные службы автоматизации.|
|[Set- AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|Задает свойства для существующих учетных данных службы автоматизации.|

## Действия Runbook

Действия в следующей таблице используются для доступа к учетным данным в модуле Runbook.

|Действия|Описание|
|:---|:---|
|Get-AutomationPSCredential|Возвращает учетные данные для использования в Runbook. Возвращает объект [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential).|

>[AZURE.NOTE]Не следует использовать переменные в параметре –Name в Get-AutomationPSCredential, так как это может усложнить обнаружение зависимостей между модулями Runbook и ресурсами учетных данных во время разработки.

## Создание новых учетных данных


### Создание новой переменной на портале Azure

1. Из учетной записи службы автоматизации щелкните **Активы** в верхней части окна.
1. Нажмите кнопку **Добавить параметр** в нижней части окна.
1. Щелкните **Добавить учетные данные**.
2. В раскрывающемся списке **Тип учетных данных** выберите **Учетные данные Windows PowerShell**.
1. Завершите работу мастера и установите флажок, чтобы сохранить новые учетные данные.


### Создание новых учетных данных на портале предварительной версии Azure

1. Из учетной записи службы автоматизации щелкните элемент **Активы**, чтобы открыть колонку **Активы**.
1. Щелкните элемент **Учетные данные**, чтобы открыть колонку **Учетные данные**.
1. Щелкните **Добавить учетные данные** в верхней части колонки.
1. Заполните форму и нажмите кнопку **Создать** для сохранения новых учетных данных.


### Создание новых учетных данных PowerShell с помощью Windows PowerShell

Команды, приведенные ниже в примере, демонстрируют создание новых учетных данных службы автоматизации. Сначала создается объект PSCredential с именем и паролем, а затем он используется для создания ресурса учетных данных. Кроме того, можно использовать командлет **Get-Credential**, чтобы отобразить запрос имени и пароля.

	$user = "MyDomain\MyUser"
	$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
	$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
	New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred

## Использование учетных данных PowerShell в Runbook

Для получения ресурса учетных данных из Runbook используется действие **Get-AutomationPSCredential**. Оно возвращает [объект PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx), который можно использовать в действии или командлете, требующем параметр PSCredential. Можно также получить свойства объекта учетных данных, чтобы использовать их по отдельности. Объект имеет свойства для имени пользователя и надежного пароля. Также можно использовать метод **GetNetworkCredential** для возврата объекта [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx), предоставляющего небезопасную версию пароля.

### Пример текстового Runbook

Команды в приведенном ниже примере демонстрируют использование учетных данных PowerShell в Runbook. В этом примере извлекаются учетные данные, и имя пользователя и пароль из них присваиваются переменным.

	$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
	$userName = $myCredential.UserName
	$securePassword = $myCredential.Password
	$password = $myCredential.GetNetworkCredential().Password


### Пример графического Runbook

Можно добавить действие **Get-AutomationPSCredential** в графический Runbook, щелкнув правой кнопкой мыши учетные данные в области «Библиотека» графического редактора и выбрав пункт **Добавить на холст**.


![Добавление учетных данных на холст](media/automation-credentials/credential-add-canvas.png)

На следующем рисунке показан пример использования учетных данных в графическом Runbook. В этом случае они используются для аутентификации Runbook для доступа к ресурсам Azure, как описано в разделе [Настройка аутентификации для ресурсов Azure](#automation-configuring.md). Первое действие получает учетные данные с доступом к подписке Azure. Затем действие **Add-AzureAccount** использует эти учетные данные для аутентификации любых последующих действий. Здесь используется [конвейерная связь](automation-graphical-authoring-intro.md#links-and-workflow), так как **Get-AutomationPSCredential** ожидает один объект.

![Добавление учетных данных на холст](media/automation-credentials/get-credential.png)


## Связанные статьи

- [Использование связей при создании графических модулей](automation-graphical-authoring-intro.md#links-and-workflow)

 

<!---HONumber=July15_HO3-->