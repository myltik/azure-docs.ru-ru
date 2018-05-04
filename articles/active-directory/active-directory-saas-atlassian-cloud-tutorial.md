---
title: Руководство по интеграции Azure Active Directory с Atlassian Cloud | Документация Майкрософт
description: Сведения о настройке единого входа Azure Active Directory в приложении Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.openlocfilehash: a43d0a165d9da6267c6f9733420244ebf913e930
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Руководство по интеграции Azure Active Directory с Atlassian Cloud

В этом руководстве описано, как интегрировать приложение Atlassian Cloud с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Atlassian Cloud обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Atlassian Cloud.
- Вы можете включить автоматический вход пользователей в Atlassian Cloud (единый вход) с использованием учетной записи Azure Active Directory.
- Вы можете управлять учетными записями централизованно, через портал Azure.

Дополнительные сведения об интеграции приложения SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Atlassian Cloud, вам потребуется:

- подписка Azure AD;
- Чтобы включить единый вход SAML для продуктов компании Atlassian Cloud, потребуется настроить Identity Manager. Дополнительные сведения об Identity Manager см. [здесь]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для тестирования действий, выполняемых в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.
Сценарий, описанный в этом руководстве, состоит из двух основных стандартных блоков.

* Добавление Atlassian Cloud из коллекции
* настройка и проверка единого входа в Azure AD.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Добавление Atlassian Cloud из коллекции
Чтобы настроить интеграцию Atlassian Cloud с Azure AD, добавьте Atlassian Cloud из коллекции в список управляемых приложений SaaS, выполнив следующее:

1. На [портале Azure](https://portal.azure.com) в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"][1]

2. Щелкните **Корпоративные приложения** > **Все приложения**.

    ![Область "Корпоративные приложения"][2]
    
3. Чтобы добавить приложение, выберите **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Atlassian Cloud**, в списке результатов выберите **Atlassian Cloud**, а затем нажмите кнопку **Добавить**.

    ![Atlassian Cloud в списке результатов](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Atlassian Cloud с использованием тестового пользователя *Britta Simon*.

Чтобы настроить единый вход в Azure AD, необходимо определить, какой пользователь Atlassian Cloud соответствует пользователю в Azure Active Directory. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Atlassian Cloud.

Для этого назначьте в качестве *имени пользователя* Atlassian Cloud значение *имени пользователя* в Azure AD.

Чтобы настроить и проверить единый вход Azure AD в Atlassian Cloud, выполните действия в стандартных блоках в следующих разделах.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале и настроить его в приложении Atlassian Cloud.

Чтобы настроить единый вход Azure AD в Atlassian Cloud, сделайте следующее:

1. На портале Azure в области интеграции с приложением **Atlassian Cloud** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В окне **Единый вход** в поле **Режим единого входа** выберите **Вход на основе SAML**.

    ![Окно единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Чтобы настроить приложение в режиме, инициированном поставщиком удостоверений, в разделе **Домены и URL-адреса приложения Atlassian Cloud** выполните следующее.

    ![Сведения о домене и URL-адресах единого входа приложения Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. В поле **Идентификатор** введите **`https://auth.atlassian.com/saml/<unique ID>`**.
    
    Б. В поле **URL-адрес ответа** введите **`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`**.

    c. В поле **Состояние ретранслятора** введите URL-адрес со следующим синтаксисом: **`https://<instancename>.atlassian.net`**.

4. Чтобы настроить приложение в режиме, инициированном поставщиком услуг, выберите **Показать дополнительные параметры URL-адресов**, а затем в поле **URL-адрес для входа** введите URL-адрес со следующим синтаксисом: **`https://<instancename>.atlassian.net`**.

    ![Сведения о домене и URL-адресах единого входа приложения Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE]
    > Приведенные выше значения используются только для примера. Замените их на фактические значения идентификатора, URL-адреса ответа и URL-адреса входа. Реальные значения можно получить на экране настройки SAML для Atlassian Cloud. Позже в данном руководстве мы рассмотрим эти значения.

5. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)** и сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png)

6. Приложение Atlassian Cloud ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. 

    По умолчанию значение **Идентификатор пользователя** совпадает с атрибутом user.userprincipalname. Измените его на атрибут user.mail. Кроме того, можно выбрать другое значение в соответствии с настройками в вашей организации, но в большинстве случаев необходима электронная почта.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png)

7. Щелкните **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

8. Чтобы открыть окно **Настройка единого входа**, в разделе **Настройка Atlassian Cloud** выберите **Настроить Atlassian Cloud**.

9. Скопируйте **идентификатор сущности SAML** и **URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png)

