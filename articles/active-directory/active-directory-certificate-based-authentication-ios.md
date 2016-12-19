---
title: "Приступая к работе с аутентификацией в iOS на основе сертификата | Документация Майкрософт"
description: "Узнайте, как настроить аутентификацию на основе сертификата в решениях на устройствах iOS."
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: 26a6fc54-0153-44fb-b970-9b432c99e9f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b6fde282a50dff4b3d9f0ac22a88728e30b5bc44


---
# <a name="get-started-with-certificate-based-authentication-on-ios---public-preview"></a>Приступая к работе с аутентификацией в iOS на основе сертификата — общедоступная предварительная версия
> [!div class="op_single_selector"]
> * [iOS](active-directory-certificate-based-authentication-ios.md)
> * [Android](active-directory-certificate-based-authentication-android.md)
> 
> 

В этом разделе показано, как настроить и использовать аутентификацию на основе сертификата (CBA) на устройствах iOS для пользователей клиентов в Office 365 (планы "Корпоративный", "Бизнес" и "Для образования"). 

CBA позволяет Azure Active Directory выполнять аутентификацию с помощью сертификата клиента на устройстве Android или iOS при подключении учетной записи Exchange Online к: 

* мобильным приложениям Office, таким как Microsoft Outlook и Microsoft Word;   
* клиентам Exchange ActiveSync (EAS). 

Настройка данной функции избавляет от необходимости ввода имени пользователя и пароля в определенных почтовых клиентах и приложениях Microsoft Office на мобильных устройствах. 

## <a name="supported-scenarios-and-requirements"></a>Поддерживаемые сценарии и требования
### <a name="general-requirements"></a>Общие требования
Для всех сценариев, описанных в этой статье, должны выполняться перечисленные ниже требования.  

