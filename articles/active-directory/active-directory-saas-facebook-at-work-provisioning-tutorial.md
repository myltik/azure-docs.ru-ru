---
title: "Руководство по настройке подготовки пользователей в Workplace by Facebook | Документация Майкрософт"
description: "Узнайте, как настроить автоматическую подготовку и отмену подготовки учетных записей пользователей из Azure AD в Workplace by Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: a347eedbf5511dc83e1bc7721667441cfb87cb59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configure-workplace-by-facebook-for-user-provisioning"></a>Руководство по настройке Workplace by Facebook для подготовки пользователей

Этом руководстве описывается настройка автоматической подготовки и отзыва учетных записей пользователей Azure Active Directory (Azure AD) для Workplace by Facebook.

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Workplace by Facebook, вам потребуется:

- подписка Azure AD;
- подписка Workplace by Facebook с поддержкой единого входа.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-workplace-by-facebook"></a>Назначение пользователей для Workplace by Facebook

В Azure AD для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется понятие "назначения". В контексте автоматической подготовки учетной записи синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD.

Перед настройкой и включением службы подготовки необходимо решить, какие пользователи и группы в Azure AD представляют пользователей, которым требуется доступ к приложению Workplace by Facebook. После этого можно назначить этих пользователей для приложения Workplace by Facebook, выполнив инструкции, приведенные в разделе [Назначение пользователя или группы пользователей корпоративному приложению](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

>[!IMPORTANT]
>*   Проверьте конфигурацию подготовки, назначив одного пользователя Azure AD в Workplace by Facebook. Дополнительных пользователей и группы можно будет назначить позже.
>*   При назначении пользователя Workplace by Facebook необходимо выбрать допустимую роль пользователя. Роль "Доступ по умолчанию" не подходит для подготовки.

## <a name="enable-automated-user-provisioning"></a>Включение автоматической подготовки пользователей

В этом разделе описывается подключение Azure AD к API подготовки учетных записей пользователей Workplace by Facebook. Вы также узнаете, как настроить службу подготовки для создания, обновления и отключения назначенных учетных записей пользователей в Workplace by Facebook. Это зависит от назначенных пользователей и групп в Azure AD.

>[!Tip]
>Для Workplace by Facebook можно также включить единый вход на основе SAML. Для этого следуйте инструкциям, приведенным на [портале Azure](https://portal.azure.com). Единый вход можно настроить независимо от автоматической подготовки, хотя две эти функции дополняют друг друга.

### <a name="configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Настройка подготовки учетных записей пользователей в Workplace by Facebook в Azure AD

Azure AD поддерживает автоматическую синхронизацию сведений об учетных записях назначенных пользователей с Workplace by Facebook. Эта автоматическая синхронизация позволяет Workplace by Facebook получить данные, необходимые для авторизации доступа пользователя до первого входа пользователя в систему. Она также отменяет подготовку пользователей для Workplace by Facebook, если доступ для них отозван в Azure AD.

1. На [портале Azure](https://portal.azure.com) выберите **Azure Active Directory** > **Корпоративные приложения** > **Все приложения**.

2. Если в Workplace by Facebook уже настроен единый вход, найдите свой экземпляр Workplace by Facebook с помощью поля поиска. В противном случае щелкните **Добавить** и найдите **Workplace by Facebook** в коллекции приложений. Выберите **Workplace by Facebook** в результатах поиска и добавьте в список приложений.

3. Выберите экземпляр Workplace by Facebook, а затем перейдите на вкладку **Подготовка**.

4. Для параметра **Режим подготовки к работе** выберите значение **Автоматически**. 

    ![Снимок экрана параметров подготовки Workplace by Facebook](./media/active-directory-saas-facebook-at-work-provisioning-tutorial/provisioning.png)

5. В разделе **Учетные данные администратора** введите **секретный токен** и **URL-адрес клиента** администратора Workplace by Facebook.

6. На портале Azure щелкните **Проверить подключение** и убедитесь, что Azure AD может подключиться к приложению Workplace by Facebook. Если подключение отсутствует, убедитесь, что у учетной записи Workplace by Facebook есть разрешения администратора команды.

7. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок.

8. Щелкните **Сохранить**.

9. В разделе "Сопоставления" выберите **Synchronize Azure Active Directory Users to Workplace by Facebook** (Синхронизировать пользователей Azure Active Directory с Workplace by Facebook).

10. В разделе **Сопоставления атрибутов** просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Workplace by Facebook. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Workplace by Facebook для операций обновления. Чтобы зафиксировать изменения, щелкните **Сохранить**.

11. Чтобы включить службу подготовки Azure AD для Workplace by Facebook, измените значение параметра **Состояние подготовки** на **Включено** в разделе **Настройки**.

12. Щелкните **Сохранить**.

Дополнительные сведения о настройке автоматической подготовки см. в [документации по Facebook](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers).

Теперь можно создать тестовую учетную запись. Подождите примерно 20 минут, чтобы убедиться, что учетная запись была синхронизирована с Workplace by Facebook.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Настройка единого входа](active-directory-saas-facebook-at-work-tutorial.md)

