---
title: Проверка регистрации Azure для Azure Stack | Документация Майкрософт
description: Применение средства проверки готовности Azure Stack для проверки регистрации Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 84eb1c08cc3f9ef104e2eb0b96ed397315c3f374
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937568"
---
# <a name="validate-azure-registration"></a>Проверка регистрации в Azure 
Средство проверки готовности Azure Stack (AzsReadinessChecker) позволяет убедиться, что ваша подписка Azure готова к работе с Azure Stack. Проверьте регистрацию перед тем, как развертывать Azure Stack. Средство проверки готовности выполняет следующие проверки:
- Используемая вами подписка Azure является поддерживаемой. Подписки должны быть типа "Поставщик облачных служб" или "Соглашение Enterprise". 
- Учетная запись, используемая для регистрации подписки в Azure, может войти в Azure и является владельцем подписки. 

Дополнительные сведения о регистрации Azure Stack см. в [этой статье](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Получение средства проверки готовности
Скачайте последнюю версию средства проверки готовности Azure Stack (AzsReadinessChecker), доступную в [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>предварительным требованиям
Убедитесь, что выполнены указанные ниже предварительные требования.

**На компьютере, где запускается это средство:**
 - работает Windows 10 или Windows Server 2016 и есть подключение к Интернету;
 - установлен PowerShell 5.1 или более поздней версии. Чтобы проверить используемую версию, запустите следующий командлет PowerShell и проверьте значения *Major* (основной номер версии) и *Minor* (дополнительный номер версии):  

    >`$PSVersionTable.PSVersion` 
 - Настройте [PowerShell для использования с Azure Stack](azure-stack-powershell-install.md). 
 - Загрузите последнюю версию [средства проверки готовности Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker).  

**Среда Azure Active Directory:**
 - Определите имя пользователя и пароль для учетной записи, которая является владельцем подписки Azure, которую вы будете использовать с Azure Stack.  
 - Определите идентификатор подписки Azure, которая будет использоваться. 
 - Определите окружение AzureEnvironement, которое вы намерены использовать: *AzureCloud*, *AzureGermanCloud* или *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Проверка регистрации в Azure
1. На компьютере, который соответствует всем предварительным условиям, откройте командную строку PowerShell с правами администратора и выполните следующую команду, чтобы установить AzsReadinessChecker.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. В командной строке PowerShell выполните следующее, чтобы задать *$registrationCredential* в качестве учетной записи, которая является владельцем подписки.   Замените *subscriptionowner@contoso.onmicrosoft.com* именами учетной записи и клиента, соответственно. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. В командной строке PowerShell выполните следующее, чтобы задать *$subscriptionID* в качестве используемой подписки Azure. Замените *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* собственным идентификатором подписки.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. В командной строке PowerShell выполните следующую команду, чтобы начать проверку подписки. 
   - Для параметра AzureEnvironement укажите значение *AzureCloud*, *AzureGermanCloud* или *AzureChinaCloud*.  
   - Предоставьте имя администратора Azure Active Directory и имя клиента Azure Active Directory. 

   > `Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Когда средство завершит работу, просмотрите выходные данные. Убедитесь, что в них указано состояние "ОК" для требований ко входу в систему и к регистрации. Успешное завершение проверки отображается так, как на следующем рисунке:  
![Запуск проверки](./media/azure-stack-validate-registration/registration-validation.png)


## <a name="report-and-log-file"></a>Файлы отчета и журнала
При каждом запуске проверки все результаты сохраняются в файлы **AzsReadinessChecker.log** и **AzsReadinessCheckerReport.json**. Расположение этих файлов отображается в PowerShell вместе с результатами проверки. 

Эти файлы помогут передать состояние проверки другим заинтересованным лицам перед развертыванием Azure Stack или для расследования проблем, обнаруженных при проверке. Оба файла сохраняют результаты каждой очередной проверки. В отчете содержатся подтверждения команды развертывания по конфигурации удостоверений. Файл журнала поможет командам развертывания или поддержки диагностировать проблемы с проверкой. 

По умолчанию оба файла сохраняются в *C:\Users\<имя_пользователя>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - При запуске проверки можно указать в конце командной строки параметр **-OutputPath** ***&lt;путь&gt;***, чтобы задать другое расположение отчетов.   
 - Укажите параметр **-CleanReport** в конце команды, чтобы удалить из файла *AzsReadinessCheckerReport.json* сведения.  о предыдущих запусках инструмента. Дополнительные сведения об отчетах проверки Azure Stack можно найти [здесь](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Ошибки при проверке
Если проверка завершается ошибкой, сведения о сбое отображаются в окне PowerShell. Также инструмент записывает сведения в файл AzsReadinessChecker.log.

Следующие примеры дают некоторые рекомендации по распространенным ошибкам проверки.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Пользователь должен быть владельцем подписки   
![Владелец подписки](./media/azure-stack-validate-registration/subscription-owner.png)
**Причина** — учетная запись не является администратором подписки Azure.   

**Разрешение**. Используйте учетную запись администратора подписки Azure, которой будет выставлен счет за использование развертывания Azure Stack.


### <a name="expired-or-temporary-password"></a>Пароль с истекшим сроком действия или временный пароль 
![истек срок действия пароля](./media/azure-stack-validate-registration/expired-password.png)
**Причина** — вход с учетной записью невозможен, так как срок действия пароля истек или использовался временный пароль.     

**Разрешение** — в PowerShell выполните инструкции на экране и следуйте им, чтобы сбросить пароль. 
  > `Login-AzureRMAccount` 

Также вы можете войти с этой учетной записью в https://portal.azure.com, и тогда пользователь должен будет сменить пароль.


### <a name="microsoft-accounts-are-not-supported-for-registration"></a>Учетные записи Майкрософт не поддерживаются для регистрации  
![неподдерживаемая учетная запись](./media/azure-stack-validate-registration/unsupported-account.png)
**Причина** — была указана учетная запись Майкрософт (такая как Outlook.com или Hotmail.com).  Эти учетные записи Майкрософт не поддерживаются.

**Разрешение** — используйте учетную запись и подписку из поставщика облачных служб или соглашения Enterprise. 


### <a name="unknown-user-type"></a>Неизвестный тип пользователя  
![неизвестный пользователь](./media/azure-stack-validate-registration/unknown-user.png)
**Причина** — вход с учетной записью в указанную среду Azure Active Directory невозможен. В нашем примере параметр *AzureChinaCloud* имеет значение *AzureEnvironment*.  

**Разрешение** — убедитесь, что учетная запись существует в указанном окружении Azure. Выполните в PowerShell следующую команду, чтобы проверить допустимость учетной записи для конкретного окружения.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Дальнейшие действия
[Проверка идентификатора Azure](azure-stack-validate-identity.md)
[Просмотр отчета о готовности](azure-stack-validation-report.md)
[Общие рекомендации по интеграции Azure Stack](azure-stack-datacenter-integration.md)

