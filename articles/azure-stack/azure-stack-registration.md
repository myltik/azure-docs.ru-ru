---
title: "Регистрация Azure для интегрированных систем Azure Stack | Документация Майкрософт"
description: "Описано, как зарегистрировать Azure для подключенных к Azure развернутым службам с несколькими узлами Azure Stack."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: d5b77bb43c48bd286708ca96699b20be0f761baa
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="register-azure-stack-with-azure"></a>Регистрация Azure Stack в Azure
Вы можете зарегистрировать Azure Stack в Azure, чтобы скачивать элементы marketplace из Azure и настраивать передачу коммерческих данных в корпорацию Майкрософт. После регистрации Azure Stack данные о потреблении передаются в коммерческий отдел Azure. Вы сможете увидеть их в той подписке, которую использовали для регистрации.

> [!IMPORTANT]
> Регистрация является обязательной, если вы выбрали модель выставления счетов по мере использования. Иначе вы нарушите условия лицензирования развертывания Azure Stack, так как данные об использовании не будут отправляться.

## <a name="before-you-register-azure-stack-with-azure"></a>Перед регистрацией Azure Stack в Azure
Для регистрации Azure Stack в Azure необходимо следующее:

- Идентификатор подписки Azure. Чтобы узнать этот идентификатор, войдите в Azure, щелкните **Больше служб** > **Подписки**, а затем выберите нужную подписку и найдите идентификатор подписки в разделе **Основные компоненты**. 

  > [!NOTE]
  > В настоящее время облачные подписки для правительств Китая, Германии и США не поддерживаются. 

- Имя пользователя и пароль учетной записи владельца подписки (поддерживаются учетные записи MSA/2FA).
- *Не требуется после выпуска обновления Azure Stack 1712 (180106.1).* Azure Active Directory для подписки Azure. Чтобы получить сведения об Active Directory, наведите курсор на аватар в правом верхнем углу портала Azure. 
- Зарегистрированный поставщик ресурсов Azure Stack. Дополнительные сведения см. в разделе "Регистрация поставщика ресурсов Azure Stack".

Если у вас нет подписки Azure, соответствующей всем этим требованиям, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?b=17.06). За регистрацию Azure Stack в подписке Azure дополнительная плата не взимается.

### <a name="bkmk_powershell"></a>Установка PowerShell для Azure Stack
Используйте последнюю версию PowerShell для Azure Stack, чтобы зарегистрировать систему в Azure.

