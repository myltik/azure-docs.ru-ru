---
title: "Руководство по интеграции Azure Active Directory с Nexonia | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Nexonia."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a93b771a-9bc3-444a-bdc0-457f8bb7e780
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: a6241afa7398684d9315ab401ecdb5b2abd824c5
ms.lasthandoff: 04/22/2017


---
# <a name="tutorial-azure-active-directory-integration-with-nexonia"></a>Руководство по интеграции Azure Active Directory с Nexonia

В этом руководстве описано, как интегрировать Nexonia с Azure Active Directory.

Интеграция Azure AD с приложением Nexonia обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Nexonia.
- Вы можете включить автоматический вход пользователей в Nexonia (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Nexonia, вам потребуется:

- подписка Azure AD;
- подписка на Nexonia с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Nexonia из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-nexonia-from-the-gallery"></a>Добавление Nexonia из коллекции
Чтобы настроить интеграцию Nexonia с Azure AD, необходимо добавить Nexonia из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Nexonia из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **Nexonia**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_search.png)

5. На панели результатов выберите **Nexonia** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Nexonia с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход Azure AD, необходимо знать, какой пользователь в Nexonia соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Nexonia.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Nexonia.

Чтобы настроить и проверить единый вход Azure AD в Nexonia, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Nexonia](#creating-a-nexonia-test-user)** требуется для создания в Nexonia пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Nexonia.

>[!Note]
>Если при интеграции возникают проблемы, воспользуйтесь этой [ссылкой](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?WT.mc_id=UI_AAD_Enterprise_Apps_SupportOrTroubleshooting) для ознакомления с руководством по устранению неполадок. Если вам по-прежнему не удается найти решение, направьте запрос в службу поддержки через портал Azure.

**Чтобы настроить единый вход Azure AD в Nexonia, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Nexonia** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Nexonia** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_url.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://system.nexonia.com/assistant/saml.do?orgCode=<organizationcode>`.

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него нужно указать фактический URL-адрес ответа. Чтобы получить это значение, обратитесь в [службу поддержки Nexonia](http://www.nexonia.com/contact-us/). 


4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-nexonia-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Nexonia** щелкните **Настроить Nexonia**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML**  из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_configure.png) 

7. Чтобы настроить единый вход для своего приложения, обратитесь в [службу поддержки Nexonia](http://www.nexonia.com/contact-us/) и предоставьте следующие сведения:

    • скачанный **сертификат**

    • **идентификатор сущности SAML**;

    • **URL-адрес службы единого входа SAML**;

    • **URL-адрес выхода**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-nexonia-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-nexonia-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-nexonia-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-nexonia-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-nexonia-test-user"></a>Создание тестового пользователя Nexonia

В этом разделе описано, как создать пользователя Britta Simon в приложении Nexonia. Обратитесь в [службу поддержки Nexonia](http://www.nexonia.com/contact-us/), чтобы добавить пользователей на платформу Nexonia. Перед использованием единого входа необходимо создать и активировать пользователей.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Nexonia.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Nexonia, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Nexonia**.

    ![Настройка единого входа](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Nexonia на панели доступа, вы автоматически войдете в приложение Nexonia.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_203.png


