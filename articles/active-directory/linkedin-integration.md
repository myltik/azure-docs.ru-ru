---
title: "Включение и отключение интеграции с LinkedIn для приложений Office в Azure Active Directory | Документация Майкрософт"
description: "Сведения о том, как включить или отключить интеграцию с LinkedIn для приложений Майкрософт в Azure Active Directory."
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/30/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 5ebc44d0ef6200baeacf4f1f8c4371e2d1eed9db
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="linkedin-integration-for-office-applications"></a>Интеграция с LinkedIn для приложений Office
В этой статье объясняется, как ограничить число пользователей, которым предоставляется возможность интеграции с LinkedIn в Azure Active Directory (Azure AD). Интеграция с LinkedIn включается по умолчанию при ее добавлении к клиенту, благодаря чему пользователи могут обращаться к общедоступным данным LinkedIn в некоторых приложениях Майкрософт. Каждый пользователь может самостоятельно принять решение о подключении своей рабочей или учебной учетной записи к учетной записи LinkedIn.

> [!IMPORTANT]
> Интеграция с LinkedIn не развертывается одновременно для всех клиентов Azure AD. После развертывания в клиенте Azure интеграция с LinkedIn включается по умолчанию. Интеграция с LinkedIn недоступна для локальных, независимых и государственных клиентов. 

## <a name="linkedin-integration-from-the-user-perspective"></a>Интеграция с LinkedIn с точки зрения пользователей
Когда пользователи в вашей организации подключают учетную запись LinkedIn к своим рабочим или учебным учетным записям, [они тем самым разрешают LinkedIn предоставлять данные](https://www.linkedin.com/help/linkedin/answer/84077) для использования в приложениях и службах Майкрософт, которые предоставляет ваша организация. [Пользователи могут отключить учетные записи](https://www.linkedin.com/help/linkedin/answer/85097) и таким образом удалить разрешение для LinkedIn на совместное использование данных с Майкрософт. При интеграции с LinkedIn используются общедоступные сведения профиля LinkedIn. С помощью параметров конфиденциальности LinkedIn [пользователи могут управлять отображением сведений профиля LinkedIn](https://www.linkedin.com/help/linkedin/answer/83), в том числе возможностью просмотра профиля в приложениях Майкрософт.

## <a name="privacy-considerations"></a>Рекомендации по конфиденциальности
Включение интеграции с LinkedIn в Azure AD предоставляет приложениям и службам Майкрософт доступ к некоторым сведениям о пользователях LinkedIn. Прочитайте [заявление о конфиденциальности Майкрософт](https://privacy.microsoft.com/privacystatement/), чтобы получить дополнительные сведения об аспектах конфиденциальности при включении интеграции с LinkedIn в Azure AD. 

## <a name="manage-linkedin-integration"></a>Управление интеграцией с LinkedIn
Для предприятий интеграция с LinkedIn в Azure AD включена по умолчанию. Включение интеграции с LinkedIn позволяет всем пользователям в вашей организации использовать функции LinkedIn в службах Майкрософт, например просматривать профили LinkedIn в Outlook. При отключении интеграции с LinkedIn функции LinkedIn удаляются из приложений и служб Майкрософт. Обмен данными между LinkedIn и вашей организацией через службы Майкрософт прекращается.

### <a name="enable-or-disable-linkedin-integration-for-your-organization-in-the-azure-portal"></a>Включение и отключение интеграции с LinkedIn для вашей организации на портале Azure

1. Войдите в [Центр администрирования Azure Active Directory](https://aad.portal.azure.com/) с помощью учетной записи глобального администратора клиента Azure AD.
2. Выберите **Пользователи и группы**.
3. В колонке **Пользователи и группы** выберите **Параметры пользователей**.
4. В разделе **Интеграция с LinkedIn** выберите **Да** или **Нет**, чтобы включить или отключить интеграцию с LinkedIn.
   ![Включение интеграции с LinkedIn](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="enable-or-disable-linkedin-integration-for-your-organizations-office-2016-apps-using-group-policy"></a>Включение и отключение интеграции с LinkedIn для приложений Office 2016 в вашей организации с помощью групповой политики

1. Скачайте [файлы административных шаблонов Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030).
2. Извлеките **ADMX**-файлы и скопируйте их в **центральный репозиторий**.
3. Откройте средство управления групповыми политиками.
4. Создайте объект групповой политики со следующими параметрами: **Конфигурация пользователя** > **Административные шаблоны** > **Microsoft Office 2016** > **Разное** > **Allow LinkedIn Integration** (Разрешить интеграцию с LinkedIn).
5. Выберите **Включено** или **Отключено**.
  * Если эта политика **включена**, параметр **Show LinkedIn features in Office applications** (Показывать функции LinkedIn в приложениях Office) отображается в диалоговом окне параметров Office 2016. Это также означает, что пользователи в вашей организации могут использовать функции LinkedIn в приложениях Office.
  * Если эта политика **отключена**, параметр **Show LinkedIn features in Office applications** (Показывать функции LinkedIn в приложениях Office) в диалоговом окне параметров Office 2016 отключен и конечные пользователи не могут изменить его значение. Пользователи в вашей организации не могут использовать функции LinkedIn в приложениях Office 2016. 

Эта групповая политика касается только приложений Office 2016 для локальных компьютеров. Функции LinkedIn будут отображаться в карточках профиля в Office 365, даже если пользователи отключат LinkedIn в приложениях Office 2016. 

### <a name="learn-more"></a>Подробнее 
* [Сведения и функции LinkedIn в приложениях Майкрософт](https://go.microsoft.com/fwlink/?linkid=850740)

* [Центр справки LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Дополнительная информация
Просмотреть текущие настройки интеграции с LinkedIn на портале Azure можно по следующей ссылке:

[Настройка интеграции с LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 