---
title: "Ресурсы-контейнеры сертификатов в службе автоматизации Azure | Документация Майкрософт"
description: "Сертификаты можно безопасно сохранить в службе автоматизации Azure, чтобы к ним могли обращаться модули Runbook или конфигурации DSC для прохождения аутентификации при доступе к ресурсам Azure и сторонних производителей.  В этой статье подробно рассматриваются сертификаты и работа с ними при создании текстовых и графических модулей."
services: automation
documentationcenter: 
author: mgoedtel
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 89e5486f3302098f3a1d49e4390ec5b21617d778
ms.openlocfilehash: fd1737a420c132dace9307436bfea98a9bde94a0

---

# <a name="certificate-assets-in-azure-automation"></a>Сертификация активов в службе автоматизации Azure

Сертификаты можно безопасно сохранить в службе автоматизации Azure, чтобы к ним могли обращаться модули Runbook или конфигурации DSC с помощью действия **Get-AzureRmAutomationRmCertificate** для ресурсов Azure Resource Manager. Это позволяет создавать модули Runbook и конфигурации DSC, которые используют сертификаты для проверки подлинности или добавляют их в ресурсы Azure либо сторонних производителей.

> [!NOTE] 
> Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Ключ шифруется главным сертификатом и хранится в службе автоматизации Azure. Перед сохранением защищенного ресурса ключ учетной записи службы автоматизации дешифруется с помощью главного сертификата и используется для шифрования ресурса.
> 

## <a name="windows-powershell-cmdlets"></a>Командлеты Windows PowerShell

Командлеты, представленные в следующей таблице, используются для создания ресурсов сертификата службы автоматизации и управления ими с помощью Windows PowerShell. Они входят в состав [модуля Azure PowerShell](../powershell-install-configure.md) , доступного в модулях Runbook и конфигурациях DSC службы автоматизации.

|Командлеты|Описание|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx)|Извлекает сведения о сертификате для использования в модуле Runbook или в конфигурации DSC. Сам сертификат можно извлечь только с помощью действия Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603604.aspx)|Создает сертификат в службе автоматизации Azure.|
[Remove-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603529.aspx)|Удаляет сертификат из службы автоматизации Azure.|Создает сертификат в службе автоматизации Azure.
|[Set-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603760.aspx)|Задает свойства для существующего сертификата, включая отправку файла сертификата и задание пароля для PFX-файла.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Отправляет сертификат службы в заданную облачную службу.|


## <a name="creating-a-new-certificate"></a>Создание нового сертификата

При создании нового сертификата в службу автоматизации Azure передается CER- или PFX-файл. Если пометить сертификат как экспортируемый, его можно будет переместить из хранилища сертификатов службы автоматизации Azure. Если он не экспортируемый, то он будет использоваться только для подписи в модуле Runbook или конфигурации DSC.


### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Создание нового сертификата на портале Azure

1. В учетной записи службы автоматизации щелкните плитку **Ресурсы**, чтобы открыть колонку **Ресурсы**.
1. Щелкните плитку **Сертификаты**, чтобы открыть колонку **Сертификаты**.
1. Щелкните **Добавить сертификат** в верхней части колонки.
2. Введите имя сертификата в поле **Имя** .
2. Щелкните **Выбрать файл** в разделе **Отправка файла сертификата** и перейдите к CER- или PFX-файлу.  Если выбран PFX-файл, введите пароль и укажите, разрешен ли экспорт сертификата.
1. Щелкните **Создать** для сохранения нового ресурса сертификата.


### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Создание нового сертификата с помощью Windows PowerShell

В примере, приведенном ниже, демонстрируется, как создать сертификат службы автоматизации и пометить его как экспортируемый. Вследствие этого импортируется существующий PFX-файл.

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
    $ResourceGroup = "ResourceGroup01"
    
    New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup

## <a name="using-a-certificate"></a>Использование сертификата

Чтобы использовать сертификат, необходимо применить действие **Get-AutomationCertificate** . Нельзя использовать командлет [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx), так как он возвращает сведения о ресурсе сертификата, но не сам сертификат.

### <a name="textual-runbook-sample"></a>Пример текстового Runbook

В следующем примере кода показано, как добавить сертификат из Runbook в облачную службу. В этом примере пароль извлекается из зашифрованной переменной службы автоматизации.

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>Пример графического Runbook

Вы можете добавить **Get-AutomationCertificate** в графический компонент Runbook, щелкнув правой кнопкой мыши сертификат в области "Библиотека" графического редактора и выбрав пункт **Добавить на холст**.

![Добавление сертификата на холст](media/automation-certificates/automation-certificate-add-to-canvas.png)

На следующем рисунке показан пример использования сертификата в графическом Runbook.  Этот же пример был показан выше, он добавляет сертификат из текстового Runbook в облачную службу.

![Пример графической разработки ](media/automation-certificates/graphical-runbook-add-certificate.png)


## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о работе со связями, с помощью которых можно управлять логической последовательностью действий, выполняемых модулем Runbook, см. в [Связи и рабочий процесс](automation-graphical-authoring-intro.md#links-and-workflow). 



<!--HONumber=Feb17_HO2-->


