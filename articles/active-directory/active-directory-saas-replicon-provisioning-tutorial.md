---
title: Руководство по настройке Replicon для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отмены подготовки учетных записей пользователей в Replicon.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: v-ant
ms.openlocfilehash: b6f536ba1711076eab0eb9dcbea7d357e9cebf3d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345601"
---
# <a name="tutorial-configure-replicon-for-automatic-user-provisioning"></a>Руководство по настройке Replicon для автоматической подготовки пользователей

В этом руководстве показаны шаги, которые необходимо выполнить в Replicon и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической подготовки и отзыва пользователей и групп в Replicon.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>предварительным требованиям

Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

*   клиент Azure AD;
*   клиент Replicon с включенным планом [Plus](https://www.replicon.com/time-bill-pricing/) или выше;
*   учетная запись пользователя в Replicon с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки в Azure AD зависит от [API Replicon](https://www.replicon.com/help/developers), доступного для команд Replicon в плане Plus или выше.

## <a name="adding-replicon-from-the-gallery"></a>Добавление Replicon из коллекции
Перед настройкой Replicon для автоматической подготовки пользователей в Azure AD необходимо добавить Replicon из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Replicon из коллекции приложений Azure AD, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к элементу **Корпоративные приложения** > **Все приложения**.

    ![Раздел "Корпоративные приложения"][2]
    
3. Чтобы добавить Replicon, в верхней части диалогового окна нажмите кнопку **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Replicon**.

    ![Поиск приложения Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearch.png)

5. В области результатов выберите **Replicon** и нажмите кнопку **Добавить**, чтобы добавить это приложение в список приложений SaaS.

    ![Результаты поиска Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppSearchResults.png)

    ![Создание приложения Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconAppCreate.png)


## <a name="assigning-users-to-replicon"></a>Назначение пользователей в Replicon

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей нужно решить, какие пользователи или группы в Azure AD должны иметь доступ к Replicon. Когда этот вопрос будет решен, этих пользователей и (или) группы можно будет назначить приложению Replicon, следуя приведенным ниже указаниям.

*   [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-replicon"></a>Важные рекомендации по назначению пользователей в Replicon

*   Рекомендуется назначить одного пользователя Azure AD в Replicon для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

*   При назначении пользователя в Replicon в диалоговом окне назначения необходимо выбрать действительную роль для конкретного приложения (при доступности). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-replicon"></a>Настройка автоматической подготовки пользователей в Replicon 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в Replicon на основе их назначений в Azure AD.

> [!TIP]
> Для Replicon также можно включить единый вход на основе SAML. Для этого следуйте инструкциям, приведенным в [руководстве по единому входу в Replicon](active-directory-saas-replicon-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-replicon-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей в Replicon, сделайте следующее.

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к **Azure Active Directory > Корпоративные приложения > Все приложения**.

2. Из списка приложений SaaS выберите Replicon.

    ![Подготовка Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon2.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Подготовка Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/Replicon1.png)

5. В разделе **Учетные данные администратора** введите **имя администратора**, **пароль администратора**, **CompanyId** и **домен** своей учетной записи Replicon. Примеры этих значений:

    *   В поле **Имя администратора** укажите имя учетной записи администратора в клиенте Replicon. Пример: contosoadmin.

    *   В поле **Пароль администратора** введите пароль, соответствующий имени пользователя администратора.

    *   В поле **CompanyId** введите значение CompanyId клиента Replicon. Пример: значение CompanyID на основании приведенного ниже имени для входа — Contoso.

    ![Имя для входа Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconLogin.png)

    *   В поле **Домен** укажите домен, как описано на шаге 6.
    
6. Получите **serviceEndpointRootURL** для своей учетной записи клиента Replicon, выполнив указания в [справке по службе Replicon](https://www.replicon.com/help/determining-the-url-for-your-service-calls). При получении URL-адреса **домен** должен будет быть поддоменом **serviceEndpointRootURL**, как показано на рисунке. 

    ![Подготовка Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconEndpoint.png)

7. После заполнения полей, указанных на шаге 5, щелкните **Проверить подключение**, чтобы убедиться, что Azure AD может подключиться к Replicon. Если установить подключение не удалось, убедитесь, что у учетной записи Replicon есть разрешения администратора, и повторите попытку.

    ![Подготовка Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconTestConnection.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Подготовка Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconNotificationEmail.png)

9. Выберите команду **Сохранить**.

10. В разделе **Сопоставления** выберите **Синхронизировать пользователей Azure Active Directory с Replicon**.
    
    ![Подготовка Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMapping.png)

11. В разделе **Сопоставление атрибутов** просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Replicon. Атрибуты, которые выбраны в качестве свойств **сопоставления**, будут использоваться для сопоставления учетных записей пользователей в Replicon для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconUserMappingAtrributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](./active-directory-saas-scoping-filters.md).

13. Чтобы включить службу подготовки Azure AD для Replicon, измените значение параметра **Состояние подготовки** на **Включено** в разделе **Параметры**.

    ![Подготовка Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/RepliconProvisioningStatus.png)

14. Определите пользователей или группы для подготовки в Replicon, выбрав нужные значения в поле **Область** раздела **Параметры**.

    ![Подготовка Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/UserGroupSelection.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Подготовка Replicon](./media/active-directory-saas-replicon-provisioning-tutorial/SaveProvisioning.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **Сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов о подготовке, в которых зафиксированы все действия, выполняемые службой подготовки Azure AD с приложением Replicon.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](active-directory-enterprise-apps-manage-provisioning.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дополнительная информация

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
