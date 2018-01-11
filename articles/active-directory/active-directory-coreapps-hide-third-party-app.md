---
title: "Скрыть приложения с пользователем в Azure Active Directory | Документы Microsoft"
description: "Как скрыть приложения с пользователем в Azure Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: billmath
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 667fdd45bc9eb1f01ce3883006bb29274478cb83
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Скрыть приложения с пользователем в Azure Active Directory

Если у вас есть приложение, которое не требуется отображать на панели доступа пользователей и средства запуска Office 365, есть параметр для скрытия этой плитки приложения. Этот параметр доступен только для сторонних приложений (приложения, не опубликованные корпорацией Майкрософт). Когда вы скроете приложение, у пользователей останутся разрешения для этого приложения, но оно не будет отображаться в их средствах запуска. Необходимо иметь соответствующие разрешения для управления корпоративным приложением, а также права глобального администратора для доступа к каталогу. 

## <a name="hiding-an-application-from-users-end-user-experiences"></a>Скрытие приложения от работы пользователя конечного пользователя
Выполните следующие действия, чтобы скрыть приложения из средства запуска приложений Office 365 и панели доступа пользователя

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Как скрыть стороннее приложение на панели доступа пользователя и в средствах запуска приложений O365?

1.  Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора каталога.
2.  Выберите **Другие службы**, введите **Azure Active Directory** в текстовое поле, а затем нажмите клавишу **ВВОД**.
3.  На экране ***Azure Active Directory —* имя_каталога** (то есть на экране Azure AD для каталога, которым вы управляете) выберите **Корпоративные приложения**.
![Корпоративные приложения](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  На экране **Корпоративные приложения** выберите **Все приложения**. Отобразится список приложений, которыми можно управлять.
5.  На экране **Корпоративные приложения — Все приложения** выберите приложение.</br>
![Корпоративные приложения](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  На экране ***имя_приложения*** (то есть на экране с именем выбранного приложения в заголовке) выберите "Свойства".
7.  На экране ***имя_приложения* — Свойства** для вопроса **Видно пользователям?** выберите ответ **Да**.
![Корпоративные приложения](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  Щелкните **Сохранить** .

## <a name="next-steps"></a>Дальнейшие действия
* [Просмотр всех моих групп](active-directory-groups-view-azure-portal.md)
* [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)
* [Удаление назначения пользователя или группы из корпоративного приложения](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Изменение имени или логотипа корпоративного приложения](active-directory-coreapps-change-app-logo-user-azure-portal.md)
