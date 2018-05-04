---
title: Руководство по интеграции Azure Active Directory c IQNavigator VMS | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в IQNavigator VMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: ddb2883c0b90aaffa82bbc701cdc20de1feb7e19
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>Руководство по интеграции Azure Active Directory c IQNavigator VMS

В этом руководстве описано, как интегрировать IQNavigator VMS с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением IQNavigator VMS обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к IQNavigator VMS.
- Вы можете включить автоматический вход пользователей в IQNavigator VMS (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с IQNavigator VMS, вам потребуется:

- подписка Azure AD;
- подписка IQNavigator VMS с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление IQNavigator VMS из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Добавление IQNavigator VMS из коллекции
Чтобы настроить интеграцию IQNavigator VMS с Azure AD, необходимо добавить IQNavigator VMS из коллекции в список управляемых приложений SaaS.

**Чтобы добавить IQNavigator VMS из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **IQNavigator VMS**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_search.png)

5. На панели результатов выберите **IQNavigator VMS** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в IQNavigator VMS с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в IQNavigator VMS соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в IQNavigator VMS.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в IQNavigator VMS.

Чтобы настроить и проверить единый вход Azure AD в IQNavigator VMS, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя IQNavigator VMS](#creating-a-iqnavigator-vms-test-user)** требуется для того, чтобы в IQNavigator VMS существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении IQNavigator VMS.

**Чтобы настроить единый вход Azure AD в IQNavigator VMS, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **IQNavigator VMS** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Настройка единого входа](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_samlbase.png)

3. В разделе **Домены и URL-адреса приложения IQNavigator VMS** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `iqn.com`.

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`.

4. Установите флажок **Показывать дополнительные параметры URL-адреса** и выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url1.png)

    В текстовое поле **Состояние ретранслятора** введите URL-адрес в следующем формате: `https://<subdomain>.iqnavigator.com`.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените эти значения фактическими URL-адресом ответа и значением состояния ретранслятора. Чтобы получить эти значения, обратитесь к [группе поддержки IQNavigator VMS](https://www.beeline.com/iqn-product-support/).

5. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот.
    
    ![Настройка единого входа](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_metadataurl.png)

6. Приложение IQNavigator ожидает в утверждении идентификатора имени значение уникального идентификатора пользователя. Клиент может сопоставить правильное значение для утверждения идентификатора имени. В данном случае для примера мы сопоставили user.UserPrincipalName. Однако вам нужно сопоставить правильное значение, соответствующее параметрам вашей организации.

    ![Настройка единого входа](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_attribute.png)

7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_400.png)

8. В разделе **Конфигурация IQNavigator VMS** щелкните **Настроить IQNavigator VMS**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_configure.png)

9. Чтобы настроить единый вход на стороне **IQNavigator VMS**, нужно отправить **URL-адрес метаданных федерации приложений**, **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** в [группу поддержки IQNavigator VMS](https://www.beeline.com/iqn-product-support/). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-iqnavigatorvms-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-iqnavigatorvms-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-iqnavigatorvms-tutorial/create_aaduser_03.png)

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-iqnavigatorvms-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="creating-a-iqnavigator-vms-test-user"></a>Создание тестового пользователя IQNavigator VMS

Цель этого раздела — создать пользователя с именем Britta Simon в IQNavigator VMS. Обратитесь к [группе поддержки IQNavigator VMS](https://www.beeline.com/iqn-product-support/), чтобы добавить пользователей в учетную запись IQNavigator VMS.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к IQNavigator VMS.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в IQNavigator VMS, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

2. Из списка приложений выберите **IQNavigator VMS**.

    ![Настройка единого входа](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "IQNavigator VMS" на панели доступа, вы автоматически войдете в приложение IQNavigator VMS.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_203.png

