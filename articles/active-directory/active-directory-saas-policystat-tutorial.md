---
title: Руководство по интеграции Azure Active Directory с PolicyStat | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 0cd4cc4a622a8e23b98e06bb33d99755f247612c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349997"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Руководство по интеграции Azure Active Directory с PolicyStat

В этом руководстве описано, как интегрировать PolicyStat с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением PolicyStat обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к PolicyStat.
- Вы можете включить автоматический вход пользователей в PolicyStat (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с PolicyStat, вам потребуется:

- подписка Azure AD;
- подписка PolicyStat с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление PolicyStat из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-policystat-from-the-gallery"></a>Добавление PolicyStat из коллекции
Чтобы настроить интеграцию PolicyStat с Azure AD, необходимо добавить PolicyStat из коллекции в список управляемых приложений SaaS.

**Чтобы добавить PolicyStat из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **PolicyStat**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_search.png)

5. На панели результатов выберите **PolicyStat** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в PolicyStat с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в PolicyStat соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в PolicyStat.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в PolicyStat.

Чтобы настроить и проверить единый вход Azure AD в PolicyStat, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя PolicyStat](#creating-a-policystat-test-user)** требуется для того, чтобы в PolicyStat существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении PolicyStat.

**Чтобы настроить единый вход Azure AD в PolicyStat, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **PolicyStat** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_samlbase.png)

3. В разделе **Домены и URL-адреса приложения PolicyStat** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.policystat.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов PolicyStat](http://www.policystat.com/support/). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_certificate.png) 

5. В этом разделе показано, как разрешить пользователям проходить аутентификацию в PolicyStat со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

    Приложение PolicyStat ожидает утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию **атрибутов токена SAML**.  

     На следующем снимке экрана приведен пример.

     ![Атрибуты](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_attribute.png "Атрибуты")

6. Чтобы добавить обязательные сопоставления атрибутов, выполните следующие действия.

    | Имя атрибута    |   Значение атрибута |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_04.png)

    ![Настройка единого входа](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_addatribute.png)
    
    Б. В текстовом поле **Имя атрибута** введите значение **uid**.

    c. В текстовом поле **Значение атрибута** выберите **ExtractMailPrefix()**.    
   
    d. Из списка **Почта** выберите пункт **User.mail**.
    
    д. Нажмите кнопку **ОК**.

7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-policystat-tutorial/tutorial_general_400.png)

8. В другом окне веб-браузера войдите на сайт PolicyStat своей компании в качестве администратора.

9. Щелкните вкладку **Admin** (Администрирование), а затем щелкните **Single Sign-On Configuration** (Конфигурация единого входа) в левой области навигации.
   
    ![Меню администратора](./media/active-directory-saas-policystat-tutorial/ic808633.png "Меню администратора")

10. В разделе **Setup** (Настройка) установите флажок **Enable Single Sign-on Integration** (Включить интеграцию единого входа).
   
    ![Конфигурация единого входа](./media/active-directory-saas-policystat-tutorial/ic808634.png "Конфигурация единого входа")

11. Щелкните **Configure Attributes** (Настроить атрибуты), а затем в разделе **Configure Attributes** (Настройка атрибутов) сделайте следующее.
   
    ![Конфигурация единого входа](./media/active-directory-saas-policystat-tutorial/ic808635.png "Конфигурация единого входа")
   
    a. В текстовом поле **Username Attribute** (Атрибут имени пользователя) введите значение **uid**.

    Б. В текстовое поле **First Name Attribute** (Атрибут имени) введите **имя** пользователя, **Britta**.

    c. В текстовое поле **Last Name Attribute** (Атрибут фамилии) введите **фамилию** пользователя, **Simon**.

    d. В текстовое поле **Email Attribute** (Атрибут электронной почты) введите значение **адрес электронной почты** пользователя, **BrittaSimon@contoso.com**.

    д. Нажмите кнопку **Сохранить изменения**.

12. Щелкните **Your IDP Metadata** (Метаданные вашего поставщика удостоверений), а затем в разделе **Your IDP Metadata** (Метаданные вашего поставщика удостоверений) сделайте следующее.
   
    ![Конфигурация единого входа](./media/active-directory-saas-policystat-tutorial/ic808636.png "Конфигурация единого входа")
   
    a. Откройте скачанный файл метаданных, скопируйте его содержимое и вставьте его в текстовое поле **Your Identity Provider Metadata** (Метаданные поставщика удостоверений).

    Б. Нажмите кнопку **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-policystat-test-user"></a>Создание тестового пользователя PolicyStat

Чтобы пользователи Azure AD могли выполнять вход в PolicyStat, они должны быть подготовлены для PolicyStat.  

PolicyStat поддерживает подготовку пользователей «на лету». Это означает, что вам не надо вручную добавлять пользователей PolicyStat. Пользователи будут добавляться автоматически при первом входе с помощью единого входа.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя PolicyStat или API, предоставляемые PolicyStat для подготовки учетных записей пользователя Azure Active Directory.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к PolicyStat.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в PolicyStat, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **PolicyStat**.

    ![Настройка единого входа](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "PolicyStat" на панели доступа, вы автоматически войдете в приложение PolicyStat.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_203.png

