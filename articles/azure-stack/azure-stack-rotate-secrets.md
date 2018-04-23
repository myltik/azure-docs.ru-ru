---
title: Смена секретов в Azure Stack | Документация Майкрософт
description: Дополнительные сведения о смене секретов в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: a158da6fb397b864a439e067ca99d79814e2b8d2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Смена секретов в Azure Stack

*Эти инструкции применяются только к интегрированным системам Azure Stack (1803 и более поздних версий). Не пытайтесь сменить секреты в версиях Azure Stack, предшествующих 1802.*

Azure Stack использует различные секреты, чтобы обеспечивать безопасный обмен данными между службами и ресурсами инфраструктуры Azure Stack.

- **Внутренние секреты**  
Все сертификаты, пароли, защищенные строки и ключи, используемые инфраструктурой Azure Stack без вмешательства оператора Azure Stack. 

- **Внешние секреты**  
Сертификаты службы инфраструктуры для внешних служб, предоставляемых оператором Azure Stack. Сюда входят сертификаты для следующих служб: 
    - портал администрирования; 
    - Общедоступный портал 
    - Azure Resource Manager для администратора; 
    - глобальный Azure Resource Manager; 
    - хранилище ключей администратора; 
    - Хранилище ключей 
    - ACS (включая хранилище BLOB-объектов, таблиц и очередей); 
    - ADFS<sup>*</sup>;
    - Graph<sup>*</sup>.

    > <sup>*</sup> Применимо, только если поставщиком удостоверений среды являются службы федерации Active Directory (AD FS).

> [!NOTE]
> Все остальные защищенные ключи и строки, включая пароли BMC и коммутатора, пароли учетной записи пользователя и администратора, по-прежнему вручную обновляются администратором. 

Чтобы поддерживать целостность инфраструктуры Azure Stack, операторам необходима возможность периодически сменять секреты своей инфраструктуры с частотой, соответствующей требованиям безопасности организации.

## <a name="alert-remediation"></a>Исправление оповещений

Когда до истечения срока действия секретов осталось 30 дней, на портале администратора появляются следующие оповещения: 

- ожидается истечение срока действия пароля учетной записи службы; 
- ожидается истечение срока действия внутреннего сертификата; 
- ожидается истечение срока действия внешнего сертификата. 

Выполнение смены секрета с помощью описанных ниже инструкций устранит эти оповещения.

## <a name="pre-steps-for-secret-rotation"></a>Предварительные шаги для смены секретов

1.  Уведомите пользователей об операциях обслуживания. Запланируйте стандартные окна обслуживания, по возможности в нерабочие часы. Операции технического обслуживания могут влиять на рабочие нагрузки пользователей и на операции портала.

    > [!note]  
    > Следующие шаги применяются только при смене внешних секретов Azure Stack.

2.  Подготовьте новый набор внешних сертификатов для замены. Новый набор соответствует спецификациям сертификата, приведенным в статье [Требования к сертификатам инфраструктуры открытых ключей Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs).
3.  Сохраните резервную копию в сертификаты, используемые для смены, в защищенном расположении. Если смена выполняется, а затем завершается со сбоем, замените сертификаты в общей папке резервными копиями перед повторным запуском смены. Обратите внимание, что резервные копии необходимо сохранить в безопасном расположении.
3.  Создайте общую папку, к которой можно получить доступ из виртуальных машин ERCS. Общая папка должна быть доступна для чтения и записи для удостоверения **CloudAdmin**.
4.  Откройте консоль интегрированной среды сценариев PowerShell на компьютере с доступом к общей папке. Перейдите к общей папке. 
5.  Запустите файл **[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)**, чтобы создать требуемые каталоги для внешних сертификатов.

## <a name="rotating-external-and-internal-secrets"></a>Смена внешних и внутренних секретов

Чтобы сменить внешние и внутренние секреты:

1. В каталоге **/Certificates**, созданном в рамках предварительных шагов, установите новый набор внешних сертификатов для замены в структуре каталога в соответствии с форматом, описанным в разделе "Обязательные сертификаты" статьи [Требования к сертификатам инфраструктуры открытых ключей Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates).
2. Создайте сеанс PowerShell с [привилегированной конечной точкой](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) с использованием учетной записи **CloudAdmin** и сохраните сеансы в качестве переменных. Эта переменная будет использоваться как параметр в следующем шаге.

    > [!IMPORTANT]  
    > Не входите в сеанс, сохраните его как переменную.
    
