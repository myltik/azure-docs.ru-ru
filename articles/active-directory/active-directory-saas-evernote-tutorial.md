---
title: "Руководство по интеграции Azure Active Directory с Evernote | Документация Майкрософт"
description: "Сведения о настройке единого входа между Azure Active Directory и Evernote."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: e4e2ca40ae270e7d3bfe4e828fcd571b5594ffc8
ms.lasthandoff: 03/09/2017


---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Руководство по интеграции Azure Active Directory с Evernote

В этом руководстве описано, как интегрировать Evernote с Azure Active Directory (Azure AD).

Интеграция Evernote с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Evernote.
- Вы можете включить автоматический вход пользователей в Evernote (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Evernote, вам потребуется:

- подписка Azure AD;
- подписка на Evernote с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Evernote из коллекции.
2. Настройка и проверка единого входа в Azure AD


## <a name="adding-evernote-from-the-gallery"></a>Добавление Evernote из коллекции
Чтобы настроить интеграцию Evernote с Azure AD, необходимо добавить Evernote из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Evernote из коллекции, сделайте следующее:**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![Приложения][3]

4. В поле поиска введите **Evernote**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search01.png)

5. На панели результатов выберите **Evernote** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search_result01.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Evernote с использованием тестового пользователя Britta Simon.

Для настройки единого входа в Azure AD необходимо знать, какой пользователь в Evernote соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Evernote.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Evernote.

Чтобы настроить и проверить единый вход Azure AD в Evernote, необходимо выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
4. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход в Azure AD на портале управления Azure и настроить его в приложении Evernote.

**Чтобы настроить единый вход Azure AD в Evernote, сделайте следующее:**

1. На портале управления Azure на странице интеграции с приложением **Evernote** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Evernote** выполнять какие-либо действия не требуется, если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**.

    ![Настройка единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_idp.png)
    
4. В разделе **Домены и URL-адреса приложения Evernote** выполните следующие действия, если вы хотите настроить приложение в **режиме, инициированном поставщиком услуг**:

    ![Настройка единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sp_01.png)
    
    а. Щелкните параметр **Показать дополнительные параметры URL-адресов**.

    b. В текстовом поле **URL-адрес для входа** введите следующий URL-адрес входа: `https://www.evernote.com/Login.action`.

5. В разделе **Сертификат подписи SAML** щелкните **Создание нового сертификата**.

    ![Настройка единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_cert.png)     

6. В диалоговом окне **Создание нового сертификата** щелкните значок календаря и выберите **дату окончания срока действия**. Затем нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_general_300.png)

7. В разделе **Сертификат подписи SAML** выберите **Make new certificate active** (Сделать новый сертификат активным) и нажмите кнопку **Сохранить**.

    ![Настройка единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_makecertactive.png)

8. Во всплывающем окне **Rollover certificate** (Сертификат восстановления) нажмите кнопку **ОК**.

    ![Настройка единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_general_400.png)

9. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certificate.png) 

10. В разделе **Настройка Evernote** щелкните **Настроить Evernote**, чтобы открыть окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configure.png) 

    ![Настройка единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configuresignon.png)

11. В другом окне веб-браузера войдите на сайт компании Evernote в качестве администратора.

12. Перейдите в **консоль администрирования**.

    ![Консоль администрирования](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

13. Из **консоли администрирования** перейдите в раздел **Безопасность** и выберите **Единый вход**.

    ![Настройка единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sso.png)

14. Задайте следующие значения.

    а.  **Включить единый вход**. Единый вход включен по умолчанию (щелкните **Отключить единый вход**, чтобы удалить требование единого входа).

    b. **SAML HTTP Request URL** (URL-адрес HTTP-запроса SAML). Введите **URL-адрес службы единого входа в SAML** из раздела **Настройка Evernote** в Azure AD.

    В. **Сертификат X.509**. Откройте скачанный сертификат из Azure AD в блокноте и скопируйте его содержимое, включая строки BEGIN CERTIFICATE и END CERTIFICATE.

    ![Настройка сертификата](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certx.png)

    Щелкните **Сохранить изменения**. 


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-evernote-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-evernote-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-evernote-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-evernote-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**. 



### <a name="creating-an-evernote-test-user"></a>Создание тестового пользователя Evernote

Чтобы пользователи Azure AD могли выполнять вход в Evernote, они должны быть подготовлены для Evernote.  
В случае с Evernote подготовка выполняется вручную.

**Чтобы подготовить учетные записи пользователей, выполните следующие действия.**

1. Войдите на сайт компании Evernote в качестве администратора.

2. Щелкните **Консоль администрирования**.

    ![Консоль администрирования](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

3. В **консоли администрирования** выберите **Добавить пользователей**.

    ![Добавление тестового пользователя](./media/active-directory-saas-evernote-tutorial/create_aaduser_0001.png)

4. **Добавьте адреса электронной почты участников команды** в **соответствующих полях** и нажмите кнопку **Пригласить**.

    ![Добавление тестового пользователя](./media/active-directory-saas-evernote-tutorial/create_aaduser_0002.png)
    
5. После этого владелец учетной записи Azure Active Directory получает сообщение электронной почты, чтобы принять приглашение.   


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход в Azure путем предоставления доступа к Evernote.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении Evernote, сделайте следующее:**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Evernote**.

    ![Настройка единого входа](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Evernote на панели доступа, вы автоматически войдете в приложение. Будет выполнен вход с использованием учетной записи организации, но затем нужно будет войти с помощью личной учетной записи.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_203.png

