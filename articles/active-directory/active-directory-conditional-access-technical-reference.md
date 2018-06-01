---
title: Справочник по параметрам условного доступа в Azure Active Directory | Документация Майкрософт
description: Эта статья содержит обзор поддерживаемых параметров в политике условного доступа Azure Active Directory.
services: active-directory.
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 341142222fb1440cf5118e6d10f5a254e73d53d9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258113"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Справочник по параметрам условного доступа в Azure Active Directory

Благодаря [условному доступу Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) можно контролировать доступ авторизованных пользователей к своим ресурсам.   

Эта статья содержит информацию о поддержке следующих параметров конфигурации в политике условного доступа: 

- назначения облачных приложений;

- условие платформы устройства; 

- условие клиентских приложений;

- требование утвержденных клиентских приложений.


Если это не та информация, которую вы ищете, оставьте комментарий в конце этой статьи.

## <a name="cloud-apps-assignments"></a>Назначения облачных приложений

С помощью политик условного доступа можно управлять доступом пользователей к [облачным приложениям](active-directory-conditional-access-conditions.md#cloud-apps). При настройке политики условного доступа необходимо выбрать как минимум одно облачное приложение, к которому она применяется. 

![Выбор облачных приложений для политики](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Облачные приложения Майкрософт

Политики условного доступа можно назначить для следующих облачных приложений от корпорации Майкрософт:

- Azure Information Protection — [узнайте больше](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)

- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (включая OneDrive для бизнеса и Project Online)

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Microsoft Teams


### <a name="other-applications"></a>Другие приложения 

Помимо облачных приложений Майкрософт, политику условного доступа можно назначить для следующих типов облачных приложений:

- приложения, подключенные к Azure AD;

- предварительно интегрированное федеративное приложение SaaS;

- приложения, использующие единый вход с помощью пароля;

- бизнес-приложения;

- приложения, использующие прокси приложения Azure AD.


## <a name="device-platform-condition"></a>условие платформы устройства;

В политике условного доступа можно настроить условие платформы устройства для привязки политики к клиентской операционной системе. Условный доступ Azure AD поддерживает следующие платформы устройств:

- Android

- iOS

- Windows Phone

- Windows

- macOS


![Привязка политики доступа к клиентской ОС](./media/active-directory-conditional-access-technical-reference/41.png)





## <a name="client-apps-condition"></a>Условие клиентских приложений 

В политике условного доступа можно настроить условие [Клиентские приложения](active-directory-conditional-access-conditions.md#client-apps), чтобы привязать ее к клиентскому приложению, которое инициировало попытку доступа. Условие клиентских приложений можно задать, чтобы предоставить или заблокировать доступ, если была предпринята попытка доступа из приведенных ниже типов клиентских приложений:

- "Обзор"
- мобильные и классические приложения.

![Управление доступом к клиентским приложениям](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Поддерживаемые браузеры 

В политике условного доступа можно выбрать параметр **Браузеры**, чтобы в качестве клиентского приложения указать браузеры.

![Управление доступом поддерживаемых браузеров](./media/active-directory-conditional-access-technical-reference/05.png)

Этот параметр работает со всеми браузерами. Но чтобы выполнить условия политики устройств, например требование соответствия, поддерживаются следующие операционные системы и браузеры:


| ОС                     | Браузеры                            | Поддержка     |
| :--                    | :--                                 | :-:         |
| Windows 10             | Internet Explorer, Edge, Chrome     | ![Проверка][1] |
| Windows 8, Windows 8.1        | Internet Explorer, Chrome           | ![Проверка][1] |
| Windows 7              | Internet Explorer, Chrome           | ![Проверка][1] |
| iOS                    | Safari, Intune Managed Browser      | ![Проверка][1] |
| Android                | Chrome, Intune Managed Browser      | ![Проверка][1] |
| Windows Phone          | Internet Explorer, Edge             | ![Проверка][1] |
| Windows Server 2016    | Internet Explorer, Edge             | ![Проверка][1] |
| Windows Server 2016    | Chrome                              | Скоро |
| Windows Server 2012 R2 | Internet Explorer, Chrome           | ![Проверка][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome           | ![Проверка][1] |
| macOS                  | Chrome, Safari                      | ![Проверка][1] |



#### <a name="chrome-support"></a>Поддержка Chrome

Для поддержки Chrome в **Windows 10 Creators Update (версия 1703)** или более поздней версии установите [это расширение](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Чтобы автоматически развернуть это расширение в браузере Chrome, создайте следующий раздел реестра:

|    |    |
|--- | ---|
|Путь | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
|ИМЯ | 1 |
|type | REG_SZ (String) |
|Данные | ppnbnpeolgkicgegkbkbjmhlideopiji;https://clients2.google.com/service/update2/crx

Для поддержки Chrome в **Windows 8.1 и Windows 7** создайте следующий раздел реестра:

|    |    |
|--- | ---|
|Путь | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
|ИМЯ | 1 |
|type | REG_SZ (String) |
|Данные | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}|

Эти браузеры поддерживают аутентификацию устройств, позволяя идентифицировать устройство и проверить, соответствует ли оно политике. Если браузер работает в частном режиме, проверка устройства завершается ошибкой. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>Поддерживаемые мобильные приложения и классические клиенты

В политике условного доступа можно выбрать параметр **Мобильные приложения и настольные клиенты**, чтобы в качестве клиентского приложения указать мобильные приложения и классические клиенты.


![Управление доступом поддерживаемых мобильных приложений или классических клиентов](./media/active-directory-conditional-access-technical-reference/06.png)


Этот параметр влияет на попытки доступа, предпринимаемые из следующих мобильных приложений и классических клиентов. 


|Клиентские приложения|Целевая служба|платформа|
|---|---|---|
|Azure RemoteApp|Удаленная служба приложений Azure|Windows 10, Windows 8.1, Windows 7, iOS, Android и Mac OS X|
|Приложение Dynamics CRM|Dynamics CRM|Windows 10, Windows 8.1, iOS и Android|
|Приложения Почта, Календарь и Люди, Outlook 2016, Outlook 2013 (с современной аутентификацией)|Office 365 Exchange Online|Windows 10|
|MFA и политика расположения для приложений Политики на основе устройств не поддерживаются. |Все службы приложения "Мои приложения"|Android и iOS|
|Microsoft Teams Services — контролируют все службы, которые поддерживают Microsoft Teams, и все их клиентские приложения: для Windows Desktop, iOS, Android, WP, а также веб-клиент.|Microsoft Teams|Windows 10, Windows 8.1, Windows 7, iOS, Android и macOS |
|Приложения Office 2016, Office 2013 (с современной проверкой подлинности), клиент синхронизации OneDrive (см. [заметки](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))|Office 365 SharePoint Online|Windows 8.1, Windows 7|
|Приложения Office 2016, универсальные приложения Office, Office 2013 (с современной проверкой подлинности), клиент синхронизации OneDrive (см. [заметки](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)); поддержка групп Office и SharePoint ожидается в будущем|Office 365 SharePoint Online|Windows 10|
|Office 2016 для macOS (только Word, Excel, PowerPoint, OneNote). Поддержку OneDrive для бизнеса планируется реализовать в будущем.|Office 365 SharePoint Online|Mac OS X|
|Мобильные приложения Office|Office 365 SharePoint Online|Android, iOS|
|Приложение Office Yammer|Office 365 Yammer|Windows 10, iOS, Android|
|Outlook 2016 (Office для macOS)|Office 365 Exchange Online|Mac OS X|
|Outlook 2016, Outlook 2013 (с современной проверкой подлинности), Skype для бизнеса (с современной проверкой подлинности)|Office 365 Exchange Online|Windows 8.1, Windows 7|
|Приложение Outlook Mobile|Office 365 Exchange Online|Android, iOS|
|Приложение PowerBI|Служба PowerBI|Windows 10, Windows 8.1, Windows 7, Android и iOS|
|Skype для бизнеса|Office 365 Exchange Online|Android, iOS |
|Приложение Visual Studio Team Services|Visual Studio Team Services|Windows 10, Windows 8.1, Windows 7, iOS и Android|



## <a name="approved-client-app-requirement"></a>Требование утвержденного клиентского приложения 

В политике условного доступа можно указать, что попытка доступа к выбранным облачным приложениям должна предприниматься из утвержденного клиентского приложения. 

![Управление доступом утвержденных клиентских приложений](./media/active-directory-conditional-access-technical-reference/21.png)

Этот параметр применяется к следующим клиентским приложениям.


- Microsoft Intune Managed Browser
- Microsoft PowerBI;
- Microsoft Invoicing.
- Microsoft Launcher;
- Microsoft Azure Information Protection.
- Microsoft Excel
- Microsoft Kaizala 
- Microsoft OneDrive
- Microsoft OneNote;
- Microsoft Outlook
- Планировщик (Майкрософт);
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype для бизнеса;
- Microsoft StaffHub
- Microsoft Teams
- Microsoft Visio;
- Microsoft Word



**Примечания**

- Утвержденные клиентских приложения поддерживают функцию управления мобильными приложениями Intune.

- Функция **Требовать утвержденное клиентское приложение**:

    - поддерживает только iOS и Android для [условия платформы устройства](#device-platforms-condition);


## <a name="next-steps"></a>Дополнительная информация

- Общие сведения об условном доступе см. в статье [Условный доступ в Azure Active Directory](active-directory-conditional-access-azure-portal.md).
- Если вы готовы к настройке политик условного доступа для своей среды, прочитайте статью [Рекомендации по работе с условным доступом в Azure Active Directory](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png