3. Выполните команду **[invoke-command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)**. Передайте переменную сеанса PowerShell с привилегированной конечной точкой в качестве параметра **Сеанс**. 
4. Выполните команду **Start-SecretRotation** со следующими параметрами:
    - **PfxFilesPath**  
    Укажите сетевой путь к созданному ранее каталогу сертификатов.  
    - **PathAccessCredential**  
    Объект PSCredential для учетных данных в общей папке. 
    - **CertificatePassword**  
    Защищенная строка пароля, используемая для всех созданных файлов сертификатов PFX.
4. Дождитесь смены секретов.  
После успешного выполнения смены секретов консоль отобразит следующее: **Overall action status: Success** (Общее состояние действия: успешно выполнено). 
5. После успешного завершения смены секретов удалите свои сертификаты из общего ресурса, созданного на предварительном шаге, и сохраните их в безопасном расположении резервного копирования. 

## <a name="walkthrough-of-secret-rotation"></a>Пошаговое руководство по смене секретов

Следующий пример PowerShell демонстрирует командлеты и параметры, необходимые для смены секретов.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -AsPlainText -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>Смена только внутренних секретов

Чтобы сменить только внутренние секреты Azure Stack:

1. Создайте сеанс PowerShell с [привилегированной конечной точкой](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).
2. В сеансе с привилегированной конечной точкой выполните **Start-SecretRotation** без аргументов.

## <a name="start-secretrotation-reference"></a>Справочник по Start-SecretRotation

Этот командлет сменяет секреты системы Azure Stack. Выполняется только с привилегированной конечной точкой Azure Stack.

### <a name="syntax"></a>Синтаксис

Путь (по умолчанию)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>ОПИСАНИЕ

Командлет Start-SecretRotation сменяет секреты инфраструктуры системы Azure Stack. По умолчанию он сменяет все секреты, предоставленные внешней инфраструктуре сети. При вводе данных пользователем он также сменяет сертификаты конечных точек инфраструктуры внешней сети. При смене конечных точек инфраструктуры внешней сети командлет Start-SecretRotation необходимо выполнить через блок скрипта Invoke-Command в сеансе с привилегированной конечной точкой среды Azure Stack, переданном в качестве параметра сеанса.
 
### <a name="parameters"></a>Параметры

| Параметр | type | Обязательно | Позиция | значение по умолчанию | ОПИСАНИЕ |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | Строка  | Ложь  | именованная  | None  | Путь к общей папке в каталоге **\Certificates**, который содержит все сертификаты конечных точек внешней сети. Требуется только при смене внешних и внутренних секретов. Конечным каталогом должен быть **\Certificates**. |
| CertificatePassword | SecureString | Ложь  | именованная  | Нет  | Пароль для всех сертификатов, предоставляемых в -PfXFilesPath. Необходимое значение, если PfxFilesPath предоставлен, когда сменяются внешние и внутренние секреты. |
|

### <a name="examples"></a>Примеры
 
**Смена только секретов внутренней инфраструктуры**

```powershell  
PS C:\> Start-SecretRotation  
```

Эта команда сменяет все инфраструктурные секреты, предоставляемые внутренней сети Azure Stack. Командлет Start-SecretRotation сменяет все созданные стеком секреты, однако из-за того, что предоставленные сертификаты отсутствуют, сертификаты внешних конечных точек не будут сменены.  

**Смена секретов внешней и внутренней инфраструктуры**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

Эта команда сменяет все секреты инфраструктуры, предоставляемые во внутренней сети Azure Stack, а также сертификаты TSL, используемые конечными точками инфраструктуры внешней сети Azure Stack. Командлет Start-SecretRotation сменяет все созданные стеком секреты и, так как сертификаты предоставлены, сертификаты внешних конечных точек.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>Обновление пароля контроллера управления основной платой (BMC)

Контроллер управления основной платой отслеживает физическое состояние серверов. Спецификации и инструкции по обновлению паролей контроллеров управления основной платой различаются в зависимости от поставщика вычислительной техники. Следует регулярно обновлять пароли для компонентов Azure Stack.

1. Обновите контроллер управления основной платой на физических серверах Azure Stack в соответствии с инструкциями поставщика. Пароли для всех контроллеров в среде должны совпадать.
2. Откройте привилегированную конечную точку в сеансах Azure Stack. См. инструкции по [использованию привилегированной конечной точки в Azure Stack](azure-stack-privileged-endpoint.md).
3. После изменения запроса PowerShell на **[IP-адрес или имя виртуальной машины ERCS]: PS>** или на **[azs-ercs01]: PS>** (в зависимости от среды) выполните `Set-BmcPassword`, запустив `invoke-command`. Передайте переменную сеанса привилегированной конечной точки в качестве параметра. Например: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    Также можно использовать статическую версию PowerShell с паролями в качестве строк кода:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>Дополнительная информация

[Система безопасности для инфраструктуры Azure Stack](azure-stack-security-foundations.md)
