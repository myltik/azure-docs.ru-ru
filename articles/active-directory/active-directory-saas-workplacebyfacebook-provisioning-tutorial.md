---
title: Руководство по настройке Workplace by Facebook для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: c25acd00aa55bb8d8c222f440ddfc951bfdf7970
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353590"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Руководство по настройке Workplace by Facebook для автоматической подготовки пользователей

Цель этого руководства — показать, как в Workplace by Facebook и Azure AD настроить автоматическую подготовку и отзыв учетных записей пользователей из Azure AD в Workplace by Facebook.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Workplace by Facebook, вам потребуется:

- подписка Azure AD;
- подписка Workplace by Facebook с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Назначение пользователей для Workplace by Facebook

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки учетной записи будут синхронизированы только те записи и группы, которые были "назначены" приложению в Azure AD.

Перед настройкой и включением службы подготовки необходимо решить, какие пользователи или группы в Azure AD представляют пользователей, которым требуется доступ к приложению Workplace by Facebook. Приняв это решение, можно будет назначить этих пользователей для приложения Workplace by Facebook, выполнив следующие действия.

[Назначение корпоративному приложению пользователя или группы](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Важные советы по назначению пользователей в Workplace by Facebook

*   Рекомендуется назначить одного пользователя Azure AD в Workplace by Facebook для тестирования конфигурации подготовки. Дополнительные пользователи и/или группы можно назначить позднее.

*   При назначении пользователя Workplace by Facebook необходимо выбрать допустимую роль пользователя. Роль "Доступ по умолчанию" не работает для подготовки.

## <a name="enable-user-provisioning"></a>Включение подготовки пользователей

В этом разделе описывается подключение вашего каталога Azure AD к API подготовки учетных записей пользователей в Workplace by Facebook и настройка службы подготовки для создания, обновления и отключения назначенных учетных записей пользователей в Workplace by Facebook на основе назначения пользователей и групп в Azure AD.

>[!Tip]
>Для Workplace by Facebook можно также включить единый вход на основе SAML. Для этого следуйте инструкциям, приведенным на [портале Azure](https://portal.azure.com). Единый вход можно настроить независимо от автоматической подготовки, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Настройка подготовки учетных записей пользователей в Workplace by Facebook в Azure AD

В этом разделе описано, как включить подготовку учетных записей пользователей Active Directory для Workplace by Facebook.

Azure AD поддерживает автоматическую синхронизацию сведений об учетных записях назначенных пользователей с Workplace by Facebook. Эта автоматическая синхронизация позволяет Workplace by Facebook получить данные, необходимые для авторизации доступа пользователя до первого входа пользователя в систему. Она также отменяет подготовку пользователей для Workplace by Facebook, если доступ для них отозван в Azure AD.

1. На [портале Azure](https://portal.azure.com) перейдите в раздел **Azure Active Directory** > **Корпоративные приложения** > **Все приложения**.

2. Если в Workplace by Facebook уже настроен единый вход, найдите свой экземпляр Workplace by Facebook с помощью поля поиска. В противном случае щелкните **Добавить** и найдите **Workplace by Facebook** в коллекции приложений. Выберите Workplace by Facebook в результатах поиска и добавьте в список приложений.

3. Выберите экземпляр Workplace by Facebook, а затем перейдите на вкладку **Подготовка**.

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически). 

    ![Подготовка](./media/active-directory-saas-workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. В разделе **Учетные данные администратора** введите маркер доступа администратора из Workplace by Facebook и укажите URL-адрес клиента, `https://www.facebook.com/scim/v1/`. Ознакомьтесь с [инструкциями](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) по созданию маркера доступа для Workplace. 

6. На портале Azure щелкните **Проверить подключение** и убедитесь, что Azure AD может подключиться к приложению Workplace by Facebook. Если подключение отсутствует, убедитесь, что у учетной записи Workplace by Facebook есть разрешения администратора команды.

7. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок.

8. Нажмите кнопку **Сохранить**.

9. В разделе "Сопоставления" выберите **Synchronize Azure Active Directory Users to Workplace by Facebook** (Синхронизировать пользователей Azure Active Directory с Workplace by Facebook).

10. В разделе **Сопоставления атрибутов** просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Workplace by Facebook. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Workplace by Facebook для операций обновления. Нажмите кнопку "Сохранить", чтобы подтвердить все изменения.

11. Чтобы включить службу подготовки Azure AD для Workplace by Facebook, измените значение параметра **Состояние подготовки** на **Включено** в разделе **Настройки**.

12. Нажмите кнопку **Сохранить**.

Дополнительные сведения о настройке автоматической подготовки см. по адресу [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers).

Теперь можно создать тестовую учетную запись. Подождите примерно 20 минут, чтобы убедиться, что учетная запись была синхронизирована с Workplace by Facebook.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Настройка единого входа](active-directory-saas-workplacebyfacebook-tutorial.md)
