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
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: f7297e938c5561328a07e22012205cc2ef3ac419
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="register-azure-stack-with-azure"></a>Регистрация Azure Stack в Azure
Регистрация [Azure Stack](azure-stack-poc.md) в Azure позволяет скачивать элементы Marketplace из Azure и настраивать передачу коммерческих данных в корпорацию Майкрософт. После регистрации Azure Stack данные об использовании отсылаются в отдел коммерческих предложений Azure и их можно просматривать в подписке, используемой для регистрации. 

> [!IMPORTANT]
> Регистрация является обязательной, если вы выбрали модель выставления счетов по мере использования. Иначе вы нарушите условия лицензирования развертывания Azure Stack, так как данные об использовании не будут отправляться.

## <a name="prerequisites"></a>предварительным требованиям
Для регистрации Azure Stack в Azure необходимо следующее:

- Идентификатор подписки Azure. Чтобы узнать этот идентификатор, войдите в Azure, щелкните **Больше служб** > **Подписки**, а затем выберите нужную подписку и найдите идентификатор подписки в разделе **Основные компоненты**. 

  > [!NOTE]
  > Сейчас облачные подписки для правительств Германии и США не поддерживаются.

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
Чтобы зарегистрировать поставщик ресурсов Azure Stack в Azure, запустите интегрированную среду сценариев PowerShell и используйте следующие команды PowerShell с параметром **EnvironmentName**, заданным для соответствующего типа подписки Azure (см. параметры ниже). 

1. Добавьте учетную запись Azure, которая использовалась для регистрации Azure Stack. Чтобы добавить учетную запись, выполните командлет **Connect-AzureRmAccount**. Вам будет предложено ввести данные учетной записи глобального администратора Azure. Возможно, потребуется выполнить двухфакторную аутентификацию в зависимости от конфигурации вашей учетной записи.

   ```PowerShell
      Connect-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | Параметр | ОПИСАНИЕ |  
   |-----|-----|
   | EnvironmentName | Имя среды облачной подписки Azure. Поддерживаемые имена среды — **AzureCloud** или, при использование подписки Azure для Китая, **AzureChinaCloud**.  |
   |  |  |

2. Если у вас несколько подписок Azure, выполните следующую команду, чтобы выбрать нужную подписку:  

   ```PowerShell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Выполните следующую команду, чтобы зарегистрировать поставщик ресурсов Azure Stack в своей подписке Azure:

   ```PowerShell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Регистрация Azure Stack в Azure с помощью модели выставления счетов с оплатой по мере использования
Следуйте инструкциям ниже, чтобы зарегистрировать Azure Stack в Azure с помощью модели выставления счетов с оплатой по мере использования.

1. Запустите интегрированную среду сценариев PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время [загрузки средств Azure Stack](#bkmk_tools). Импортируйте модуль **RegisterWithAzure.psm1** с помощью PowerShell: 

  ```powershell
  Import-Module .\RegisterWithAzure.psm1
  ```

2. Затем, в том же сеансе PowerShell, убедитесь, что вы вошли в правильный контекст Azure PowerShell. Это учетная запись Azure, которая использовалась для регистрации указанного выше поставщика ресурсов Azure Stack. Выполните эти команды PowerShell: 

  ```powershell 
  Connect-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>" 
  ``` 

3. В этом же сеансе PowerShell выполните командлет **Set-AzsRegistration**. Выполните эти команды PowerShell:  

  ```powershell
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse
  ```

  |Параметр|ОПИСАНИЕ|
  |-----|-----|
  |PrivilegedEndpointCredential|Учетные данные, которые используются для [получения доступа к привилегированной конечной точке](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Имя пользователя вводится в формате **AzureStackDomain\CloudAdmin**.|
  |PrivilegedEndpoint|Предварительно настроенная удаленная консоль PowerShell, которая позволяет собирать журналы и выполнять другие задачи после развертывания. Дополнительные сведения см. в разделе [Доступ к привилегированной конечной точке](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).|
  |BillingModel|Модель выставления счетов, которая используется в подписке. Для этого параметра допустимы такие значения: Capacity, PayAsYouUse и Development.|
  |  |  |

  Процесс займет от 10 до 15 минут. После выполнения команды отобразится сообщение **Your environment is now registered and activated using the provided parameters** (Ваша среда зарегистрирована и активирована с помощью предоставленных параметров).

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Регистрация Azure Stack в Azure с помощью модели выставления счетов с оплатой за емкость
Следуйте тем же инструкциям, что и для модели выставления счетов с оплатой по мере использования. Но добавьте номер соглашения, по которому приобретена емкость, и измените значение параметра **BillingModel** на **Capacity**. Остальные параметры не изменяются.

Выполните эти команды PowerShell:

```powershell
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Регистрация Azure Stack в отключенных средах 
*Сведения в этом разделе применяются к Azure Stack, начиная с версии обновления 1712 (180106.1), и не поддерживаются в более ранних версиях.*

