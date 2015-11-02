<properties
	pageTitle="Предварительная версия доменных служб Azure Active Directory: приступая к работе | Microsoft Azure"
	description="Приступая к работе с доменными службами Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="maheshu"/>

# Доменные службы Azure AD *(Предварительная версия)* — Приступая к работе

## Шаг 5. Включение синхронизации паролей
После включения доменных служб Azure AD для вашего клиента Azure AD следующим шагом является включение синхронизации паролей. Это позволяет пользователям входить в домен, используя свои корпоративные учетные данные.

Этапы настройки различаются в зависимости от того, является ли ваша организация только облачным клиентом Azure AD или для нее настроена синхронизация вашего локального каталога с помощью Azure AD Connect.

### Включение синхронизации паролей только для облачных клиентов
Если ваша организация является только облачным клиентом Azure AD, пользователям, которые захотят воспользоваться доменными службами Azure AD, потребуется изменить свои пароли. При этом необходимо сформировать в Azure AD хэши учетных данных, необходимые доменным службам Azure AD для проверки подлинности Kerberos и NTLM. Вы можете либо принудительно сделать так, чтобы срок действия паролей для всех пользователей в клиенте доменных служб Azure AD истек, либо отправить пользователям инструкции по изменению паролей.

Инструкции по изменению паролей, которые необходимо отправить пользователям, таковы.

1. Перейдите на страницу панели доступа Azure AD для вашей организации. Обычно она доступна по адресу [http://myapps.microsoft.com](http://myapps.microsoft.com).
2. Выберите вкладку **Профиль** на этой странице.
3. Щелкните элемент **Изменить пароль** на этой странице, чтобы изменить пароль.

    ![Создание виртуальной сети для доменных служб Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. Откроется страница **изменения пароля**. Затем пользователь может ввести свой текущий (старый) пароль и продолжить изменение пароля.

    ![Создание виртуальной сети для доменных служб Azure AD.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

После изменения паролей новые пароли будут синхронизированы со службами домена Azure AD через короткое время. После окончания синхронизации паролей пользователи смогут входить в домен с новыми паролями.


### Включение синхронизации паролей для синхронизированных клиентов
Если клиент Azure AD для вашей организации настроена на синхронизацию с локальным каталогом с помощью Azure AD Connect, понадобится настроить Azure AD Connect для синхронизации хэшей учетных данных, необходимых для проверки подлинности NTLM и Kerberos. По умолчанию эти хэши не синхронизируются с Azure AD, и для включения синхронизации с клиентом Azure AD необходимо выполнить следующие действия.

#### Установка Azure AD Connect

Необходимо установить общедоступную версию (GA) Azure AD на компьютере, присоединенном к домену. Если Azure AD Connect уже установлена, необходимо обновить ее до общедоступной версии.

  [Загрузка Azure AD Connect — общедоступная версия (GA)](http://download.microsoft.com/download/B/0/0/B00291D0-5A83-4DE7-86F5-980BC00DE05A/AzureADConnect.msi)

> [AZURE.WARNING]Общедоступная версия Azure AD Connect НЕОБХОДИМА для синхронизации устаревших учетных данных (требуются для проверки подлинности NTLM и Kerberos) с вашим клиентом Azure AD. В предыдущих выпусках Azure AD Connect эта возможность недоступна.

Инструкции по установке Azure AD Connect доступны в следующей статье: [Приступая к работе с Azure AD Connect](../active-directory/active-directory-aadconnect.md)


#### Включение синхронизации устаревших учетных данных для Azure AD

Включите синхронизацию устаревших учетных данных, необходимую для проверки подлинности NTLM в доменных службах Azure AD. Это можно сделать, создав следующий ключ реестра на компьютере, на который был установлен Azure AD Connect.

Создайте следующий параметр реестра типа DWORD и присвойте ему значение 1.

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\MSOLCoExistence\PasswordSync\EnableWindowsLegacyCredentialsSync

Set its value to 1.
```

#### Принудительная полная синхронизация паролей в Azure AD

Для включения принудительной полной синхронизации паролей и включения синхронизации хэшей паролей всех локальных пользователей (включая хэши учетных данных, необходимые для проверки подлинности NTLM или Kerberos) выполните следующий сценарий PowerShell в каждом лесу AD.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null  
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

В зависимости от размера каталога (число пользователей, групп и т. д.) синхронизация учетных данных с Azure AD, а затем с доменными службами Azure AD может занять некоторое время.

<!---HONumber=Oct15_HO4-->