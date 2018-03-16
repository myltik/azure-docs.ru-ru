---
title: "Регистрация Azure Stack | Документация Майкрософт"
description: "Регистрация Azure Stack в подписке Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/27/2018
ms.author: jeffgilb
ms.openlocfilehash: 8d520e1917d63b56a2adf3823ddbb741721fe751
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>Регистрация Azure Stack в подписке Azure

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете зарегистрировать [Azure Stack](azure-stack-poc.md) в Azure, чтобы скачивать элементы marketplace из Azure и настраивать передачу коммерческих данных в корпорацию Майкрософт.

> [!NOTE]
>Мы рекомендуем использовать регистрацию, так как она позволяет проверить важные функции Azure Stack, например синдикацию marketplace и отчеты о потреблении. После регистрации Azure Stack данные о потреблении передаются в коммерческий отдел Azure. Вы сможете увидеть их в той подписке, которую использовали для регистрации. С пользователей Пакета средств разработки Azure Stack плата за передаваемые данные о потреблении не взимается.


## <a name="get-azure-subscription"></a>Получение подписки Azure

Для регистрации Azure Stack в Azure необходимо следующее:

- Идентификатор подписки Azure. Чтобы узнать этот идентификатор, выполните вход в Azure, щелкните **Дополнительные службы** > **Подписки**, затем выберите нужную подписку и найдите **Идентификатор подписки** в разделе **Основные компоненты**. В настоящее время облачные подписки для правительств Китая, Германии и США не поддерживаются.
- Имя пользователя и пароль учетной записи владельца подписки (поддерживаются учетные записи MSA/2FA).
- *Не требуется, начиная с версии обновления Azure Stack 1712 (180106.1).* Azure Active Directory для подписки Azure. Чтобы получить сведения об Active Directory, наведите курсор на аватар в правом верхнем углу портала Azure.

