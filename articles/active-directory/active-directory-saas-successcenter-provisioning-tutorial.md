---
title: "Руководство по настройке Cornerstone OnDemand для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как настроить Azure Active Directory для автоматической подготовки и отзыва учетных записей пользователей в Cornerstone OnDemand."
services: active-directory
documentationcenter: 
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 8aed9bdcdd54f2f0ef3cd81f979635e5716f0b5f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2018
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Руководство по настройке Cornerstone OnDemand для автоматической подготовки пользователей


В этом руководстве показаны шаги, которые необходимо выполнить в Cornerstone OnDemand и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической подготовки и отзыва пользователей и групп в Cornerstone OnDemand.


> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>предварительным требованиям

Сценарий, описанный в этом руководстве, предполагает, что у вас уже имеется:

*   клиент Azure AD;
*   Клиент Cornerstone OnDemand
*   учетная запись пользователя в Cornerstone OnDemand с разрешениями администратора.


> [!NOTE]
> Интеграция подготовки Azure AD зависит от [веб-службы Cornerstone OnDemand](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf), которая доступна командам Cornerstone OnDemand.

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Добавление Cornerstone OnDemand из коллекции
Перед настройки Cornerstone OnDemand для автоматической подготовки пользователей в Azure AD необходимо добавить Cornerstone OnDemand из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Cornerstone OnDemand из коллекции приложений Azure AD, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к элементу **Корпоративные приложения** > **Все приложения**.

    ![Раздел "Корпоративные приложения"][2]
    
3. Чтобы добавить Cornerstone OnDemand, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Cornerstone OnDemand**.

    ![Подготовка Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/AppSearch.png)

5. В области результатов выберите **Cornerstone OnDemand** и нажмите кнопку **Добавить**, чтобы добавить это приложение в список приложений SaaS.

    ![Подготовка Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/AppSearchResults.png)

    ![Подготовка Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cornerstone-ondemand"></a>Назначение пользователей в Cornerstone OnDemand

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD. 

Перед настройкой и включением автоматической подготовки пользователей нужно решить, какие пользователи или группы в Azure AD должны иметь доступ к Cornerstone OnDemand. Когда этот вопрос будет решен, эти пользователи и группы можно будет назначить приложению Cornerstone OnDemand, следуя приведенным ниже указаниям:

*   [Назначение корпоративному приложению пользователя или группы](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Важные рекомендации по назначению пользователей в Cornerstone OnDemand

*   Рекомендуется назначить одного пользователя Azure AD в Cornerstone OnDemand для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

*   При назначении пользователя в Cornerstone OnDemand в диалоговом окне назначения необходимо выбрать действительную роль для конкретного приложения (если доступно). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-cornerstone-ondemand"></a>Настройка автоматической подготовки пользователей в Cornerstone OnDemand

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей или групп в Cornerstone OnDemand на основе назначений в Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cornerstone-ondemand-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей в Cornerstone OnDemand:


1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к **Azure Active Directory > Корпоративные приложения > Все приложения**.

2. Выберите Cornerstone OnDemand из списка приложений SaaS.
 
    ![Подготовка Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/Successcenter2.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Подготовка Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningCredentials.png)

5. В разделе **Учетные данные администратора** введите **учетные данные**, **пароль администратора** и **домен** своей учетной записи Cornerstone OnDemand.

    *   В поле **Имя администратора** укажите имя учетной записи администратора в клиенте Cornerstone OnDemand. Например, admin.

    *   В поле **Пароль администратора** введите пароль, соответствующий имени пользователя администратора.

    *   В поле **Домен** введите URL-адрес веб-службы Cornerstone. Например, если служба расположена в `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, доменом Contoso будет `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`.

6. После заполнения поля, указанного в шаге 5, нажмите кнопку **Проверить подключение**, чтобы убедиться, что Azure AD может подключиться к Cornerstone OnDemand. Если установить подключение не удалось, убедитесь, что у учетной записи Cornerstone OnDemand есть разрешения администратора и попробуйте еще раз.

    ![Подготовка Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/TestConnection.png)

7. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Подготовка Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/EmailNotification.png)

8. Выберите команду **Сохранить**.

9. В разделе **сопоставления** выберите **Synchronize Azure Active Directory Users to Cornerstone OnDemand** (Синхронизировать пользователей Azure Active Directory с Cornerstone OnDemand).

    ![Подготовка Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/UserMapping.png)

10. В разделе **Сопоставление атрибутов** просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Cornerstone OnDemand. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления учетных записей пользователей в Cornerstone OnDemand для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/UserMappingAttributes.png)

11. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](./active-directory-saas-scoping-filters.md).

12. Чтобы включить службу подготовки Azure AD для Cornerstone OnDemand, измените значение параметра **Состояние подготовки** на **Включено** в разделе **Параметры**.

    ![Подготовка Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningStatus.png)

13. Определите пользователей или группы, которые вы хотите подготовить в Cornerstone OnDemand, выбрав нужные значения в поле **Область** раздела **Параметры**.

    ![Подготовка Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/SyncScope.png)

14. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Подготовка Cornerstone OnDemand](./media/active-directory-saas-successcenter-provisioning-tutorial/Save.png) 


После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **Сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов о подготовке, в которых зафиксированы все действия, выполняемые с приложением Cornerstone OnDemand службой подготовки Azure AD.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](./active-directory-saas-provisioning-reporting.md).
## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](active-directory-enterprise-apps-manage-provisioning.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Дополнительная информация

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_03.png
