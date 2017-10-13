---
title: "Руководство по интеграции Azure Active Directory с BambooHR | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и BambooHR."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: jeedes
ms.openlocfilehash: 06bf91b0e598fd3d8e644378efdb753611ee1ebc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Руководство по интеграции Azure Active Directory с BambooHR

В этом руководстве описано, как интегрировать BambooHR с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением BambooHR обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к BambooHR.
- Вы можете включить автоматический вход пользователей в BambooHR (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с BambooHR, вам потребуется:

- подписка Azure AD;
- подписка BambooHR с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление BambooHR из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-bamboohr-from-the-gallery"></a>Добавление BambooHR из коллекции
Чтобы настроить интеграцию BambooHR с Azure AD, необходимо добавить BambooHR из коллекции в список управляемых приложений SaaS.

**Чтобы добавить BambooHR из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **BambooHR**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_search.png)

5. На панели результатов выберите **BambooHR** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в приложение BambooHR с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в BambooHR соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в BambooHR.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в BambooHR.

Чтобы настроить и проверить единый вход Azure AD в BambooHR, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя BambooHR](#creating-a-bamboohr-test-user)** нужно для того, чтобы в BambooHR также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении BambooHR.

**Чтобы настроить единый вход Azure AD в BambooHR, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **BambooHR** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. В разделе **Домены и URL-адреса приложения BambooHR** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company>.bamboohr.com`
    
    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Для получения этого значения обратитесь к [группе поддержки BambooHR](https://www.bamboohr.com/contact.php). 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация BambooHR** щелкните **Настроить BambooHR**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

6. В другом окне веб-браузера войдите на свой корпоративный веб-сайт BambooHR в качестве администратора.

7. На домашней странице выполните следующие действия.
   
    ![Единый вход](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "Единый вход")   

    а. Нажмите **Приложения**.
   
    b. В меню приложений в левой части окна нажмите **Единый вход**.
   
    c. Нажмите **Единый вход SAML**.

8. В разделе **Единый вход SAML** выполните следующие действия.
   
    ![Единый вход SAML](./media/active-directory-saas-bamboo-hr-tutorial/ic796692.png "Единый вход SAML")
   
    а. Вставьте значение **URL-адрес службы единого входа SAML** в текстовое поле **SSO Login Url** (URL-адрес единого входа).
      
    b. Откройте в Блокноте сертификат в кодировке Base-64, скачанный с портала Azure, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **X.509 Certificate** (Сертификат X.509).
   
    c. Щелкните **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-bamboohr-test-user"></a>Создание тестового пользователя BambooHR

Чтобы пользователи Azure AD могли выполнять вход в BambooHR, они должны быть подготовлены в BambooHR.  

В случае с BambooHR подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на свой веб-сайт **BambooHR** в качестве администратора.

2. На панели инструментов в верхней части экрана нажмите **Параметры**.
   
    ![Параметр](./media/active-directory-saas-bamboo-hr-tutorial/ic796694.png "Параметр")

3. Нажмите **Обзор**.

4. На панели навигации в левой части экрана последовательно выберите параметры **Безопасность \> Пользователи**.

5. Введите имя пользователя, пароль и адрес электронной почты для действующей учетной записи AAD, которую вы хотите подготовить, в соответствующие текстовые поля.

6. Щелкните **Сохранить**.
        
>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя BambooHR или API, предоставляемые BambooHR для подготовки учетных записей пользователя AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к BambooHR.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в BambooHR, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **BambooHR**.

    ![Настройка единого входа](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "BambooHR" на панели доступа, вы автоматически войдете в приложение BambooHR.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_203.png