Если вы еще не сделали это, [установите PowerShell для Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Скачивание средств Azure Stack
Репозиторий GitHub со средствами Azure Stack содержит модули PowerShell, которые поддерживают функциональные возможности Azure Stack, в том числе функции регистрации. Для регистрации экземпляра Azure Stack в Azure вам нужно импортировать и использовать модуль PowerShell RegisterWithAzure.psm1, который можно найти в репозитории средств Azure Stack. 

```powershell
# Change directory to the root directory. 
cd \

# Download the tools archive.
  invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
  expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
  cd AzureStack-Tools-master
```

## <a name="register-azure-stack-in-connected-environments"></a>Регистрация Azure Stack в подключенных средах
Подключенные среды могут иметь доступ к Интернету и Azure. Для этих сред необходимо зарегистрировать в Azure поставщик ресурсов Azure Stack, а затем настроить модель выставления счетов.

### <a name="register-the-azure-stack-resource-provider"></a>Регистрация поставщика ресурсов Azure Stack
Чтобы зарегистрировать поставщик ресурсов Azure Stack в Azure, запустите интегрированную среду сценариев PowerShell от имени администратора и выполните следующие команды PowerShell. Эти команды выполняют следующие действия:
- Отображают запрос на вход от имени владельца используемой подписки Azure и задают для параметра `EnvironmentName` значение **AzureCloud**.
- Регистрируют поставщик ресурсов Azure **Microsoft.AzureStack**.

Выполните эти команды PowerShell:

```powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack 
```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Регистрация Azure Stack в Azure с помощью модели выставления счетов с оплатой по мере использования
Следуйте инструкциям ниже, чтобы зарегистрировать Azure Stack в Azure с помощью модели выставления счетов с оплатой по мере использования.

Запустите интегрированную среду сценариев PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время [загрузки средств Azure Stack](#bkmk_tools). Импортируйте модуль **RegisterWithAzure.psm1** с помощью PowerShell: 

Выполните эти команды PowerShell:

```powershell
Import-Module .\RegisterWithAzure.psm1
```
Затем в том же сеансе PowerShell выполните командлет **Set-AzsRegistration**. При появлении запроса на ввод учетных данных укажите владельца подписки Azure.  

Выполните эти команды PowerShell:

```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|Параметр|ОПИСАНИЕ|
|-----|-----|
|CloudAdminCredential|Объект PowerShell, который содержит учетные данные (имя пользователя и пароль) владельца подписки Azure.|
|PrivilegedEndpoint|Предварительно настроенная удаленная консоль PowerShell, которая позволяет собирать журналы и выполнять другие задачи после развертывания. Дополнительные сведения см. в разделе [Доступ к привилегированной конечной точке](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint).|
|BillingModel|Модель выставления счетов, которая используется в подписке. Для этого параметра допустимы такие значения: Capacity, PayAsYouUse и Development.|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Регистрация Azure Stack в Azure с помощью модели выставления счетов с оплатой за емкость
Следуйте тем же инструкциям, что и для модели выставления счетов с оплатой по мере использования. Но добавьте номер соглашения, по которому приобретена емкость, и измените значение параметра `BillingModel` на **Capacity**. Остальные параметры не изменяются.

Выполните эти команды PowerShell:
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Azure subscription owner>  -Message "Enter the cloud domain credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Регистрация Azure Stack в отключенных средах 
*Сведения в этом разделе применяются к Azure Stack, начиная с версии обновления 1712 (180106.1), и не поддерживаются в более ранних версиях.*

При регистрации Azure Stack в отключенной среде (без подключения к Интернету) необходимо получить маркер регистрации из среды Azure Stack, а затем использовать этот маркер на компьютере, который можно подключить к Azure и на котором [установлена среда PowerShell для Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Получение маркера регистрации из среды Azure Stack
  1. Чтобы получить маркер регистрации, выполните следующие команды PowerShell:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > Маркер регистрации сохранен в файле, указанном для *$env:SystemDrive\RegistrationToken.txt*.

  2. Сохраните этот маркер, чтобы использовать его на компьютере, подключенном к Azure.


### <a name="connect-to-azure-and-register"></a>Подключение к Azure и регистрация
Запустите интегрированную среду сценариев PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время [загрузки средств Azure Stack](#bkmk_tools). Импортируйте модуль **RegisterWithAzure.psm1**: 

Выполните эти команды PowerShell:
```powershell
Import-Module .\RegisterWithAzure.psm1
```
Затем в том же сеансе PowerShell укажите маркер регистрации для регистрации в Azure:

```Powershell  
$registrationToken = "<Your Registration Token>"
Register-AzsEnvironment -RegistrationToken $registrationToken  
```
При необходимости можно использовать командлет Get-Content, чтобы указать файл, содержащий маркер регистрации:

 ```Powershell  
 $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
 Register-AzsEnvironment -RegistrationToken $registrationToken  
 ```
> [!NOTE]  
> Сохраните имя ресурса регистрации или маркер регистрации для дальнейшего использования.

## <a name="verify-azure-stack-registration"></a>Проверка регистрации Azure Stack
Чтобы убедиться, что инфраструктура Azure Stack успешно зарегистрирована в Azure, следуйте инструкциям ниже.
1. Войдите на [портал администратора](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal) Azure Stack: https&#58;//adminportal.*&lt;регион>.&lt;полное доменное имя>*.
2. Щелкните **Дополнительные службы** > **Marketplace Management** (Управление Marketplace) > **Add from Azure** (Добавить из Azure).

Если отобразится список элементов, доступных в Azure (например, WordPress), значит, активация прошла успешно.

> [!NOTE]
> По завершении регистрации активное предупреждение об отсутствии регистрации больше не будет отображаться.

## <a name="renew-or-change-registration"></a>Обновление или изменение регистрации
Регистрацию необходимо обновлять или возобновлять в следующих случаях:
- после возобновления годовой подписки на основе емкости;
- при изменении модели выставления счетов;
- при масштабировании (добавление или удаление узлов) для выставления счетов на основе емкости.

### <a name="change-the-subscription-you-use"></a>Изменение подписки
Если вы хотите изменить используемую подписку, сначала выполните командлет **Remove-AzsRegistration**. Убедитесь, что вы вошли в правильном контексте выполнения Azure PowerShell, и выполните командлет **Set-AzsRegistration** с измененными параметрами:

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>Изменение компонентов модели выставления счетов или синдикации
Если вам нужно изменить компоненты модели выставления счетов или синдикации для установки, чтобы задать новые значения, можно применить функцию регистрации. Для этого не требуется удалять текущую регистрацию: 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

## <a name="remove-a-registered-resource"></a>Удаление зарегистрированных ресурсов
Если вы хотите удалить ресурс, используйте командлет **UnRegister-AzsEnvironment** и передайте имя ресурса регистрации или маркер регистрации, использованный для командлета **Register-AzsEnvironment**.

Отмена регистрации с помощью имени ресурса:

```Powershell    
UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
```
Отмена регистрации с помощью маркера регистрации:

```Powershell
$registrationToken = "*Your copied registration token*"
UnRegister-AzsEnvironment -RegistrationToken $registrationToken
```

## <a name="next-steps"></a>Дополнительная информация

[Интеграция внешнего решения для мониторинга](azure-stack-integrate-monitor.md)