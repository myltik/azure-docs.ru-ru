---
title: Руководство по интеграции Azure Active Directory с UserEcho | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении UserEcho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 29a02a5324344330dae3f2e47a09c94343e7355e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Руководство. Интеграция Azure Active Directory с UserEcho

В этом руководстве описано, как интегрировать UserEcho с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением UserEcho обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению UserEcho.
- Вы можете включить автоматический вход пользователей в UserEcho (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением UserEcho, вам потребуется следующее:

- подписка Azure AD;
- подписка UserEcho с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление UserEcho из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-userecho-from-the-gallery"></a>Добавление UserEcho из коллекции.
Чтобы настроить интеграцию UserEcho с Azure AD, необходимо добавить UserEcho из коллекции в список управляемых приложений SaaS.

**Чтобы добавить UserEcho из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **UserEcho**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_search.png)

5. На панели результатов выберите **UserEcho**, а затем нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в UserEcho с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в UserEcho соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в UserEcho.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в UserEcho.

Чтобы настроить и проверить единый вход Azure AD в UserEcho, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя UserEcho](#creating-a-userecho-test-user)** требуется для того, чтобы в UserEcho существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении UserEcho.

**Чтобы настроить единый вход Azure AD в UserEcho, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **UserEcho** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_samlbase.png)

3. В разделе **Домены и URL-адреса приложения UserEcho** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.userecho.com/`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь к [группе поддержки клиентов UserEcho](https://feedback.userecho.com/). 

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация UserEcho** щелкните **Настроить UserEcho**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_configure.png) 

7. В другом окне браузера войдите на корпоративный сайт UserEcho с правами администратора.

8. На панели инструментов вверху щелкните имя пользователя, чтобы развернуть меню, а затем щелкните **Setup**(Настройка).
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 

9. Щелкните **Integrations**(Интеграция).
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) 

10. Щелкните **Website** (Веб-сайт), а затем — **Single sign-on (SAML2)** (Единый вход (SAML2)).
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) 

11. На странице **Single sign-on (SAML)** (Единый вход (SAML)) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png)
    
    a. В поле **SAML-enabled** (Включить SAML) выберите **Yes** (Да).
    
    Б. В текстовое поле **SAML SSO URL** (URL-адрес единого входа SAML) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
    
    c. В текстовое поле **Remote logoout URL** (URL-адрес удаленного выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.
    
    d. Откройте скачанный сертификат в блокноте, а затем скопируйте и вставьте его содержимое в текстовое поле **X.509 Certificate** (Сертификат X.509).
    
    д. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-userecho-test-user"></a>Создание тестового пользователя UserEcho

Цель этого раздела — создать в приложении UserEcho пользователя с именем Britta Simon.

**Чтобы создать пользователя с именем Britta Simon в UserEcho, выполните следующие действия.**

1. Войдите на корпоративный сайт UserEcho с правами администратора.

2. На панели инструментов вверху щелкните имя пользователя, чтобы развернуть меню, а затем щелкните **Setup**(Настройка).
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png)

3. Щелкните **Users** (Пользователи), чтобы развернуть раздел **Users** (Пользователи).
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png)

4. Выберите раздел **Пользователи**.
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png)

5. Щелкните **Invite a new user**(Пригласить нового пользователя).
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)

6. В диалоговом окне **Invite a new user** (Приглашение нового пользователя) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png)

    a. В текстовое поле **Name** (Имя) введите имя пользователя, например Britta Simon.
    
    Б.  В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например Brittasimon@contoso.com.
    
    c. Нажмите кнопку **Пригласить**.

Пользователю Britta будет отправлено приглашение, с помощью которого можно начать работу с приложением UserEcho. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к UserEcho.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению UserEcho, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **UserEcho**.

    ![Настройка единого входа](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув элемент UserEcho на панели доступа, вы автоматически войдете в приложение UserEcho.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png

