---
title: "Скрытие сторонних приложений в пользовательском интерфейсе Azure Active Directory | Документация Майкрософт"
description: "Как скрыть сторонние приложения в пользовательском интерфейсе Azure Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
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
ms.openlocfilehash: 28b6885e7e74a44f4d00f4a804a93e74f6eec685
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2017
---
# <a name="hide-a-third-party-application-from-users-experience-in-azure-active-directory"></a>Скрытие сторонних приложений в пользовательском интерфейсе Azure Active Directory

Если вы используете стороннее приложение (опубликованное другим поставщиком, а не корпорацией Майкрософт), которое не должно отображаться для пользователей на панелях доступа и в средствах запуска Office 365, можете скрыть плитку этого приложения. Когда вы скроете приложение, у пользователей останутся разрешения для этого приложения, но оно не будет отображаться в их средствах запуска. Необходимо иметь соответствующие разрешения для управления корпоративным приложением, а также права глобального администратора для доступа к каталогу.

## <a name="hiding-a-users-access-panel"></a>Скрытие панели доступа для пользователя
Чтобы скрыть панель доступа для пользователя и средства запуска приложений Office 365, выполните приведенные ниже инструкции.

### <a name="how-do-i-hide-an-app-from-users-access-panel-and-o365-app-launchers"></a>Как скрыть приложение на панели доступа для пользователя и в средствах запуска приложений O365?

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
