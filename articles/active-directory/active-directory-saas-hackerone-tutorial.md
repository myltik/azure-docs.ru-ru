---
title: Руководство по интеграции Azure Active Directory с HackerOne | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в HackerOne.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 2d82b4cae516b88a01c4a31c8a72c82203e91630
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Учебник. Интеграция Azure Active Directory с HackerOn

В данном руководстве описано, как интегрировать Azure Active Directory (Azure AD) с приложением HackerOne.

Интеграция HackerOn с Azure AD дает приведенные далее преимущества:

- С помощью Azure AD вы можете контролировать доступ к HackerOn.
- Вы можете включить автоматический вход пользователей в HackerOn (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с HackerOn, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа HackerOne.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление HackerOne из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-hackerone-from-the-gallery"></a>Добавление HackerOne из коллекции
Чтобы настроить интеграцию HackerOne с Azure AD, необходимо добавить HackerOne из коллекции в список управляемых приложений SaaS.

**Чтобы добавить HackerOn из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **HackerOne**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_search.png)

5. На панели результатов выберите **HackerOne** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.

В этом разделе описана настройка и проверка единого входа Azure AD в HackerOne с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в HackerOne соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в HackerOne.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в HackerOne.

Чтобы настроить и проверить единый вход Azure AD в HackerOne, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя HackerOne](#creating-a-hackerone-test-user)** требуется для создания пользователя Britta Simon в HackerOne, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении HackerOne.

**Чтобы настроить единый вход Azure AD в HackerOne, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **HackerOne** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_samlbase.png)

3. Для **URL-адреса службы единого входа и идентификатора HackerOne** введите следующие значения:

    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://hackerone.com/<company name>/authentication`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в формате:`https://hackerone.com/users/saml/metadata`
    
    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить его, обратитесь в [службу поддержки HackerOne](mailto:support@hackerone.com). 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_general_400.png)

6. В разделе **Настройка HackerOne** щелкните **Настроить HackerOne**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_configure.png) 

7. Войдите в клиент HackerOne как администратор.

8. В верхнем меню щелкните **Settings** (Параметры).
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) 

9. Перейдите к разделу **Authentication** (Аутентификация) и щелкните **Add SAML settings** (Добавить параметры SAML).
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) 

10. В диалоговом окне **SAML Settings** (Параметры SAML) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) 

    a. Введите имя зарегистрированного домена в поле **Email Domain** (Домен электронной почты).

    Б. В текстовое поле **URL-адрес для единого входа** вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    c. Откройте в блокноте **сертификат**, скачанный на портале Azure, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.509**.
    
    d. Выберите команду **Сохранить**.

11. В диалоговом окне "Authentication Settings" (Параметры аутентификации) выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) 

    a. Щелкните **Run test**(Выполнить проверку).

    Б. Если поле **Status** (Состояние) имеет значение **Last test status: created** (Состояние при последней проверке: создан), обратитесь в [службу поддержки HackerOne](mailto:support@hackerone.com), чтобы запросить проверку своей конфигурации.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-hackerone-test-user"></a>Создание тестового пользователя HackerOne

Затем нужно создать пользователя Britta Simon в приложении HackerOne. Приложение HackerOne поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Новый пользователь, если он еще не существует, создается при доступе к HackerOne.

>[!NOTE]
>Если вам нужно вручную создать пользователя, необходимо обратиться в службу поддержки HackerOne. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к HackerOne.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в HackerOne, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **HackerOne**.

    ![Настройка единого входа](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

И, наконец, нужно проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув плитку HackerOne на панели доступа, вы автоматически войдете в приложение HackerOne.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png