При регистрации Azure Stack в отключенной среде (без подключения к Интернету) необходимо получить маркер регистрации из среды Azure Stack, а затем использовать этот маркер на компьютере, который можно подключить к Azure и на котором [установлена среда PowerShell для Azure Stack](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Получение маркера регистрации из среды Azure Stack

1. Запустите интегрированную среду сценариев PowerShell с правами администратора и перейдите к папке **Registration** в каталоге **AzureStack-Tools-master**, созданном во время [загрузки средств Azure Stack](#bkmk_tools). Импортируйте модуль **RegisterWithAzure.psm1**:  

  ```powershell 
  Import-Module .\RegisterWithAzure.psm1 
  ``` 

2. Чтобы получить маркер регистрации, выполните следующие команды PowerShell:  

  ```Powershell
  $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
  $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
  ```
  
  > [!TIP]  
  > Маркер регистрации сохранен в файле, указанном для *$FilePathForRegistrationToken*. Путь к файлу или его имя можно изменить по своему усмотрению. 

3. Сохраните этот маркер, чтобы использовать его на компьютере, подключенном к Azure. Файл или текст можно скопировать из $FilePathForRegistrationToken.


### <a name="connect-to-azure-and-register"></a>Подключение к Azure и регистрация
На компьютере, подключенном к Интернету, выполните те же действия, чтобы импортировать модуль RegisterWithAzure.psm1 и войти в правильный контекст Azure Powershell. Затем вызовите Register-AzsEnvironment и укажите токен регистрации для регистрации с помощью Azure:

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
  > Сохраните имя ресурса регистрации или токен регистрации для дальнейшего использования.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Извлечение ключа активации из ресурса регистрации Azure 
Затем необходимо извлечь ключ активации из ресурса регистрации, созданного в Azure во время выполнения Register-AzsEnvironment. 
 
Чтобы получить ключ активации, выполните следующие команды PowerShell:  

  ```Powershell 
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>" 
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt" 
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath 
  ``` 
  > [!TIP]   
  > Ключ активации сохраняется в файл, указанный для *$KeyOutputFilePath*. Путь к файлу или его имя можно изменить по своему усмотрению. 

### <a name="create-an-activation-resource-in-azure-stack"></a>Создание ресурса активации в Azure Stack 
Вернитесь в среду Azure Stack с файлом или текстом из ключа активации, созданного из Get-AzsActivationKey. Затем создайте ресурс активации в Azure Stack с помощью этого ключа активации. Чтобы создать ресурс активации, выполните следующие команды PowerShell:  

  ```Powershell 
  $ActivationKey = "<activation key>" 
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey 
  ``` 

При необходимости можно использовать командлет Get-Content, чтобы указать файл, содержащий маркер регистрации: 

  ```Powershell   
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>' 
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey 
  ``` 

## <a name="verify-azure-stack-registration"></a>Проверка регистрации Azure Stack
Чтобы убедиться, что инфраструктура Azure Stack успешно зарегистрирована в Azure, следуйте инструкциям ниже.
1. Войдите на [портал администратора](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal) Azure Stack: https&#58;//adminportal.*&lt;регион>.&lt;полное доменное имя>*.
2. Щелкните **Дополнительные службы** > **Marketplace Management** (Управление Marketplace) > **Add from Azure** (Добавить из Azure).

Если отобразится список элементов, доступных в Azure (например, WordPress), значит, активация прошла успешно. Тем не менее в отключенных средах элементы Azure Marketplace не отображаются в Azure Stack Marketplace.

> [!NOTE]
> По завершении регистрации активное предупреждение об отсутствии регистрации больше не будет отображаться.

## <a name="renew-or-change-registration"></a>Обновление или изменение регистрации
### <a name="renew-or-change-registration-in-connected-environments"></a>Обновление или изменение регистрации в подключенных средах
Регистрацию необходимо обновлять или возобновлять в следующих случаях:
- после возобновления годовой подписки на основе емкости;
- при изменении модели выставления счетов;
- при масштабировании (добавление или удаление узлов) для выставления счетов на основе емкости.

#### <a name="change-the-subscription-you-use"></a>Изменение подписки
Если вы хотите изменить используемую подписку, сначала выполните командлет **Remove-AzsRegistration**. Убедитесь, что вы вошли в правильном контексте выполнения Azure PowerShell, и выполните командлет **Set-AzsRegistration** с измененными параметрами:

  ```powershell
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>Изменение компонентов модели выставления счетов или синдикации
Если вам нужно изменить компоненты модели выставления счетов или синдикации для установки, чтобы задать новые значения, можно применить функцию регистрации. Для этого не требуется удалять текущую регистрацию: 

  ```powershell
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Обновление или изменение регистрации в отключенных средах 
Регистрацию необходимо обновлять или возобновлять в следующих случаях: 
- после возобновления годовой подписки на основе емкости; 
- при изменении модели выставления счетов; 
- при масштабировании (добавление или удаление узлов) для выставления счетов на основе емкости. 

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Удаление ресурса активации из Azure Stack 
Сначала необходимо удалить ресурс активации из Azure Stack, а затем — из Azure.  

Чтобы удалить ресурс активации из Azure Stack, выполните следующие команды PowerShell в среде Azure Stack:  

  ```Powershell 
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint 
  ``` 

Затем, чтобы удалить ресурс регистрации в Azure, убедитесь, что используется компьютер, подключенный к Azure, войдите в правильный контекст Azure PowerShell и выполните соответствующие команды PowerShell, как описано ниже.

Вы можете использовать токен регистрации, с помощью которого создавался ресурс:  

  ```Powershell 
  $registrationToken = "<registration token>" 
  Unregister-AzsEnvironment -RegistrationToken $registrationToken 
  ``` 
  
Или можно использовать имя регистрации: 

  ```Powershell 
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>" 
  Unregister-AzsEnvironment -RegistrationName $registrationName 
  ``` 

### <a name="re-register-using-disconnected-steps"></a>Повторная регистрация c использованием шагов для отключенной среды 
Регистрация полностью отменена в сценарии с отключенной средой. Теперь вам следует повторить шаги для регистрации среды Azure Stack в сценарии с отключенной средой. 

## <a name="next-steps"></a>Дополнительная информация

[Скачивание элементов Marketplace из Azure](azure-stack-download-azure-marketplace-item.md)