Если у вас нет подписки Azure, соответствующей всем этим требованиям, [создайте бесплатную учетную запись](https://azure.microsoft.com/en-us/free/?b=17.06). За регистрацию Azure Stack в подписке Azure дополнительная плата не взимается.

## <a name="register-azure-stack-with-azure"></a>Регистрация Azure Stack в Azure  
> [!NOTE]
> Все шаги необходимо выполнять на компьютере с доступом к привилегированной конечной точке. Для Пакета средств разработки Azure Stack это будет главный компьютер. Если вы используете интегрированную систему, обратитесь к оператору Azure Stack.
>

1. Откройте консоль PowerShell от имени администратора и [установите PowerShell для Azure Stack](azure-stack-powershell-install.md).  

2. Добавьте учетную запись Azure, которая использовалась для регистрации Azure Stack. Для этого выполните командлет `Add-AzureRmAccount`, задав для параметра EnvironmentName значение **AzureCloud**. Вам будет предложено ввести учетные данные учетной записи Azure. Также может потребоваться выполнить двухфакторную аутентификацию в зависимости от конфигурации вашей учетной записи.

   ```Powershell
   Add-AzureRmAccount -EnvironmentName "AzureCloud"
   ```

3. Если у вас несколько подписок Azure, выполните следующую команду, чтобы выбрать нужную подписку:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Выполните следующую команду, чтобы зарегистрировать поставщик ресурсов Azure Stack в своей подписке Azure:

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Удалите все имеющиеся версии модулей PowerShell, связанных с регистрацией, и [скачайте последнюю версию с сайта GitHub](azure-stack-powershell-download.md).  

6. В каталоге AzureStack-Tools-master, созданном на предыдущем шаге, перейдите в папку Registration и импортируйте модуль .\RegisterWithAzure.psm1.  

   ```powershell
   Import-Module .\RegisterWithAzure.psm1
   ```

7. В том же сеансе PowerShell выполните следующий сценарий. Когда появится запрос на ввод учетных данных, в качестве имени пользователя укажите `azurestack\cloudadmin` и введите пароль для локального администратора, который использовался при развертывании.  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Set-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development
   ```

   | Параметр | ОПИСАНИЕ |  
   |--------|-------------|
   | CloudAdminCredential | Учетные данные для облачного домена, которые используются для [получения доступа к привилегированной конечной точке](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Имя пользователя вводится в формате **\<домен_Azure_Stack\>\cloudadmin**. Имя пользователя для пакета средств разработки — **azurestack\cloudadmin**. Если вы используете интегрированную систему, обратитесь к оператору Azure Stack, чтобы получить это значение.|  
   | PrivilegedEndpoint | Предварительно настроенная удаленная консоль PowerShell, которая позволяет собирать журналы и выполнять другие задачи после развертывания. Для пакета средств разработки привилегированная конечная точка размещается на виртуальной машине AzS-ERCS01. Если вы используете интегрированную систему, обратитесь к оператору Azure Stack, чтобы получить это значение. Дополнительные сведения см. в разделе [Доступ к привилегированной конечной точке](azure-stack-privileged-endpoint.md).|  
   | BillingModel | Модель выставления счетов, которая используется в подписке. Для этого параметра допустимы такие значения: **Capacity**, **PayAsYouUse** и **Development**. Для пакета средств разработки используется значение **Development**. Если вы используете интегрированную систему, обратитесь к оператору Azure Stack, чтобы получить это значение. |

8. По завершении скрипта появится такое сообщение: Activating Azure Stack (this step may take up to 10 minutes to complete) (Активация Azure Stack (эта операция может занять до 10 минут)).




## <a name="verify-the-registration"></a>Проверка регистрации  

1. Войдите на портал администратора Azure Stack (https://adminportal.local.azurestack.external).

2. Щелкните **Дополнительные службы** > **Marketplace Management** (Управление Marketplace) > **Add from Azure** (Добавить из Azure).

3. Если отобразится список элементов, доступных в Azure (например, WordPress), значит, активация прошла успешно.

> [!NOTE]
> По завершении регистрации активное предупреждение об отсутствии регистрации больше не будет отображаться.


## <a name="modify-the-registration"></a>Изменение регистрации

### <a name="change-the-subscription-you-use"></a>Изменение используемой подписки
Если вы хотите изменить используемую подписку, необходимо сначала выполнить командлет Remove-AzsRegistration. Убедитесь, что вы вошли в правильном контексте выполнения Azure PowerShell, и выполните командлет Set-AzsRegistration с измененными параметрами.

  ```Powershell   
  Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```
### <a name="change-the-billing-model-or-syndication-features"></a>Изменение компонентов модели выставления счетов или синдикации
Если вам нужно изменить компоненты модели выставления счетов или синдикации для установки, чтобы задать новые значения, можно применить функцию регистрации. Для этого не требуется удалять текущую регистрацию.  

  ```Powershell
     Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```


## <a name="disconnected-registration"></a>Отключенная регистрация
*Сведения в этом разделе применяются к Azure Stack, начиная с версии обновления 1712 (180106.1), и не поддерживаются в более ранних версиях.*

При регистрации Azure Stack в отключенной среде необходимо получить токен регистрации из среды Azure Stack, а затем использовать этот токен на компьютере, который можно подключить к Azure для регистрации.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Получение токена регистрации из среды Azure Stack
  1. Чтобы получить токен регистрации, сделайте следующее:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > Токен регистрации сохранен в файле, указанном для *$env:SystemDrive\RegistrationToken.txt*.

  2. Сохраните этот маркер, чтобы использовать его на компьютере, подключенном к Azure.


### <a name="connect-to-azure-and-register"></a>Подключение к Azure и регистрация
Выполните действия для данной процедуры на компьютере, который можно подключить к Azure.

  1. [Скачайте с GitHub последние модули PowerShell, соответствующие регистрации](azure-stack-powershell-download.md).  

  2. В каталоге AzureStack-Tools-master, созданном на предыдущем шаге, перейдите в папку Registration и импортируйте модуль .\RegisterWithAzure.psm1.  

     ```powershell
     Import-Module .\RegisterWithAzure.psm1
     ```

  3. Скопируйте [RegisterWithAzure.psm1](https://go.microsoft.com/fwlink/?linkid=842959) в папку, например *C:\Temp*.

  4. Запустите интегрированную среду PowerShell ISE с правами администратора, а затем импортируйте модуль RegisterWithAzure.  

  5. Убедитесь, что вошли в правильном контексте выполнения Azure PowerShell, который необходимо использовать для регистрации среды Azure Stack.  

     ```Powershell
        Set-AzureRmContext -SubscriptionId $YourAzureSubscriptionId   
     ```
  6. Укажите токен регистрации для регистрации с помощью Azure:

     ```Powershell  
       $registrationToken = "*Your Registration Token*"
       Register-AzsEnvironment -RegistrationToken $registrationToken  
     ```
    При необходимости можно использовать командлет Get-Content, чтобы указать файл, содержащий токен регистрации:

    ```Powershell  
       $registrationToken = Get-Content -Path 'C:\Temp\<Registration Token File>'
       Register-AzsEnvironment -RegistrationToken $registrationToken  
    ```
> [!NOTE]  
> Сохраните имя ресурса регистрации или токен регистрации для дальнейшего использования.

### <a name="remove-a-registered-resource"></a>Удаление зарегистрированных ресурсов
Если вы хотите удалить ресурс регистрации, необходимо использовать командлет UnRegister-AzsEnvironment и передать имя ресурса регистрации или токен регистрации, использованный для командлета Register-AzsEnvironment.
- **Имя ресурса регистрации:**

  ```Powershell    
     UnRegister-AzsEnvironment -RegistrationName "*Name of the registration resource*"
  ```
- **Токен регистрации:**    

  ```Powershell
     $registrationToken = "*Your copied registration token*"
     UnRegister-AzsEnvironment -RegistrationToken $registrationToken
   ```


## <a name="next-steps"></a>Дополнительная информация
[Подключение к Azure Stack](azure-stack-connect-azure-stack.md)
