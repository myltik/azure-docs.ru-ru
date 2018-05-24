---
title: Руководство по интеграции Azure Active Directory с Printix | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Printix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4aea7320-b2d5-49e0-9b63-aeaff0f6fe66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: 23f77e791cbc171c5007795ebd2652fb3835efed
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-printix"></a>Руководство. Интеграция Azure Active Directory с Printix

В этом руководстве описано, как интегрировать Printix с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Printix обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Printix.
- Вы можете включить автоматический вход пользователей в Printix (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Printix, вам потребуется:

- подписка Azure AD;
- подписка Printix с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Printix из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-printix-from-the-gallery"></a>Добавление Printix из коллекции
Чтобы настроить интеграцию Printix с Azure AD, необходимо добавить Printix из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Printix из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Printix**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/tutorial_printix_search.png)

5. На панели результатов выберите **Printix** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/tutorial_printix_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Printix с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Printix соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Printix.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Printix.

Чтобы настроить и проверить единый вход Azure AD в Printix, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Printix](#creating-a-printix-test-user)** требуется для того, чтобы в Printix существовал пользователь Britta Simon, связанный с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Printix.

**Чтобы настроить единый вход Azure AD в Printix, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Printix** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Printix** сделайте следующее:

    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.printix.net`

    > [!NOTE] 
    > Это значение приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов Printix](mailto:support@printix.net). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_general_400.png)

6. Войдите в клиент Printix как администратор.

7. В меню вверху щелкните значок в правом верхнем углу и выберите**Authentication**(Проверка подлинности).
   
    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_06.png)

8. На вкладке **Setup** (Настройка) выберите **Enable Azure/Office 365 authentication** (Включить проверку подлинности Azure/Office 365).
   
    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_07.png)

9. На вкладке **Azure** введите URL-адрес метаданных федерации в поле ввода **Federation metadata document** (Документ метаданных федерации). 

    Вложите XML-файл метаданных, который вы скачали из Azure AD, в [службу поддержки Printix](mailto:support@printix.net). Служба отправит этот файл на сервер и предоставит вам URL-адрес метаданных федерации.
   
    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_08.png)
   
10. Нажмите кнопку **Test** (Тестировать). Если проверка будет выполнена успешно, нажмите кнопку **ОК**.
   
     После нажатия кнопки **тестирования** отобразится страница Azure Active Directory. Если на ней есть сообщение The test was successful (Проверка выполнена успешно), введите учетные данные для тестовой учетной записи Azure. Появится окно с сообщением Settings tested OK (Параметры успешно проверены). Нажмите кнопку **ОК**.
   
    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_09.png)

11. Щелкните кнопку **Save** (Сохранить) на странице **Authentication** (Проверка подлинности).


> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-printix-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-printix-test-user"></a>Создание тестового пользователя Printix

Цель этого раздела — создать пользователя с именем Britta Simon в приложении Printix. Приложение Printix поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению Printix (если он еще не создан). 

> [!NOTE]
> Чтобы создать пользователя вручную, необходимо обратиться в [службу поддержки Printix](mailto:support@printix.net).
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ему доступ к Printix.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Printix, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Printix**.

    ![Настройка единого входа](./media/active-directory-saas-printix-tutorial/tutorial_printix_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Printix на панели доступа, вы автоматически войдете в приложение Printix.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-printix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-printix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-printix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-printix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-printix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-printix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-printix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-printix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-printix-tutorial/tutorial_general_203.png

