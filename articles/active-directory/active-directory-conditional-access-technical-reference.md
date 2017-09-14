---
title: "Техническая информация об условном доступе в Azure Active Directory | Документация Майкрософт"
description: "С условным контролем доступа при проверке подлинности пользователя и перед предоставлением ему доступа к приложению Azure Active Directory проверяет определенные условия, которые вы можете выбрать. Если эти условия выполняются, пользователь проходит проверку подлинности, и ему дается доступ к приложению."
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
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: f3d8bdbfc29ca1008006837512c0e6ae8cb8f6fe
ms.contentlocale: ru-ru
ms.lasthandoff: 09/02/2017

---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Техническая информация об условном доступе в Azure Active Directory.

Благодаря [условному доступу Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) можно настроить доступ авторизованных пользователей к вашим ресурсам.  
Этот раздел содержит информацию о поддержке для следующих элементов политики условного доступа: 

- Назначения облачных приложений

- условие платформы устройства; 

- условие клиентских приложений;

- требование утвержденного клиентского приложения. 



## <a name="cloud-apps-assignments"></a>Назначения облачных приложений

При настройке политики условного доступа необходимо [выбрать облачные приложения, к которым она применяется](active-directory-conditional-access-azure-portal.md#who). 

![Контроль](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-apps"></a>Облачные приложения Майкрософт

Политики условного доступа можно назначить для следующих облачных приложений от корпорации Майкрософт:

- Удаленное приложение Azure

- Dynamics CRM

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (включая OneDrive для бизнеса)

- Microsoft Power BI 

- Visual Studio Team Services

- Microsoft Teams


### <a name="other-apps"></a>Другие приложения 

Помимо облачных приложений Майкрософт, политику условного доступа можно назначить для следующих типов облачных приложений:

- приложения, подключенные к Azure Active Directory (Azure AD);

- предварительно интегрированное федеративное приложение по модели "Программное обеспечение как услуга" (SaaS);

- приложения, использующие единый вход с помощью пароля;

- бизнес-приложения;

- приложения, использующие прокси приложения Azure AD. 


## <a name="device-platforms-condition"></a>Условие платформы устройства

В политике условного доступа можно настроить условие платформы устройства для привязки политики к клиентской операционной системе.

![Контроль](./media/active-directory-conditional-access-technical-reference/41.png)

Условный доступ Azure AD поддерживает следующие платформы устройств:

- Android

- iOS

- Windows Phone

- Windows

- macOS (предварительная версия).



## <a name="client-apps-condition"></a>Условие клиентских приложений 

При настройке политики условного доступа можно задать [условие клиентских приложений](active-directory-conditional-access-azure-portal.md#client-apps). Условие клиентских приложений дает возможность предоставить или заблокировать доступ, если была предпринята попытка доступа из этих типов клиентских приложений:

- "Обзор"
- мобильные и классические приложения.

![Контроль](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Поддерживаемые браузеры 

Если для предоставления доступа к ресурсам в политике условного доступа выбран *браузер*, доступ можно получить только с помощью поддерживаемого браузера. При попытке доступа с помощью неподдерживаемого браузера он блокируется.

![Поддерживаемые браузеры](./media/active-directory-conditional-access-technical-reference/05.png)

В политике условного доступа поддерживаются следующие браузеры: 


| ОС                     | Браузеры                 | Поддержка     |
| :--                    | :--                      | :-:         |
| Windows 10                 | IE, Edge                 | ![Проверка][1] |
| Windows 10                 | Chrome                   | Предварительный просмотр     |
| Windows 8, Windows 8.1            | Internet Explorer, Chrome               | ![Проверка][1] |
| Windows 7                  | Internet Explorer, Chrome               | ![Проверка][1] |
| iOS                    | Safari                   | ![Проверка][1] |
| Android                | Chrome                   | ![Проверка][1] |
| Windows Phone          | IE, Edge                 | ![Проверка][1] |
| Windows Server 2016    | IE, Edge                 | ![Проверка][1] |
| Windows Server 2016    | Chrome                   | Скоро |
| Windows Server 2012 R2 | Internet Explorer, Chrome               | ![Проверка][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome               | ![Проверка][1] |
| MacOS                 | Safari                   | ![Проверка][1] |
| MacOS                 | Chrome                   | Скоро |

> [!NOTE]
> Для поддержки Chrome необходимо использовать Windows 10 Creators Update и установить расширение, которое можно найти [здесь](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).


### <a name="supported-mobile-apps-and-desktop-clients"></a>Поддерживаемые мобильные приложения и настольные клиенты

Если в политике условного доступа для предоставления доступа к ресурсам выбрать **Мобильные приложения и настольные клиенты**, доступ предоставляется, только он получен с помощью поддерживаемых мобильных приложений или настольных клиентов. При попытке доступа с помощью неподдерживаемого мобильного приложения или настольного клиента он блокируется.

![Контроль](./media/active-directory-conditional-access-technical-reference/06.png)

Следующие мобильные приложения и настольные клиенты поддерживают условный доступ к Office 365 и другим служебным приложениям, подключенным к Azure AD:


| Клиентские приложения| Целевая служба| Платформа |
| --- | --- | --- |
| MFA и политика расположения для приложений Политики на основе устройств не поддерживаются.| Все службы приложения "Мои приложения"| Android и iOS|
| Azure RemoteApp| Удаленная служба приложений Azure| Windows 10, Windows 8.1, Windows 7, iOS, Android и Mac OS X|
| Приложение Dynamics CRM| Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS и Android|
| Microsoft Teams Services — контролируют все службы, которые поддерживают Microsoft Teams, и все их клиентские приложения: для Windows Desktop, iOS, Android, WP, а также веб-клиент.| Microsoft Teams| Windows 10, Windows 8.1, Windows 7, iOS и Android|
| Приложения "Почта", "Календарь" и "Люди", Outlook 2016, Outlook 2013 (с современной проверкой подлинности), Skype для бизнеса (с современной проверкой подлинности)| Office 365 Exchange Online| Windows 10|
| Outlook 2016, Outlook 2013 (с современной проверкой подлинности), Skype для бизнеса (с современной проверкой подлинности)| Office 365 Exchange Online| Windows 8.1, Windows 7|
| Приложение Outlook Mobile| Office 365 Exchange Online| iOS|
| Outlook 2016 (Office для macOS)| Office 365 Exchange Online| Mac OS X|
| Приложения Office 2016, универсальные приложения Office, Office 2013 (с современной проверкой подлинности), клиент синхронизации OneDrive (см. [заметки](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)); поддержка групп Office и SharePoint ожидается в будущем| Office 365 SharePoint Online| Windows 10|
| Приложения Office 2016, Office 2013 (с современной проверкой подлинности), клиент синхронизации OneDrive (см. [заметки](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))| Office 365 SharePoint Online| Windows 8.1, Windows 7|
| Мобильные приложения Office| Office 365 SharePoint Online| iOS, Android|
| Office 2016 для macOS (только Word, Excel, PowerPoint, OneNote). Поддержку OneDrive для бизнеса планируется реализовать в будущем.| Office 365 SharePoint Online| Mac OS X|
| Приложение Office Yammer| Office 365 Yammer| Windows 10, iOS, Android|
| Приложение PowerBI. Приложение Power BI для Android в настоящее время не поддерживает условный доступ на основе устройств.| Служба PowerBI| Windows 10, Windows 8.1, Windows 7 и iOS.|
| Приложение Visual Studio Team Services| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS и Android|



## <a name="approved-client-app-requirement"></a>Требование утвержденного клиентского приложения 

При настройке политики условного доступа можно выбрать требование для предоставления доступа только в том случае, если была предпринята попытка подключения с помощью утвержденного клиентского приложения. 

![Контроль](./media/active-directory-conditional-access-technical-reference/21.png)

Утвержденные клиентские приложения для этого параметра:

- Microsoft Excel;

- Microsoft OneDrive;

- Microsoft Outlook;

- Microsoft OneNote;

- Microsoft PowerPoint;

- Microsoft SharePoint

- Microsoft Skype для бизнеса;

- Microsoft Teams

- Microsoft Visio;

- Microsoft Word.


**Примечания:**

- Эти приложения поддерживают управление мобильными приложениями Microsoft Intune (MAM).

- Требования:

    - поддерживает только IOS и Android как выбранное [условие платформы устройств](#device-platforms-condition); 

    - не поддерживает **браузер** как выбранное [условие клиентского приложения](#supported-browsers); 
    
    - заменяет собой [условие клиентского приложения](#supported-mobile-apps-and-desktop-clients) **Мобильные приложения и настольные клиенты**, если оно выбрано.  


## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения об условном доступе см. в статье [Условный доступ в Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- Если вы готовы к настройке политик условного доступа для своей среды, см. статью [Рекомендации по работе с условным доступом в Azure Active Directory](active-directory-conditional-access-best-practices.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png



