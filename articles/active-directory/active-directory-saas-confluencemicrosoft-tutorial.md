---
title: Руководство по интеграции Azure Active Directory с Confluence SAML SSO by Microsoft | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Confluence SAML SSO by Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: c89be4917bb266bcd0244f2409bfa703dd3e523c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Руководство по интеграции Azure Active Directory с Confluence SAML SSO by Microsoft

В этом руководстве описано, как интегрировать Confluence SAML SSO by Microsoft с Azure Active Directory (Azure AD).

Интеграция Azure AD с Confluence SAML SSO by Microsoft обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Confluence SAML SSO by Microsoft.
- Можно включить автоматический вход пользователей в Confluence SAML SSO by Microsoft, то есть единый вход, с учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="description"></a>Описание:

Используйте учетную запись Microsoft Azure Active Directory с сервером Atlassian Confluence, чтобы включить единый вход. Таким образом все пользователи вашей организации смогут использовать учетные данные Azure AD для входа в приложение Confluence. Этот подключаемый модуль для федерации использует SAML 2.0.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Confluence SAML SSO by Microsoft, вам потребуется следующее:

- подписка Azure AD;
- серверное приложение Confluence, установленное на 64-разрядной версии сервера Windows (в локальной среде или облачной инфраструктуре IaaS);
- поддержка HTTPS на сервере Confluence;
- поддерживаемые версии подключаемого модуля Confluence указаны в разделе ниже;
- сервер Confluence должен быть доступен через Интернет, в частности, на странице входа Azure AD для аутентификации, и должен иметь возможность получать маркер из Azure AD;
- в Confluence должны быть настроены учетные данные администратора;
- в Confluence должна быть отключена поддержка WebSudo;
- в серверном приложении Confluence должен быть создан тестовый пользователь.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду Confluence для проверки действий в этом учебнике. Сначала протестируйте интеграцию в среде разработки или промежуточной среде приложения, а затем используйте ее в рабочей среде.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Поддерживаемые версии Confluence 

Сейчас поддерживаются следующие версии Confluence:

- Confluence: версии c 5.0 по 5.10

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Confluence SAML SSO by Microsoft из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Добавление Confluence SAML SSO by Microsoft из коллекции
Чтобы настроить интеграцию Confluence SAML SSO by Microsoft с Azure AD, необходимо добавить Confluence SAML SSO by Microsoft из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Confluence SAML SSO by Microsoft из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Confluence SAML SSO by Microsoft**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_search.png)

5. На панели результатов выберите **Confluence SAML SSO by Microsoft** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описывается настройка и проверка единого входа Azure AD в Confluence SAML SSO by Microsoft с использованием тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходимо знать, какой пользователь в Confluence SAML SSO by Microsoft соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Confluence SAML SSO by Microsoft.

