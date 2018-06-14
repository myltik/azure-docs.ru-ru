---
title: Учебник. Интеграция Azure Active Directory с Flatter Files | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Flatter Files.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: 2f2a068523f64eb381239560108c2a961da371be
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343216"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Руководство. Интеграция Azure Active Directory с Flatter Files

В этом руководстве описано, как интегрировать приложение Flatter Files с Azure Active Directory (Azure AD).

Интеграция Azure AD с Flatter Files обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Flatter Files.
- Вы можете включить автоматический вход пользователей в Flatter Files (единый вход) с помощью учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Flatter Files, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа в Flatter Files.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Flatter Files из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-flatter-files-from-the-gallery"></a>Добавление Flatter Files из коллекции
Чтобы настроить интеграцию Flatter Files с Azure AD, необходимо добавить Flatter Files из коллекции в список управляемых приложений SaaS.

**Как добавить Flatter Files из коллекции**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Flatter Files**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_search.png)

5. На панели результатов выберите **Flatter Files** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Flatter Files с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Flatter Files соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Flatter Files.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Flatter Files.

Чтобы настроить и проверить единый вход Azure AD в Flatter Files, вам потребуется выполнить следующие действия.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Flatter Files](#creating-a-flatter-files-test-user)** требуется для создания во Flatter Files пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Flatter Files.

**Как настроить единый вход Azure AD в Flatter Files**

1. На портале Azure на странице интеграции с приложением **Flatter Files** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_samlbase.png)

3. В разделе **Домен и URL-адреса Flatter Files** не нужно выполнять никаких действий, поскольку приложение уже предварительно интегрировано с Azure.

    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_url.png)
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Flatter Files** щелкните **Настроить Flatter Files**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_configure.png) 

7. Войдите в приложение Flatter Files с правами администратора.

8. Щелкните **Панель мониторинга**. 
   
    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  

9. Выберите элемент **Settings** (Параметры), а затем выполните следующие действия на вкладке **Company** (Компания). 
   
    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Установите флажок **Use SAML 2.0 For Authentication**(Использовать SAML 2.0 для проверки подлинности).
    
    Б. Нажмите кнопку **Configure SAML** (Настроить SAML).

8. В диалоговом окне **SAML Configuration** (Настройка SAML) выполните следующие действия. 
   
    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. В поле **Домен** укажите свой зарегистрированный домен.
   
    >[!NOTE]
    >Если у вас нет зарегистрированного домена, обратитесь в службу поддержки Flatter Files по адресу [support@flatterfiles.com](mailto:support@flatterfiles.com). 
    
    Б. В текстовое поле **URL-адрес поставщика удостоверений** вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.
   
    c.  Откройте сертификат в кодировке Base-64 в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат поставщика удостоверений**.

    d. Нажмите кнопку **Обновить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-flatter-files-test-user"></a>Создание тестового пользователя Flatter Files

Цель этого раздела — создать пользователя с именем Britta Simon в Flatter Files.

**Как создать пользователя с именем Britta Simon в Flatter Files**

1. Войдите на сайт своей компании в службе **Flatter Files** с правами администратора.

2. В области навигации слева щелкните **Settings** (Параметры), а затем выберите вкладку **Users** (Пользователи).
   
    ![Создание пользователя Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Нажмите кнопку **Add User**(Добавить пользователя). 

4. На странице **Добавление пользователя** выполните следующие действия.
   
    ![Создание пользователя Flatter Files](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. В текстовом поле **Имя** введите **Britta**.
   
    Б. В текстовом поле **Фамилия** введите **Simon**. 
   
    c. В текстовом поле **Email Address** (Адрес электронной почты) введите адрес электронной почты пользователя Britta Simon на портале Azure.
   
    d. Нажмите кнопку **Submit**(Отправить).   


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Flatter Files.

![Назначение пользователя][200] 

**Как назначить пользователя Britta Simon в Flatter Files**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Flatter Files**.

    ![Настройка единого входа](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкните элемент Flatter Files на панели доступа, чтобы автоматически войти в приложение Flatter Files.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png