* Доступ к центрам сертификации для выдачи сертификатов клиентов.  
* Центры сертификации должны быть настроены в Azure Active Directory. Подробные инструкции по настройке см. в разделе [Приступая к работе](#getting-started).  
* Корневой центр сертификации и все промежуточные центры сертификации должны быть настроены в Azure Active Directory.  
* Каждый центр сертификации должен иметь список отзыва сертификатов (CRL), на который можно сослаться с помощью URL-адреса для Интернета.  
* Для аутентификации клиента должен быть выдан сертификат клиента.  
* Только для клиентов Exchange ActiveSync: в сертификате клиента в поле "Альтернативное имя субъекта" в качестве значения имени субъекта или имени RFC822 должен быть указан маршрутизируемый адрес электронной почты пользователя в Exchange Online. Azure Active Directory сопоставляет значение RFC822 с атрибутом прокси-адреса в каталоге.  

### <a name="office-mobile-applications-support"></a>Поддержка мобильных приложений Office
| Приложения | Поддержка |
| --- | --- |
| Word/Excel/PowerPoint |![Проверка][1] |
| OneNote |![Проверка][1] |
| OneDrive |![Проверка][1] |
| Outlook |![Проверка][1] |
| Yammer |![Проверка][1] |
| Skype для бизнеса |Скоро |

### <a name="requirements"></a>Требования
Устройство должно иметь операционную систему iOS 9 и более поздней версии. 

Необходимо настроить сервер федерации.  

Для приложений Office на iOS требуется Azure Authenticator.  

Чтобы служба Azure Active Directory могла отзывать сертификат клиента, маркер AD FS должен иметь следующие утверждения:  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
   (серийный номер сертификата клиента); 
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
   (строка для издателя сертификата клиента). 

Azure Active Directory добавляет эти утверждения в маркер обновления, если они доступны в маркере AD FS (или любом другом токене SAML). Когда требуется проверить маркер обновления, эта информация используется для проверки отзыва. 

Рекомендуется обновить страницы ошибок AD FS следующими сведениями:

* требованием установки Azure Authenticator для iOS;
* инструкциями о получении сертификата пользователя. 

Дополнительные сведения см. в разделе [Настройка страниц входа AD FS](https://technet.microsoft.com/library/dn280950.aspx).  

### <a name="exchange-activesync-clients-support"></a>Поддержка клиентов Exchange ActiveSync
В iOS версии 9 и выше поддерживается собственный почтовый клиент iOS. Чтобы определить, поддерживается ли эта функция во всех остальных приложениях Exchange ActiveSync, обратитесь к разработчику приложения.  

## <a name="getting-started"></a>Приступая к работе
Чтобы приступить к работе, необходимо настроить центры сертификации в Azure Active Directory. Для каждого центра сертификации передайте следующие данные: 

* открытую часть сертификата в формате *CER* ; 
* URL-адреса для Интернета, где находятся списки отзыва сертификатов (CRL).

Ниже приведена схема для центра сертификации. 

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


Чтобы передать эти данные, можно воспользоваться модулем Azure AD с помощью Windows PowerShell.  
Ниже приведены примеры добавления, удаления и изменения центра сертификации. 

### <a name="configuring-your-azure-ad-tenant-for-certificate-based-authentication"></a>Настройка клиента Azure AD для аутентификации на основе сертификата
1. Запустите Windows PowerShell с правами администратора. 
2. Установите модуль Azure AD. Необходимо установить версию [1.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0) или более позднюю.  
   
        Install-Module -Name AzureADPreview –RequiredVersion 1.1.143.0 
3. Подключитесь к целевому клиенту: 
   
        Connect-AzureAD 

### <a name="adding-a-new-certificate-authority"></a>Добавление нового центра сертификации
1. Задайте различные свойства центра сертификации и добавьте его в Azure Active Directory: 
   
        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 
2. Получите сведения о центрах сертификации: 
   
        Get-AzureADTrustedCertificateAuthority 

### <a name="retrieving-the-list-certificate-authorities"></a>Получение списка центров сертификации
Получите текущий список центров сертификации, хранящихся в Azure Active Directory для вашего клиента: 

        Get-AzureADTrustedCertificateAuthority 


### <a name="removing-a-certificate-authority"></a>Удаление центра сертификации
1. Получите сведения о центрах сертификации: 
   
     $c=Get-AzureADTrustedCertificateAuthority 
2. Удалите сертификат для центра сертификации: 
   
        Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 

### <a name="modfiying-a-certificate-authority"></a>Изменение центра сертификации
1. Получите сведения о центрах сертификации: 
   
     $c=Get-AzureADTrustedCertificateAuthority 
2. Измените свойства центра сертификации: 
   
        $c[0].AuthorityType=1 
3. Укажите **центр сертификации**: 
   
        Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 

## <a name="testing-office-mobile-applications"></a>Тестирование мобильных приложений Office
Чтобы протестировать аутентификацию на основе сертификата в мобильном приложении Office, выполните описанные ниже действия. 

1. На тестовом устройстве установите мобильное приложение Office (например, OneDrive) из App Store.
2. Убедитесь, что для тестового устройства подготовлен сертификат пользователя. 
3. Запустите приложение. 
4. Введите имя пользователя, а затем выберите сертификат пользователя, который хотите использовать. 

Вы должны без проблем войти в систему. 

## <a name="testing-exchange-activesync-client-applications"></a>Тестирование клиентских приложений Exchange ActiveSync
Для доступа к Exchange ActiveSync с использованием аутентификации на основе сертификата приложению должен быть доступен профиль EAS, содержащий сертификат клиента. В профиле EAS должны содержаться следующие сведения:

* сертификат пользователя, который будет использоваться для аутентификации; 
* конечная точка EAS должна иметь значение outlook.office365.com (так как в настоящее время эта функция поддерживается только в мультитенантной среде Exchange Online).

Профиль EAS можно настроить и поместить на устройство с помощью системы MDM, такой как Intune, либо вручную поместить сертификат в профиль EAS на устройстве.  

### <a name="testing-eas-client-applications-on-ios"></a>Тестирование клиентских приложений EAS на iOS
Чтобы протестировать аутентификацию на основе сертификата в собственном почтовом приложении, работающем на iOS 9 или более поздней версии, выполните описанные ниже действия. 

1. Настройте профиль EAS, удовлетворяющий изложенным выше требованиям. 
2. Установите профиль на устройство iOS (с помощью системы MDM, такой как Intune, или приложения Apple Configurator).
3. Когда профиль будет полностью установлен, откройте собственное почтовое приложение и убедитесь, что почта синхронизируется.

## <a name="revocation"></a>Запрет доступа
Чтобы отозвать сертификат клиента, Azure Active Directory извлекает список отзыва сертификатов (CRL) из URL-адресов, переданных вместе с информацией центра сертификации, и кэширует его. Метка времени последней публикации (свойство**Дата вступления в силу** ) в списке отзыва сертификатов обеспечивает допустимость этого списка. Список отзыва сертификатов периодически опрашивается для отзыва доступа к сертификатам, которые числятся в этом списке.

Если требуется более быстрый отзыв (например, если пользователь потерял устройство), то маркер авторизации пользователя можно сделать недействительным. Чтобы сделать маркер авторизации недействительным, с помощью Windows PowerShell определите поле **StsRefreshTokenValidFrom** для этого пользователя. Поле **StsRefreshTokenValidFrom** необходимо обновить для каждого пользователя, доступ для которого будет отозван.

Чтобы отзыв оставался в силе, для свойства **Дата вступления в силу** списка отзыва сертификатов необходимо указать дату, которая наступит после даты, заданной в поле **StsRefreshTokenValidFrom**, а также убедиться, что этот сертификат есть в списке отзыва сертификатов.

Ниже описан процесс обновления и аннулирования маркера авторизации с помощью поля **StsRefreshTokenValidFrom** . 

1. Используя учетные данные администратора, подключитесь к службе MSOL: 
   
        $msolcred = get-credential 
        connect-msolservice -credential $msolcred 
2. Получите текущее значение StsRefreshTokensValidFrom для пользователя: 
   
     $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`   $user.StsRefreshTokensValidFrom 
3. Настройте новое значение StsRefreshTokensValidFrom для пользователя, равное текущей метке времени: 
   
     Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

Задаваемая дата должна быть в будущем. Если дата не в будущем, свойство **StsRefreshTokensValidFrom** не будет задано. Если дата в будущем, для **StsRefreshTokensValidFrom** задается актуальное время (не дата, указанная командой Set-MsolUser). 

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png



<!--HONumber=Nov16_HO3-->