Чтобы настроить и проверить единый вход Microsoft Azure AD в Confluence SAML SSO by Microsoft, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Confluence SAML SSO by Microsoft](#creating-a-confluence-saml-sso-by-microsoft-test-user)** требуется для того, чтобы в Confluence SAML SSO by Microsoft существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Confluence SAML SSO by Microsoft.

**Чтобы настроить единый вход Azure AD в Confluence SAML SSO by Microsoft, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **Confluence SAML SSO by Microsoft** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Настройка единого входа](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Confluence SAML SSO by Microsoft** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<domain:port>/plugins/servlet/saml/auth`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<domain:port>/`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<domain:port>/plugins/servlet/saml/auth`.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Если это именованный URL-адрес, то порт указывать необязательно. Эти значения предоставляются во время настройки подключаемого модуля Confluence, которая описывается далее в этом руководстве.

4. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот.
    
    ![Настройка единого входа](./media/active-directory-saas-Confluencemicrosoft-tutorial/tutorial_metadataurl.png)
     
5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-Confluencemicrosoft-tutorial/tutorial_general_400.png)

6. В другом окне браузера войдите в свой экземпляр Confluence в качестве администратора.

7. Наведите указатель мыши на шестеренку и щелкните **Add-ons** (Надстройки).
    
    ![Настройка единого входа](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon1.png)

8. Скачайте подключаемый модуль из [Центра загрузки Майкрософт](https://www.microsoft.com/en-us/download/details.aspx?id=56503). Вручную передайте подключаемый модуль, предоставленный корпорацией Майкрософт, воспользовавшись меню **Upload add-on** (Отправка надстройки). Загрузка подключаемого модуля выполняется в соответствии с [соглашением об обслуживании Майкрософт](https://www.microsoft.com/en-us/servicesagreement/). 
    
    ![Настройка единого входа](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon12.png)

9. После установки подключаемый модуль появится в разделе **Управление надстройками**, подраздел **Установленные пользователем**. Щелкните **Configure** (Настройка), чтобы настроить новый подключаемый модуль.
    
    ![Настройка единого входа](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon13.png)

10. Выполните следующие действия на странице настройки:

    ![Настройка единого входа](./media/active-directory-saas-Confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Убедитесь, что с приложением сопоставлен только один сертификат, чтобы при разрешении метаданных не возникла ошибка. Если имеется несколько сертификатов, то при разрешении метаданных администратор увидит сообщение об ошибке.

    a. В текстовое поле **URL-адрес метаданных** вставьте значение **URL-адреса метаданных федерации приложений**, скопированное на портале Azure, и нажмите кнопку **Разрешить**. Будет прочитан URL-адрес метаданных поставщика удостоверений, а также будут заполнены все поля сведений.

    Б. Скопируйте значения **идентификатора, URL-адреса ответа и URL-адреса входа** и вставьте их в соответствующие поля **идентификатора, URL-адреса ответа и URL-адреса входа** в разделе **Домены и URL-адреса приложения Confluence SAML SSO by Microsoft** на портале Azure.

    c. В поле **Имя кнопки входа** введите имя кнопки, которую должны видеть на экране входа пользователи вашей организации.

    d. Для параметра **SAML User ID Locations** (Расположения идентификатора пользователя SAML) укажите значение **User ID is in the NameIdentifier element of the Subject statement** (Идентификатор пользователя указан в элементе NameIdentifier утверждения Subject) или **User ID is in an Attribute element** (Идентификатор пользователя указан в элементе Attribute).  Этим идентификатором должен быть идентификатор пользователя Confluence. Если идентификатор пользователя не совпадет, система не позволит пользователям выполнить вход. 

    > [!Note]
    > По умолчанию идентификатор пользователя SAML указан в идентификаторе имени. Его можно заменить атрибутом и ввести имя соответствующего атрибута.
    
    д. Если выбран параметр **Идентификатор пользователя указан в элементе Attribute**, то в текстовом поле **Имя атрибута** введите имя атрибута, в котором ожидается идентификатор пользователя. 

    f. При использовании федеративного домена (например, AD FS и т. д.) для Azure AD выберите параметр **Включить обнаружение домашней области** и настройте **доменное имя**.
    
    ж. В поле **Доменное имя** введите доменное имя, если вы используете вход на основе AD FS.

    h. Установите флажок **Включить единый выход**, если после выхода пользователя из Confluence требуется выходить и из Azure AD. 

    i. Нажмите кнопку **Сохранить**, чтобы сохранить изменения.

    > [!NOTE]
    > Дополнительные сведения об установке и устранении неполадок см. в [руководстве администратора соединителя единого входа MS Confluence](ms-confluence-jira-plugin-adminguide.md) и в [часто задаваемых вопросах](ms-confluence-jira-plugin-faq.md).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-confluencemicrosoft-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-confluence-saml-sso-by-microsoft-test-user"></a>Создание тестового пользователя Confluence SAML SSO by Microsoft

Чтобы пользователи Azure AD могли входить на локальный сервер Confluence, их необходимо подготовить в Confluence SAML SSO by Microsoft. Для Confluence SAML SSO by Microsoft подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на локальный сервер Confluence как администратор.

2. Наведите указатель мыши на шестеренку и щелкните **User management** (Управление пользователями).

    ![Добавление сотрудника](./media/active-directory-saas-confluencemicrosoft-tutorial/user1.png) 

3. В разделе "Users" (Пользователи) выберите вкладку **Add users** (Добавление пользователей). На диалоговой странице **Add a User** (Добавление пользователя) выполните следующее.

    ![Добавление сотрудника](./media/active-directory-saas-confluencemicrosoft-tutorial/user2.png) 

    a. В текстовом поле **Username** (Имя пользователя) введите электронный адрес пользователя, например Britta Simon.

    Б. В текстовом поле **Full Name** (Полное имя) введите полное имя пользователя, например Britta Simon.

    c. В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например Brittasimon@contoso.com.

    d. В текстовом поле **Password** (Пароль) введите пароль пользователя Britta Simon.

    д. Щелкните **Confirm Password** (Подтвердить пароль) и повторно введите пароль.
    
    f. Нажмите кнопку **Добавить**.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Confluence SAML SSO by Microsoft.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Confluence SAML SSO by Microsoft, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Confluence SAML SSO by Microsoft**.

    ![Настройка единого входа](./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Confluence SAML SSO by Microsoft" на панели доступа, вы автоматически войдете в приложение Confluence SAML SSO by Microsoft.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-confluencemicrosoft-tutorial/tutorial_general_203.png
