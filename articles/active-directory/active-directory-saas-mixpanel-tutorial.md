---
title: Руководство по интеграции Azure Active Directory с Mixpanel | Документация Майкрософт
description: Сведения о настройке единого входа Azure Active Directory в Mixpanel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 0dec216ea2bc01c3ee3f5afae0ec4966c51afe00
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34338396"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Руководство. Интеграция Azure Active Directory с Mixpanel

В этом руководстве описано, как интегрировать Mixpanel с Azure Active Directory (Azure AD).

Интеграция Mixpanel с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Mixpanel.
- Вы можете включить автоматический вход пользователей в Mixpanel (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Mixpanel, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Mixpanel.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Mixpanel из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-mixpanel-from-the-gallery"></a>Добавление Mixpanel из коллекции
Чтобы настроить интеграцию Mixpanel с Azure AD, необходимо добавить Mixpanel из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Mixpanel из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Mixpanel**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_search.png)

5. На панели результатов выберите **Mixpanel** и щелкните **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описаны настройка и проверка единого входа Azure AD в Mixpanel с использованием тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходимо знать, какому пользователю в Azure AD соответствует пользователь в Mixpanel. То есть необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Mixpanel.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Mixpanel.

Чтобы настроить и проверить единый вход Azure AD в Mixpanel, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Mixpanel](#creating-a-mixpanel-test-user)** требуется для создания в Mixpanel пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Mixpanel.

**Чтобы настроить единый вход Azure AD в Mixpanel, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Mixpanel** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Mixpanel** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_url.png)

     В текстовом поле **URL-адрес для входа** введите URL-адрес в формате `https://mixpanel.com/login/`.

    > [!NOTE] 
    > Зарегистрируйтесь на сайте [https://mixpanel.com/register/](https://mixpanel.com/register/), настройте учетные данные для входа в систему и обратитесь в [службу поддержки Mixpanel](mailto:support@mixpanel.com), чтобы включить параметры единого входа для своего клиента. Вы также можете получить URL-адрес единого входа, обратившись в службу поддержки Mixpanel. 
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Mixpanel** щелкните **Настроить Mixpanel**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_configure.png) 

7. В отдельном окне браузера войдите в приложение Mixpanel под учетной записью администратора.

8. Щелкните маленький значок **шестеренки** в левом нижнем углу страницы. 
   
    ![Единый вход в Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_06.png) 

9. Откройте вкладку **Access security** (Безопасность доступа) и щелкните **Change settings** (Изменить параметры).
   
    ![Параметры Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_08.png) 

10. На странице диалогового окна **Change your certificate** (Изменение сертификата) щелкните **Choose file** (Выбрать файл), чтобы передать скачанный сертификат, а затем щелкните **Next** (Далее).
   
    ![Параметры Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_09.png) 

11.  В текстовом поле URL-адреса проверки подлинности диалоговой страницы **Change your authentication URL** (Изменение URL-адреса проверки подлинности) вставьте значение **URL-адрес службы единого входа SAML**, которое скопировано с портала Azure, и щелкните **Next** (Далее).
   
   ![Параметры Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_10.png) 

12. Нажмите кнопку **Done**(Готово).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-mixpanel-test-user"></a>Создание тестового пользователя Mixpanel

Цель этого раздела — создать пользователя с именем Britta Simon в Mixpanel. 

1. Войдите на веб-сайт компании Mixpanel в качестве администратора.

2. Щелкните маленькую кнопку с шестеренкой в левом нижнем углу страницы, чтобы открыть окно **Параметры** .

3. Откройте вкладку **Команды** .

4. В текстовое поле **член команды** введите электронный адрес пользователя Britta Simon на портале Azure.
   
    ![Параметры Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. Нажмите кнопку **Пригласить**. 

> [!Note]
> Пользователь получит по электронной почте письмо с предложением настроить свой профиль.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Mixpanel.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Mixpanel, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Mixpanel**.

    ![Настройка единого входа](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Mixpanel на панели доступа, вы автоматически войдете в приложение Mixpanel.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_203.png