10. Чтобы настроить единый вход для вашего приложения, войдите на портал Atlassian с помощью учетных данных администратора.

11. Перед настройкой единого входа необходимо проверить домен. Дополнительные сведения см. в документе по [проверке домена Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

12. В левой области выберите **SAML single sign-on** (Единый вход SAML). Подпишитесь на Atlassian Identity Manager.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

13. В окне**Add SAML configuration** (Добавление конфигурации SAML) выполните следующее.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. В поле **Identity provider Entity ID** (Идентификатор сущности поставщика удостоверений) вставьте идентификатор сущности SAML, скопированный на портале Azure.

    Б. В поле **Identity provider SSO URL** (URL-адрес единого входа поставщика удостоверений) вставьте URL-адрес службы единого входа SAML, скопированный с портала Azure.

    c. Откройте сертификат, скачанный с портала Azure в TXT-файле, скопируйте значения (без строк *Begin Certificate* и *End Certificate*) и вставьте их в поле **Public X509 certificate** (Общий сертификат X509).
    
    d. Выберите **Save Configuration** (Сохранить конфигурацию).
     
14. Чтобы убедиться, что вы настроили правильные URL-адреса, обновите параметры Azure AD, выполнив следующее.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. В окне SAML скопируйте **идентификатор удостоверения поставщика услуг** и вставьте его в поле **Идентификатор** на портале Azure в разделе **Домены и URL-адреса приложения Atlassian Cloud**.
    
    Б. В окне SAML скопируйте **URL-адрес поставщика службы обработчика утверждений поставщика услуг** и вставьте его в поле **URL-адрес ответа** на портале Azure в разделе **Домены и URL-адреса приложения Atlassian Cloud**. URL-адрес для входа является URL-адресом клиента приложения Atlassian Cloud.

    > [!NOTE]
    > Если вы уже давно являетесь клиентом, после обновления значений **идентификатора удостоверений поставщика услуг** и **URL-адреса службы обработчика утверждений** на портале Azure выберите **Yes, update configuration** (Да, обновить конфигурацию). Если вы — новый клиент, этот шаг можно пропустить.
    
15. На портале Azure выберите **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

В этом разделе вы создадите на портале Azure тестового пользователя Britta Simon, выполнив следующие действия.

   ![Создание тестового пользователя Azure AD][100]

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Чтобы отобразить список пользователей, выберите **Пользователи и группы** > **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. В окне **Все пользователи** выберите **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. В окне **Пользователь** сделайте следующее.

    ![Окно пользователя](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-an-atlassian-cloud-test-user"></a>Создание тестового пользователя в Atlassian Cloud

Чтобы настроить вход в Atlassian Cloud для пользователей Azure AD, подготовьте учетные записи пользователей вручную в Atlassian Cloud, выполнив следующее.

1. В области **администрирования** выберите **Users** (Пользователи).

    ![Ссылка на пункт Users (Пользователи) в Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Чтобы создать пользователя в Atlassian Cloud, выберите **Invite user** (Пригласить пользователя).

    ![Создание пользователя Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. В поле **Email address** (Адрес электронной почты) введите адрес, а затем назначьте доступ к приложению.

    ![Создание пользователя Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Чтобы отправить приглашение для пользователя по электронной почте, выберите **Invite users** (Пригласить пользователей). Приглашение по электронной почте отправляется пользователю, и после его принятия пользователь получит доступ к системе.

>[!NOTE]
>Вы также можете создать несколько пользователей, нажав кнопку **Bulk Create** (Массовое создание) в разделе **Users** (Пользователи).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Atlassian Cloud. Для этого выполните следующее.

![Назначение роли пользователя][200]

1. На портале Azure откройте представление **Приложения**, перейдите к представлению "Каталог" и выберите **Корпоративные приложения** > **Все приложения**.

    ![Назначение пользователя][201]

2. В списке **приложений** выберите **Atlassian Cloud**.

    ![Ссылка на Atlassian Cloud в списке приложений](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)

3. В области слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**, затем в области **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В окне **Пользователи и группы** в списке **Пользователи** выберите **Britta Simon**.

6. В окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В окне **Добавление назначения** выберите **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку **Atlassian Cloud** на панели доступа, вы автоматически войдете в приложение Atlassian Cloud.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png
