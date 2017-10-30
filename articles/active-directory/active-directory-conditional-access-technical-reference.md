---
title: "Техническая информация об условном доступе в Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как использовать элементы управления условным доступом Azure Active Directory. Укажите условия для аутентификации пользователей и управления доступом к приложению. В случае соблюдения заданных условий пользователи проходят аутентификацию и получают доступ к вашему приложению."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 7f9574bc3d811d413b68d97ebe710858af8eb919
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Техническая информация об условном доступе в Azure Active Directory

Благодаря [условному доступу Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) можно точно настроить доступ авторизованных пользователей к своим ресурсам.  

Этот раздел содержит информацию о поддержке следующих параметров конфигурации для политики условного доступа: 

- назначения облачных приложений;

- условие платформы устройства; 

- условие клиентских приложений;

- требование утвержденных клиентских приложений.



## <a name="cloud-apps-assignments"></a>Назначения облачных приложений

При настройке политики условного доступа необходимо [выбрать облачные приложения, к которым она применяется](active-directory-conditional-access-azure-portal.md#who). 

![Выбор облачных приложений для политики](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Облачные приложения Майкрософт

Политики условного доступа можно назначить для следующих облачных приложений от корпорации Майкрософт:

- [Azure Information Protection](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (включая OneDrive для бизнеса)

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

В политике условного доступа можно настроить условие платформы устройства для привязки политики к клиентской операционной системе.

![Привязка политики доступа к клиентской ОС](./media/active-directory-conditional-access-technical-reference/41.png)

Условный доступ Azure AD поддерживает следующие платформы устройств:

- Android

- iOS

- Windows Phone

- Windows

- macOS (предварительная версия)



## <a name="client-apps-condition"></a>Условие клиентских приложений 

При настройке политики условного доступа можно [выбрать клиентские приложения](active-directory-conditional-access-azure-portal.md#client-apps) для условия клиентских приложений. Условие клиентских приложений можно задать, чтобы предоставить или заблокировать доступ, если была предпринята попытка доступа из приведенных ниже типов клиентских приложений:

- "Обзор"
- мобильные и классические приложения.

![Управление доступом к клиентским приложениям](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Поддерживаемые браузеры 

Управлять доступом браузера можно с помощью параметра **Браузера** в политике условного доступа. Доступ предоставляется только при попытке доступа с помощью поддерживаемого браузера. При попытке доступа с помощью неподдерживаемого браузера доступ блокируется.

![Управление доступом поддерживаемых браузеров](./media/active-directory-conditional-access-technical-reference/05.png)

В политике условного доступа поддерживаются следующие браузеры: 


| ОС                     | Браузеры                    | Поддержка     |
| :--                    | :--                         | :-:         |
| Windows 10             | Internet Explorer, Edge     | ![Проверка][1] |
| Windows 10             | Chrome                      | ![Проверка][1] |
| Windows 8, Windows 8.1        | Internet Explorer, Chrome   | ![Проверка][1] |
| Windows 7              | Internet Explorer, Chrome   | ![Проверка][1] |
| iOS                    | Safari, Intune Managed Browser                      | ![Проверка][1] |
| Android                | Chrome, Intune Managed Browser                      | ![Проверка][1] |
| Windows Phone          | Internet Explorer, Edge     | ![Проверка][1] |
| Windows Server 2016    | Internet Explorer, Edge     | ![Проверка][1] |
| Windows Server 2016    | Chrome                      | Скоро |
| Windows Server 2012 R2 | Internet Explorer, Chrome   | ![Проверка][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome   | ![Проверка][1] |
| macOS                  | Safari                      | ![Проверка][1] |
| macOS                  | Chrome                      | Скоро |

> [!NOTE]
> Для поддержки Chrome нужно использовать Windows 10 Creators Update (версия 1703) или более позднюю версию.<br>
> Можно установить [это расширение](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="supported-mobile-applications-and-desktop-clients"></a>Поддерживаемые мобильные приложения и классические клиенты

Для управления доступом приложений и клиентов используется параметр **Мобильные приложения и настольные клиенты** в политике условного доступа. Доступ предоставляется только при попытке доступа с помощью поддерживаемого мобильного приложения или классического клиента. При попытке доступа с помощью неподдерживаемого приложения или клиента доступ блокируется.

![Управление доступом поддерживаемых мобильных приложений или классических клиентов](./media/active-directory-conditional-access-technical-reference/06.png)

Следующие мобильные приложения и настольные клиенты поддерживают условный доступ к Office 365 и другим служебным приложениям, подключенным к Azure AD:


| Клиентские приложения| Целевая служба| Платформа |
| --- | --- | --- |
| Многофакторная идентификация Azure и политика расположения для приложений (политики на основе устройств не поддерживаются)| Все службы приложения "Мои приложения"| Android, iOS|
| Azure RemoteApp| Служба Azure RemoteApp| Windows 10, Windows 8.1, Windows 7, iOS, Android, macOS|
| Приложение Dynamics 365| Dynamics 365| Windows 10, Windows 8.1, Windows 7, iOS, Android|
| Команды Microsoft Office 365 (контролируют все службы, которые поддерживают Microsoft Teams, и все их клиентские приложения: для Windows Desktop, iOS, Android, Windows Phone, а также веб-клиент).| Microsoft Teams| Windows 10, Windows 8.1, Windows 7, iOS, Android|
| Приложения "Почта", "Календарь" и "Люди", Outlook 2016, Outlook 2013 (с современной проверкой подлинности), Skype для бизнеса (с современной проверкой подлинности)| Office 365 Exchange Online| Windows 10|
| Outlook 2016, Outlook 2013 (с современной проверкой подлинности), Skype для бизнеса (с современной проверкой подлинности)| Office 365 Exchange Online| Windows 8.1, Windows 7|
| Приложение Outlook Mobile| Office 365 Exchange Online| iOS|
| Outlook 2016 (Office для macOS)| Office 365 Exchange Online| macOS|
| Приложения Office 2016, универсальные приложения Office, Office 2013 (с современной аутентификацией), клиент синхронизации [OneDrive](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e); поддержка групп Office и приложения SharePoint ожидается в будущем| Office 365 SharePoint Online| Windows 10|
| Приложения Office 2016, Office 2013 (с современной аутентификацией), клиент синхронизации [OneDrive](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)| Office 365 SharePoint Online| Windows 8.1, Windows 7|
| Мобильные приложения Office| Office 365 SharePoint Online| iOS, Android|
| Office 2016 для macOS (поддерживаются только Word, Excel, PowerPoint и OneNote), поддержка OneDrive для бизнеса ожидается в будущем| Office 365 SharePoint Online| macOS|
| Приложение Office Yammer| Office 365 Yammer| Windows 10, iOS, Android|
| Приложение PowerBI (в настоящее время не поддерживается в Android)| Служба PowerBI| Windows 10, Windows 8.1, Windows 7 и iOS.|
| Приложение Visual Studio Team Services| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS, Android|



## <a name="approved-client-app-requirement"></a>Требование утвержденного клиентского приложения 

Для управления подключениями клиентов используется параметр **Требовать утвержденное клиентское приложение** в политике условного доступа. Доступ предоставляется только в том случае, если производится попытка подключения с помощью утвержденного клиентского приложения.

![Управление доступом утвержденных клиентских приложений](./media/active-directory-conditional-access-technical-reference/21.png)

Ниже приведены клиентские приложения, которые могут использоваться в качестве обязательных утвержденных клиентских приложений:

- Microsoft Excel

- Microsoft OneDrive;

- Microsoft Outlook;

- Microsoft OneNote;

- Microsoft PowerPoint

- Microsoft SharePoint

- Microsoft Skype для бизнеса;

- Microsoft Teams

- Microsoft Visio;

- Microsoft Word


**Примечания**

- Утвержденные клиентских приложения поддерживают функцию управления мобильными приложениями Intune.

- Функция **Требовать утвержденное клиентское приложение**:

    - поддерживает только iOS и Android для [условия платформы устройства](#device-platforms-condition);

    - не поддерживает параметр **Браузер** для [условия клиентских приложений](#supported-browsers);
    
    - переопределяет параметр **Мобильные приложения и настольные клиенты** для [условия клиентских приложений](#supported-mobile-apps-and-desktop-clients), если этот параметр выбран.


## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об условном доступе см. в статье [Условный доступ в Azure Active Directory](active-directory-conditional-access-azure-portal.md).
- Если вы готовы к настройке политик условного доступа для своей среды, прочитайте статью [Рекомендации по работе с условным доступом в Azure Active Directory](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png


