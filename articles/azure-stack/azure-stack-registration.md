---
title: Регистрация Azure для интегрированных систем Azure Stack | Документация Майкрософт
description: Описано, как зарегистрировать Azure для подключенных к Azure развернутым службам с несколькими узлами Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: 1dc3d9a96b9b27927cc8cc66b5e80987fba4f8ea
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="register-azure-stack-with-azure"></a>Регистрация Azure Stack в Azure
Регистрация [Azure Stack](azure-stack-poc.md) в Azure позволяет скачивать элементы Marketplace из Azure и настраивать передачу коммерческих данных в корпорацию Майкрософт. После регистрации Azure Stack данные об использовании отсылаются в отдел коммерческих предложений Azure и их можно просматривать в подписке, используемой для регистрации. 

> [!IMPORTANT]
> Регистрация является обязательной, если вы выбрали модель выставления счетов по мере использования. Иначе вы нарушите условия лицензирования развертывания Azure Stack, так как данные об использовании не будут отправляться.

## <a name="prerequisites"></a>предварительным требованиям
Для регистрации Azure Stack в Azure необходимо следующее:

- Идентификатор подписки Azure. Чтобы узнать этот идентификатор, войдите в Azure, щелкните **Больше служб** > **Подписки**, а затем выберите нужную подписку и найдите идентификатор подписки в разделе **Основные компоненты**. 

  > [!NOTE]
  > Сейчас облачные подписки для правительств Китая, Германии и США не поддерживаются. 

- Имя пользователя и пароль учетной записи владельца подписки (поддерживаются учетные записи MSA/2FA).
- Зарегистрированный поставщик ресурсов Azure Stack. Дополнительные сведения см. в приведенном ниже разделе о регистрации поставщика ресурсов Azure Stack.

Если у вас нет подписки Azure, соответствующей всем этим требованиям, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?b=17.06). За регистрацию Azure Stack в подписке Azure дополнительная плата не взимается.

### <a name="bkmk_powershell"></a>Установка PowerShell для Azure Stack
Чтобы зарегистрировать систему в Azure, требуется последняя версия PowerShell для Azure Stack.

Если вы еще не сделали это, [установите PowerShell для Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Скачивание средств Azure Stack
Репозиторий GitHub со средствами Azure Stack содержит модули PowerShell, которые поддерживают функциональные возможности Azure Stack, в том числе функции регистрации. Для регистрации экземпляра Azure Stack в Azure вам нужно импортировать и использовать модуль PowerShell RegisterWithAzure.psm1, который можно найти в репозитории средств Azure Stack. 

Перед регистрацией в Azure удалите все существующие версии средств Azure Stack и [загрузите новейшую версию с сайта GitHub](azure-stack-powershell-download.md).

## <a name="register-azure-stack-in-connected-environments"></a>Регистрация Azure Stack в подключенных средах
Подключенные среды могут иметь доступ к Интернету и Azure. Для этих сред необходимо зарегистрировать в Azure поставщик ресурсов Azure Stack, а затем настроить модель выставления счетов.

> [!NOTE]
> Все шаги необходимо выполнять на компьютере с доступом к привилегированной конечной точке. 

### <a name="register-the-azure-stack-resource-provider"></a>Регистрация поставщика ресурсов Azure Stack
Чтобы зарегистрировать поставщик ресурсов Azure Stack в Azure, запустите интегрированную среду сценариев PowerShell от имени администратора и выполните следующие команды PowerShell. Эти команды выполняют следующие действия:
- Отображение запроса на вход от имени владельца используемой подписки Azure и установка для параметра **EnvironmentName** значения **AzureCloud**.
- Регистрируют поставщик ресурсов Azure **Microsoft.AzureStack**.

1. Добавьте учетную запись Azure, которая использовалась для регистрации Azure Stack. Чтобы добавить учетную запись, выполните командлет **Add-AzureRmAccount**. Вам будет предложено ввести данные учетной записи глобального администратора Azure. Возможно, потребуется выполнить двухфакторную аутентификацию в зависимости от конфигурации вашей учетной записи.

   ```Powershell
      Add-AzureRmAccount -EnvironmentName AzureCloud
   ```

2. Если у вас несколько подписок Azure, выполните следующую команду, чтобы выбрать нужную подписку:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Выполните следующую команду, чтобы зарегистрировать поставщик ресурсов Azure Stack в своей подписке Azure:

   ```Powershell
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
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials>  -Message "Enter the credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|Параметр|ОПИСАНИЕ|
|-----|-----|
|CloudAdminCredential|Объект PowerShell, который содержит учетные данные (имя пользователя и пароль) для доступа к привилегированной конечной точке.|
|PrivilegedEndpoint|Предварительно настроенная удаленная консоль PowerShell, которая позволяет собирать журналы и выполнять другие задачи после развертывания. Дополнительные сведения см. в разделе [Доступ к привилегированной конечной точке](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint).|
|BillingModel|Модель выставления счетов, которая используется в подписке. Для этого параметра допустимы такие значения: Capacity, PayAsYouUse и Development.|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Регистрация Azure Stack в Azure с помощью модели выставления счетов с оплатой за емкость
Следуйте тем же инструкциям, что и для модели выставления счетов с оплатой по мере использования. Но добавьте номер соглашения, по которому приобретена емкость, и измените значение параметра `BillingModel` на **Capacity**. Остальные параметры не изменяются.

Выполните эти команды PowerShell:
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials>  -Message "Enter the credentials to access the privileged endpoint"
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
## <a name="next-steps"></a>Дополнительная информация

[Скачивание элементов Marketplace из Azure](azure-stack-download-azure-marketplace-item.md)