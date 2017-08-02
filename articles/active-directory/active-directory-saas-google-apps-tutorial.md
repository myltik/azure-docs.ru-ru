---
title: "Руководство по интеграции Azure Active Directory c Google Apps в Azure | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Google Apps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 065841d6b4fe50e953f01bba4d3f23de82b82726
ms.contentlocale: ru-ru
ms.lasthandoff: 07/14/2017

---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>Руководство по интеграции Azure Active Directory с Google Apps

В этом руководстве описано, как интегрировать приложение Google Apps с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Google Apps обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Google Apps.
- Вы можете включить автоматический вход пользователей в Google Apps (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Google Apps, вам потребуется:

- подписка Azure AD;
- подписка Google Apps с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="video-tutorial"></a>Видеоруководство
Как включить единый вход в Google Apps за 2 минуты.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enable-single-sign-on-to-Google-Apps-in-2-minutes-with-Azure-AD/player]

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы
1. **Вопрос. Поддерживают ли Chromebooks и другие устройства Chrome единый вход Azure AD?**
   
    Ответ. Да, пользователи могут войти на свои устройства Chromebook, используя свои учетные данные Azure AD. Сведения о том, почему учетные данные могут быть запрошены у пользователей дважды, см. в этой [статье о поддержке Google Apps](https://support.google.com/chrome/a/answer/6060880).

2. **Вопрос. Если включить единый вход, смогут ли пользователи использовать свои учетные данные Azure AD для входа в любой продукт Google, такой как Google Classroom, GMail, Google Drive, YouTube и т. д?**
   
    Ответ. Да, в зависимости от того, [какие приложения Google](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) вы решили включить или отключить для своей организации.

3. **Вопрос. Можно ли включить единый вход только для подмножества пользователей Google Apps?**
   
    Ответ. Нет, при включении единого входа всем пользователям Google Apps вашей организации сразу же потребуется использовать свои учетные данные Azure AD для аутентификации. Поскольку Google Apps не поддерживает наличие нескольких поставщиков удостоверений, то в качестве поставщика удостоверений для среды Google Apps может быть использован либо Azure AD, либо Google — но не оба одновременно.

4. **Вопрос. Если пользователь выполнил вход в Windows, пройдет ли он автоматическую аутентификацию в Google Apps без запроса пароля?**
   
    Ответ. Существует два варианта включения этого сценария. Во-первых, пользователи могут входить в устройства Windows 10 посредством [присоединения к Azure Active Directory](active-directory-azureadjoin-overview.md). Кроме того, пользователи могут входить на устройства с Windows, присоединенные к домену локального каталога Active Directory, для которого разрешен единый вход Azure AD посредством развертывания [служб федерации Active Directory (AD FS)](active-directory-aadconnect-user-signin.md). В обоих случаях необходимо выполнить действия следующего руководства для включения единого входа между Azure AD и Google Apps.

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Google Apps из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-google-apps-from-the-gallery"></a>Добавление Google Apps из коллекции
Чтобы настроить интеграцию Google Apps с Azure AD, необходимо добавить Google Apps из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Google Apps из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **Google Apps**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_search.png)

5. На панели результатов выберите **Google Apps** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Google Apps с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Google Apps соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Google Apps.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Google Apps.

Чтобы настроить и проверить единый вход Azure AD в Google Apps, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Google Apps](#creating-a-google-apps-test-user)** требуется для того, чтобы в Google Apps существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Google Apps.

**Чтобы настроить единый вход Azure AD в Google Apps, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Google Apps** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Google Apps** сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://mail.google.com/a/<yourdomain>`

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Обратитесь к [группе поддержки Google](https://www.google.com/contact/).
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-google-apps-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Google Apps** щелкните **Настроить Google Apps**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, URL-адрес службы единого входа SAML и URL-адрес изменения пароля** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_configure.png) 

7. Откройте в браузере новую вкладку и войдите в [консоль администратора Google Apps](http://admin.google.com/) с использованием учетной записи администратора.

8. Щелкните **Security**(Безопасность). Если эта ссылка не отображается, она может быть скрыта в меню **More Controls** (Другие элементы управления) в нижней части экрана.
   
    ![Выбор элемента "Безопасность"][10]

9. На странице **Security** (Безопасность) выберите **Set up single sign-on (SSO)** (Настроить единый вход).
   
    ![Выбор элемента "Единый вход"][11]

10. Выполните следующие настройки.
   
    ![Настройка единого входа][12]
   
    а. Выберите **Setup SSO with third party identity provider**(Настройка единого входа с помощью стороннего поставщика удостоверений).

    b. В текстовое поле **Sign-in page URL** (URL-адрес страницы входа) в Google Apps вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.

    c. В текстовое поле **Sign-out page URL** (URL-адрес страницы выхода) в Google Apps вставьте значение **URL-адрес выхода**, скопированное на портале Azure. 

    г) В текстовое поле **Change password URL** (URL-адрес изменения пароля) в Google Apps вставьте значение поля **Изменить URL-адрес пароля**, скопированное на портале Azure. 

    д. В Google Apps в поле **Verification certificate** (Сертификат проверки) передайте сертификат, скачанный с портала Azure.

    f. Нажмите кнопку **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
 
### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-google-apps-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-google-apps-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-google-apps-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-google-apps-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-google-apps-test-user"></a>Создание тестового пользователя Google Apps

Цель этого раздела — создать пользователя с именем Britta Simon в Google Apps. Google Apps поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в Google Apps еще не существует, он создается при попытке доступа к Google Apps.

>[!NOTE] 
>Чтобы создать пользователя вручную, обратитесь к [группе поддержки Google](https://www.google.com/contact/).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Google Apps.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Google Apps, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Google Apps**.

    ![Настройка единого входа](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе, чтобы проверить параметры единого входа, откройте панель доступа по адресу [https://myapps.microsoft.com](active-directory-saas-access-panel-introduction.md), войдите с тестовой учетной записью и щелкните элемент **Google Apps** на панели доступа.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Руководство по настройке Google Apps для автоматической подготовки пользователей](active-directory-saas-google-apps-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_203.png

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png

[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png
