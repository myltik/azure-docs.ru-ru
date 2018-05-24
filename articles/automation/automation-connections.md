---
title: Ресурсы подключений в службе автоматизации Azure
description: Ресурсы-контейнеры подключений в службе автоматизации Azure содержат данные, необходимые для подключения к внешней службе или приложению из модуля Runbook либо конфигурации DSC. В статье подробно рассматриваются подключения и работа с ними как в текстовых, так и в графических модулях.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ee866248ae7f0c1f1c49c449b777c2b68d884c5b
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
---
# <a name="connection-assets-in-azure-automation"></a>Ресурсы подключений в службе автоматизации Azure

Ресурс-контейнер подключения службы автоматизации содержит информацию, необходимую для подключения к внешней службе, приложению из модуля Runbook или конфигурации DSC. Это может включать данные, необходимые для аутентификации, такие как имя пользователя и пароль, а также информацию о подключении, например URL-адрес или порт. Вместо создания нескольких переменных значение подключения хранит в одном ресурсе все свойства для подключения к определенному приложению. Пользователь может изменять значения для подключения в одном месте, а передать имя подключения в модуль Runbook или конфигурацию DSC можно в одном параметре. К свойствам подключения можно получить доступ в модуле Runbook или конфигурации DSC с помощью действия **Get-AutomationConnection** . 

