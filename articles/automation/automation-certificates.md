---
title: Сертификация активов в службе автоматизации Azure
description: Сертификаты можно безопасно сохранить в службе автоматизации Azure, чтобы к ним могли обращаться модули Runbook или конфигурации DSC для прохождения аутентификации при доступе к ресурсам Azure и ресурсам сторонних производителей.  В этой статье подробно рассматриваются сертификаты и работа с ними при создании текстовых и графических модулей.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 70d5c777f98a1e09125c7ef42ba597009bde084b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194344"
---
# <a name="certificate-assets-in-azure-automation"></a>Сертификация активов в службе автоматизации Azure

Сертификаты можно безопасно сохранить в службе автоматизации Azure, чтобы к ним могли обращаться модули Runbook или конфигурации DSC с помощью действия **Get-AzureRmAutomationCertificate** для ресурсов Azure Resource Manager. Эта возможность позволяет создавать модули runbook и конфигурации DSC, которые используют сертификаты для аутентификации или добавляют их в ресурсы Azure либо ресурсы сторонних производителей.

>[!NOTE]
>Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Этот ключ хранится в Key Vault. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса.

## <a name="azurerm-powershell-cmdlets"></a>Командлеты PowerShell для AzureRM
Для AzureRM командлеты, представленные в следующей таблице, используются для создания ресурсов учетных данных службы автоматизации и управления ими с помощью Windows PowerShell. Они входят в состав [модуля AzureRM.Automation](/powershell/azure/overview), доступного в модулях Runbook и конфигурациях DSC службы автоматизации.

|Командлеты|ОПИСАНИЕ|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Извлекает сведения о сертификате для использования в модуле Runbook или в конфигурации DSC. Сам сертификат можно извлечь только с помощью действия Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Создает сертификат в службе автоматизации Azure.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Удаляет сертификат из службы автоматизации Azure.|Создает сертификат в службе автоматизации Azure.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Задает свойства для существующего сертификата, включая отправку файла сертификата и задание пароля для PFX-файла.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Отправляет сертификат службы в заданную облачную службу.|

## <a name="activities"></a>Действия
Действия в следующей таблице используются для доступа к сертификатам в модуле runbook и конфигурации DSC.

| Действия | ОПИСАНИЕ |
|:---|:---|
|Get-AutomationCertificate|Получает сертификат для использования в модуле Runbook или в конфигурации DSC. Возвращает объект [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate2.aspx).|

> [!NOTE] 
> Не используйте переменные в параметре -Name действия **Get-AutomationCertificate** в runbook или конфигурации DSC, так как это усложняет определение зависимостей между модулями runbook или конфигурацией DSC и переменными службы автоматизации во время разработки.

## <a name="python2-functions"></a>Функции Python2

Функция, приведенная в следующей таблице, используется для доступа к сертификатам в runbook Python2.

| Функция | ОПИСАНИЕ |
|:---|:---|
| automationassets.get_automation_certificate | Извлекает сведения о ресурсе сертификата. |

> [!NOTE]
> Для доступа к функциям ресурса необходимо импортировать модуль **automationassets** в начало runbook Python.

## <a name="creating-a-new-certificate"></a>Создание нового сертификата

При создании нового сертификата в службу автоматизации Azure передается CER- или PFX-файл. Если пометить сертификат как экспортируемый, его можно будет переместить из хранилища сертификатов службы автоматизации Azure. Если он не экспортируемый, то он будет использоваться только для подписи в модуле Runbook или конфигурации DSC. Службе автоматизации Azure требуется, чтобы у сертификата был поставщик: **Microsoft Enhanced RSA and AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Создание нового сертификата на портале Azure

1. В учетной записи службы автоматизации щелкните плитку **Ресурсы**, чтобы открыть колонку **Ресурсы**.
1. Щелкните плитку **Сертификаты**, чтобы открыть колонку **Сертификаты**.
1. Щелкните **Добавить сертификат** в верхней части колонки.
1. Введите имя сертификата в поле **Имя** .
1. Чтобы перейти к CER- или PFX-файлу, щелкните **Выбрать файл** в разделе **Отправка файла сертификата**. Если выбран PFX-файл, введите пароль и укажите, разрешен ли экспорт сертификата.
1. Щелкните **Создать** для сохранения нового ресурса сертификата.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Создание нового сертификата с помощью Windows PowerShell

В примере, приведенном ниже, демонстрируется, как создать сертификат службы автоматизации и пометить его как экспортируемый. Вследствие этого импортируется существующий PFX-файл.

```powershell-interactive
$certName = 'MyCertificate'
$certPath = '.\MyCert.pfx'
$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup
```

## <a name="using-a-certificate"></a>Использование сертификата

Чтобы использовать сертификат, примените действие **Get-AutomationCertificate**. Нельзя использовать командлет [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx), так как он возвращает сведения о ресурсе сертификата, но не сам сертификат.

### <a name="textual-runbook-sample"></a>Пример текстового Runbook

В следующем примере кода показано, как добавить сертификат из Runbook в облачную службу. В этом примере пароль извлекается из зашифрованной переменной службы автоматизации.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Пример графического Runbook

Вы можете добавить **Get-AutomationCertificate** в графический компонент Runbook, щелкнув правой кнопкой мыши сертификат в области "Библиотека" графического редактора и выбрав пункт **Добавить на холст**.

![Добавление сертификата на холст](media/automation-certificates/automation-certificate-add-to-canvas.png)

На следующем рисунке показан пример использования сертификата в графическом Runbook. Этот же пример был показан выше, он добавляет сертификат из текстового runbook в облачную службу.

![Пример графической разработки ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Пример Python2
В следующем примере показано, как получить доступ к сертификатам в модулях runbook Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert 
```

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о работе со связями, с помощью которых можно управлять логической последовательностью действий, выполняемых модулем Runbook, см. в [Связи и рабочий процесс](automation-graphical-authoring-intro.md#links-and-workflow). 
