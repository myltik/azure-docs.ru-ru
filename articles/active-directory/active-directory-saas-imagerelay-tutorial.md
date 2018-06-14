---
title: Руководство по интеграции Azure Active Directory с Image Relay | Документация Майкрософт
description: Сведения о настройке единого входа Azure Active Directory в Image Relay.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: b4ed73ca669ede9c206abd653a0b991edc2b1063
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343544"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Руководство по интеграции Azure Active Directory с приложением Image Relay

В этом учебнике описано, как интегрировать Image Relay с Azure Active Directory (Azure AD).

Интеграция Image Relay с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Image Relay.
- Вы можете включить автоматический вход пользователей в Image Relay (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Image Relay, вам потребуется:

- подписка Azure AD;
- подписка Image Relay с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Image Relay из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-image-relay-from-the-gallery"></a>Добавление Image Relay из коллекции
Чтобы настроить интеграцию Image Relay с Azure AD, необходимо добавить Image Relay из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Image Relay из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Image Relay**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_search.png)

5. На панели результатов выберите **Image Relay** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Image Relay для тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Image Relay соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Image Relay.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Image Relay.

Чтобы настроить и проверить единый вход Azure AD в Image Relay, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Image Relay](#creating-an-image-relay-test-user)** требуется для создания пользователя Britta Simon в Image Relay, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Image Relay.

**Чтобы настроить единый вход Azure AD в Image Relay, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Image Relay** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

3. В разделе **Домены и URL-адреса Image Relay** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.imagerelay.com/`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Image Relay](http://support.imagerelay.com/). 
 


4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Image Relay** щелкните **Настроить Image Relay**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_configure.png) 

7. В другом окне браузера войдите на сайт компании Image Relay с правами администратора.

8. На панели инструментов вверху экрана выберите рабочую нагрузку **Users & Permissions** (Пользователи и разрешения).
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

9. Щелкните **Создать новое разрешение**.
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png)

10. В рабочей нагрузке **Single Sign On Settings** (Параметры единого входа) установите флажок **This Group can only sign-in via Single Sign On** (Эта группа может входить только через единый вход) и нажмите кнопку **Сохранить**.
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

11. Откройте **Параметры учетной записи**.
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

12. Выберите рабочую нагрузку **Параметры единого входа** .
    
     ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

13. В диалоговом окне **SAML Settings** (Параметры SAML) выполните следующие действия.
    
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. В текстовом поле **URL-адрес входа** вставьте значение **URL-адреса службы единого входа**, скопированное на портале Azure.

    Б. В текстовом поле **URL-адрес выхода** вставьте значение **URL-адреса службы единого выхода**, скопированное на портале Azure.

    c. В поле **Name Id Format** (Формат ИД имени) выберите **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    d. В разделе **Binding Options for Requests from the Service Provider (Image Relay)** (Обязательные параметры для запросов от поставщика услуг (Image Relay)) выберите **POST Binding** (Привязка POST).

    д. В разделе **Сертификат X.509** щелкните **Обновить сертификат**.

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Откройте скачанный сертификат в блокноте, а затем скопируйте и вставьте его содержимое в текстовое поле X.509 Certificate (Сертификат X.509).

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    ж. В разделе **Just-In-Time User Provisioning** (JIT-подготовка пользователей) выберите **Enable Just-In-Time User Provisioning** (Включить JIT-подготовку пользователей).

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Выберите группу разрешений (например, **SSO Basic**(Базовый единый вход)), которой будет разрешено выполнять вход только через службу единого входа.

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-an-image-relay-test-user"></a>Создание тестового пользователя Image Relay

Цель этого раздела — создать пользователя с именем Britta Simon в Image Relay.

**Чтобы создать пользователя с именем Britta Simon в Image Relay, выполните следующие действия:**

1. Войдите на cайт компании Image Relay в качестве администратора.

2. Откройте раздел **Users & Permissions** (Пользователи и разрешения) и выберите параметр **Create SSO User** (Создать пользователя единого входа).
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Введите **Адрес электронной почты**, **Имя**, **Фамилию** и **Организацию** пользователя, которого нужно подготовить, и выберите группу разрешений (например, "Базовый единый вход"). Эта группа сможет выполнять вход только с помощью единого входа.
   
    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

4. Нажмите кнопку **Создать**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon, предоставив этому пользователю доступ к Image Relay.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Image Relay, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Image Relay**.

    ![Настройка единого входа](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.    

Щелкнув плитку Image Relay на панели доступа, вы автоматически войдете в приложение Image Relay.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png