При создании подключения необходимо указать его *тип*. Тип подключения — это шаблон, определяющий набор его свойств. Подключение определяет значения для каждого свойства, определенного в его типе. Типы подключения в службе автоматизации Azure добавляются в модули интеграции или создаются с помощью [API службы автоматизации](http://msdn.microsoft.com/library/azure/mt163818.aspx), если модуль интеграции содержит тип подключения и импортируется в учетную запись службы автоматизации. В противном случае следует создать файл метаданных и указать в нем тип подключения для службы автоматизации.  Дополнительные сведения по этому вопросу см. в статье о [модулях интеграции](automation-integration-modules.md).  

>[!NOTE]
>Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Этот ключ хранится в Key Vault. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса.

## <a name="windows-powershell-cmdlets"></a>Командлеты Windows PowerShell

Командлеты, представленные в следующей таблице, используются для создания подключений и управления ими с помощью Windows PowerShell в службе автоматизации Azure. Они входят в состав [модуля Azure PowerShell](/powershell/azure/overview) , доступного в модулях Runbook и конфигурациях DSC службы автоматизации.

|Командлет|ОПИСАНИЕ|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Извлекает подключение. Содержит хэш-таблицу со значениями полей подключения.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Создает новое подключение.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Удаляет существующее подключение.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Задает значение определенного поля для существующего подключения.|

## <a name="activities"></a>Действия

Действия в следующей таблице используются для доступа к подключениям в модуле Runbook или конфигурации DSC.

|Действия|ОПИСАНИЕ|
|---|---|
|[Get-AutomationConnection](/powershell/module/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Получает подключение для использования. Возвращает хэш-таблицу со свойствами подключения.|

>[!NOTE] 
>Не следует использовать переменные в параметре –Name для командлета **Get-AutomationConnection**, так как это может усложнить обнаружение зависимостей между модулями Runbook или конфигурациями DSC и ресурсами подключений во время разработки.

 
## <a name="python2-functions"></a>Функции Python2 
Функция, приведенная в следующей таблице, используется для доступа к подключениям в модуле Runbook Python2. 

| Функция | ОПИСАНИЕ | 
|:---|:---| 
| automationassets.get_automation_connection | Извлекает подключение. Возвращает словарь со свойствами подключения. | 

> [!NOTE] 
> Для доступа к функциям ресурсов, необходимо импортировать модуль automationassets в верхнюю часть модуля Runbook Python.

## <a name="creating-a-new-connection"></a>Создание нового подключения

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Создание нового подключения на портале Azure

1. Из учетной записи службы автоматизации щелкните **Ресурсы**, чтобы открыть колонку **Ресурсы**.
2. Щелкните элемент **Подключения**, чтобы открыть колонку **Подключения**.
3. Щелкните **Добавить подключение** в верхней части колонки.
4. В раскрывающемся списке **Тип** выберите тип подключения, которое необходимо создать. Форма представит свойства для этого типа.
5. Заполните форму и нажмите кнопку **Создать** для сохранения нового подключения.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Создание нового подключения с помощью Windows PowerShell

Создайте новое подключение с помощью командлета Windows PowerShell [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection). У этого командлета есть параметр **ConnectionFieldValues** , который ожидает [хэш-таблицу](http://technet.microsoft.com/library/hh847780.aspx) , задающую значения для каждого свойства, определенного типом подключения.

Если вы знаете, как в службе автоматизации используется [учетная запись запуска от имени](automation-sec-configure-azure-runas-account.md) для аутентификации модулей Runbook с помощью субъекта-службы, то это поможет вам понять сценарий PowerShell, который можно использовать вместо учетной записи запуска от имени. Приведенные ниже примеры команд создают новый ресурс подключения.  

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues 
```

С помощью этого скрипта вы сможете создать ресурс подключения, так как при создании учетной записи автоматизации она по умолчанию автоматически включает несколько глобальных модулей и тип подключения **AzurServicePrincipal**, которые нужны для создания ресурса подключения **AzureRunAsConnection**.  Важно помнить о том, что при попытке использовать другой метод аутентификации вы не сможете создать ресурс подключения для подключения к службе или приложению, так как другие типы подключения не определены в учетной записи службы автоматизации.  Дополнительные сведения о создании собственных типов подключения и использовании модулей из [коллекции PowerShell](https://www.powershellgallery.com) есть в статье [Модули интеграции службы автоматизации Azure](automation-integration-modules.md).
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Использование подключения в модуле Runbook или конфигурации DSC

Для получения подключения в модуле Runbook или конфигурации DSC используется командлет **Get-AutomationConnection** .  Вы не можете использовать действие [Get-AzureRmAutomationConnection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.automation/v1.0.12/Get-AzureRmAutomationConnection?redirectedfrom=msdn).  Это действие получает значения различных полей в подключении и возвращает их в виде [хэш-таблицы](http://go.microsoft.com/fwlink/?LinkID=324844), которую затем можно использовать с помощью соответствующих команд в модуле Runbook или конфигурации DSC.

### <a name="textual-runbook-sample"></a>Пример текстового Runbook

Ниже представлены примеры команд, которые используют упомянутую выше учетную запись запуска от имени для аутентификации при обращении из Runbook к ресурсам Azure Resource Manager.  Здесь используются не учетные данные, а ресурс подключения, представляющий учетную запись запуска от имени, которая ссылается на субъект-службу на основе сертификатов.  

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection 
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

### <a name="graphical-runbook-samples"></a>Графические примеры для модуля Runbook

Можно добавить действие **Get-AutomationConnection** в графический Runbook, щелкнув правой кнопкой мыши подключение в области "Библиотека" графического редактора и выбрав пункт **Добавить на холст**.

![](media/automation-connections/connection-add-canvas.png)

На следующем рисунке показан пример использования подключения в графическом Runbook.  Это тот же пример, который представлен выше. Он выполняет аутентификацию с помощью учетной записи запуска от имени с текстовым Runbook.  В этом примере используется **константа**, установленная для действия **получения подключения запуска от имени**, в котором объект подключения используется для аутентификации.  Здесь используется [конвейерная связь](automation-graphical-authoring-intro.md#links-and-workflow), так как набор параметров ServicePrincipalCertificate ожидает получить один объект.

![](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Пример модуля Runbook Python2
В следующем примере показано, как выполнить аутентификацию с помощью подключения запуска от имени в модуле Runbook Python2.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
  """ Returns credentials to authenticate against Azure resoruce manager """
  from OpenSSL import crypto
  from msrestazure import azure_active_directory
  import adal

  # Get the Azure Automation Run As service principal certificate
  cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
  pks12_cert = crypto.load_pkcs12(cert)
  pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

  # Get Run As connection information for the Azure Automation service principal
  application_id = runas_connection["ApplicationId"]
  thumbprint = runas_connection["CertificateThumbprint"]
  tenant_id = runas_connection["TenantId"]

  # Authenticate with service principal certificate
  resource = "https://management.core.windows.net/"
  authority_url = ("https://login.microsoftonline.com/" + tenant_id)
  context = adal.AuthenticationContext(authority_url)
  return azure_active_directory.AdalAuthentication(
    lambda: context.acquire_token_with_client_certificate(
      resource,
      application_id,
      pem_pkey,
      thumbprint)
  )

# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Дополнительная информация

- Изучите, как использовать [связи в графическом редакторе](automation-graphical-authoring-intro.md#links-and-workflow), которые позволяют направлять и контролировать поток логики в Runbook.  

- В статье [Модули интеграции службы автоматизации Azure](automation-integration-modules.md) описывается использование модулей PowerShell в службе автоматизации Azure и рекомендации по созданию модулей PowerShell в качестве модулей интеграции для службы автоматизации Azure.  
