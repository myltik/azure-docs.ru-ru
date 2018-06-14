---
title: Руководство по интеграции Azure Active Directory с Samanage | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: e96cbbadb3d36cdc022052acb5a9a42da35db263
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352673"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Руководство. Интеграция Azure Active Directory с Samanage

В этом руководстве описано, как интегрировать Samanage с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Samanage обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Samanage.
- Вы можете включить автоматический вход пользователей в Samanage (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Samanage, вам потребуется:

- подписка Azure AD;
- подписка Samanage с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Samanage из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-samanage-from-the-gallery"></a>Добавление Samanage из коллекции.
Чтобы настроить интеграцию Samanage с Azure AD, необходимо добавить Samanage из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Samanage из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Samanage**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_search.png)

5. На панели результатов выберите **Samanage** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Samanage с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Samanage соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Samanage.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Samanage.

Чтобы настроить и проверить единый вход Azure AD в Samanage, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Samanage](#creating-a-samanage-test-user)** требуется для того, чтобы в Samanage существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Samanage.

**Чтобы настроить единый вход Azure AD в Samanage, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Samanage** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Samanage** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическими URL-адресом для входа и идентификатором, как описано позже в данном руководстве. Для получения дополнительных сведений обратитесь к [группе поддержки клиентов Samanage](https://www.samanage.com/support).    
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-samanage-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Samanage** щелкните **Настроить Samanage**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и идентификатор сущности SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_configure.png) 

7. В другом окне веб-браузера войдите на сайт Samanage компании в качестве администратора.

8. Щелкните **Панель мониторинга** и выберите **Настройка** в левой области навигации.
   
    ![Панель мониторинга](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Панель мониторинга")

9. Щелкните **Единый вход**.
   
    ![Единый вход](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Единый вход")

10. Перейдите в раздел **Login using SAML** (Вход с помощью SAML) и выполните следующие действия.
   
    ![Вход с помощью SAML](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "Вход с помощью SAML")
 
    a. Установите флажок **Enable Single Sign-On with SAML**(Включить единый вход с помощью SAML).  
 
    Б. В текстовое поле **Identity Provider URL** (URL-адрес поставщика удостоверений) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.    
 
    c. Убедитесь, что значение **Login URL** (URL-адрес входа) соответствует значению **URL-адрес входа** из раздела **Домены и URL-адреса приложения Samanage** на портале Azure.
 
    d. В текстовое поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.
 
    д. В текстовое поле **SAML Issuer** (Издатель SAML) введите универсальный код ресурса (URI) для идентификатора приложения, заданный для поставщика удостоверений.
 
    f. Откройте в Блокноте сертификат в кодировке Base64, скачанный с портала Azure, скопируйте содержимое сертификата в буфер обмена, а затем вставьте его в текстовое поле **Paste your Identity Provider x.509 Certificate below** (Скопируйте сюда сертификат x.509 своего поставщика удостоверений).
 
    ж. Установите флажок **Create users if they do not exist in Samanage**(Создавать пользователей, если они не существуют в Samanage).
 
    h. Нажмите кнопку **Обновить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
 
### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-samanage-test-user"></a>Создание тестового пользователя в приложении Samanage

Чтобы пользователи Azure AD могли выполнять вход в Samanage, они должны быть подготовлены в Samanage.  
В случае Samanage подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на корпоративный сайт Samanage в качестве администратора.

2. Щелкните **Dashboard** (Панель мониторинга) и выберите **Setup** (Настройка) в левой области навигации.
   
    ![Настройка](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "Настройка")

3. Откройте вкладку **Пользователи** .
   
    ![Пользователи](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "Пользователи")

4. Щелкните **Новый пользователь**.
   
    ![Новый пользователь](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "Новый пользователь")

5. Заполните текстовые поля **Name** (Имя) и **Email Address** (Адрес электронной почты) данными из учетной записи Azure Active Directory, которую необходимо подготовить, а затем нажмите кнопку **Create user** (Создать пользователя).
   
    ![Создание пользователя](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "Создание пользователя")
   
   >[!NOTE]
   >Владелец учетной записи Azure Active Directory получит по электронной почте сообщение со ссылкой для активации учетной записи. Вы можете использовать любые другие инструменты создания учетных записей пользователя Samanage или API, предоставляемые Samanage для подготовки учетных записей пользователя Azure Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Samanage.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Samanage, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Samanage**.

    ![Настройка единого входа](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Samanage на панели доступа, вы автоматически войдете в приложение Samanage.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_203.png

