---
title: Учебник. Интеграция Azure Active Directory с IBM Kenexa Survey Enterprise | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в IBM Kenexa Survey Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 88e0072adeeebaf6c9e964db28a45f6fe038fddf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Руководство по интеграции Azure Active Directory с IBM Kenexa Survey Enterprise

В этом руководстве описано, как интегрировать IBM Kenexa Survey Enterprise с Azure Active Directory (Azure AD).

Интеграция IBM Kenexa Survey Enterprise с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать, кто будет иметь доступ к IBM Kenexa Survey Enterprise.
- Вы можете включить автоматический вход пользователей в IBM Kenexa Survey Enterprise (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с IBM Kenexa Survey Enterprise, вам потребуется следующее:

- подписка Azure AD;
- подписка на IBM Kenexa Survey Enterprise с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для тестирования действий, выполняемых в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом руководстве, состоит из двух основных стандартных блоков.

* Добавление IBM Kenexa Survey Enterprise из коллекции.
* Настройка и проверка единого входа Azure AD.

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Добавление IBM Kenexa Survey Enterprise из коллекции
Чтобы настроить интеграцию IBM Kenexa Survey Enterprise в Azure AD, следует добавить IBM Kenexa Survey Enterprise из коллекции в список управляемых приложений SaaS.

Чтобы добавить IBM Kenexa Survey Enterprise из коллекции, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com) в левой области нажмите кнопку **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить приложение, нажмите кнопку **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **IBM Kenexa Survey Enterprise**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

5. Из списка результатов выберите **IBM Kenexa Survey Enterprise**, а затем нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![IBM Kenexa Survey Enterprise в списке результатов](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в IBM Kenexa Survey Enterprise с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в IBM Kenexa Survey Enterprise соответствует пользователю в Azure AD. Иными словами, в Azure AD необходимо установить связь между пользователем Azure AD и соответствующим пользователем в IBM Kenexa Survey Enterprise.

Чтобы установить эту связь, назначьте **имя пользователя** в IBM Kenexa Survey Enterprise в качестве значения **имени пользователя** в Azure AD.

Чтобы настроить и проверить единый вход Azure AD в IBM Kenexa Survey Enterprise, выполните действия в следующих двух стандартных блоках.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD

В этом разделе вы включите единый вход Azure AD на портале Azure и настроите единый вход в приложении IBM Kenexa Survey Enterprise. Для этого выполните следующее.

1. На портале Azure на странице интеграции с приложением **IBM Kenexa Survey Enterprise** щелкните **Единый вход**.

    ![Ссылка для настройки единого входа для IBM Kenexa Survey Enterprise][4]

2. В диалоговом окне **Единый вход** из списка **Режим** выберите значение **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

3. В разделе **Домены и URL-адреса приложения IBM Kenexa Survey Enterprise** выполните следующие действия.

    ![Сведения о домене и URL-адресах IBM Kenexa Survey Enterprise](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://surveys.kenexa.com/<companycode>`.

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`.

    > [!NOTE] 
    > Приведенные выше значения используются только для примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Чтобы получить фактические значения, обратитесь к [группе поддержки IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)** и сохраните файл сертификата на компьютере.

    ![Ссылки для скачивания сертификата в кодировке Base64](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    Приложение IBM Kenexa Survey Enterprise ожидает утверждения SAML (язык разметки заявлений системы безопасности) в определенном формате, поэтому необходимо добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. Полученное в ответ значение утверждения идентификатора пользователя должно совпадать с идентификатором единого входа, настроенным в системе Kenexa. Чтобы сопоставить соответствующий идентификатор пользователя в организации для применения единого входа на основе протокола IDP, обратитесь к [группе поддержки IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). 

    По умолчанию Azure AD в качестве идентификатора пользователя задает значение имени участника-пользователя (UPN). Это значение можно изменить на вкладке **Атрибут**, как показано на следующем снимке экрана. Интеграция заработает только после правильной настройки сопоставления.
    
    ![Диалоговое окно "Атрибуты пользователя"](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_attribute.png)   

5. Выберите команду **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_400.png)

6. Чтобы открыть окно **Настройка единого входа**, в разделе **Конфигурация IBM Kenexa Survey Enterprise** щелкните **Настроить IBM Kenexa Survey Enterprise**. 
 
    ![Ссылка "Настройка IBM Kenexa Survey Enterprise"](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

7. Скопируйте значения **URL-адрес выхода**, **SAML Entity ID** (Идентификатор сущности SAML) и **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML) из раздела **Краткий справочник**.

8. В окне **Настройка единого входа** из раздела **Краткий справочник** скопируйте значения **URL-адрес выхода**, **SAML Entity ID** (Идентификатор сущности SAML) и **SAML single sign-on Service URL** (URL-адрес службы единого входа SAML).

9. Чтобы настроить единый вход на стороне **IBM Kenexa Survey Enterprise**, отправьте скачанный **сертификат в кодировке Base64**, **URL-адрес выхода**, **идентификатор сущности SAML** и **URL-адрес службы единого входа SAML** [группе поддержки IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления приложения из раздела **Active Directory** > **Корпоративные приложения** просто выберите вкладку **Единый вход** и ознакомьтесь со встроенной документацией, воспользовавшись разделом **Настройка** в нижней части страницы. Чтобы узнать больше, ознакомьтесь со [встроенной документацией Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе вы создадите на портале Azure тестового пользователя Britta Simon, выполнив следующие действия.

![Создание тестового пользователя Azure AD][100]

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_01.png) 

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.
 
    ![Кнопка "Добавить"](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 

4. В диалоговом окне **Пользователь** сделайте следующее.
 
    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>Создание тестового пользователя IBM Kenexa Survey Enterprise

В этом разделе описано, как создать пользователя Britta Simon в приложении IBM Kenexa Survey Enterprise. 

Чтобы создать пользователей в системе IBM Kenexa Survey Enterprise и сопоставить с ними идентификатор единого входа, вы можете обратиться к [группе поддержки IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw). Это значение идентификатора единого входа необходимо также сопоставить со значением идентификатора пользователя из Azure AD. Эти заданные по умолчанию параметры можно изменить на вкладке **Атрибут**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к IBM Kenexa Survey Enterprise.

![Назначение роли пользователя][200] 

Чтобы назначить пользователя Britta Simon в IBM Kenexa Survey Enterprise, выполните следующие действия.

1. На портале Azure откройте представление **Приложения**, перейдите к представлению **Каталог**, выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Ссылки "Корпоративные приложения" и "Все приложения"][201] 

2. Из списка **Приложения** выберите **IBM Kenexa Survey Enterprise**.

    ![Ссылка на IBM Kenexa Survey Enterprise в списке "Приложения"](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

3. В области слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202] 

4. Нажмите кнопку **Добавить**, затем в области **Добавление назначения** щелкните **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке **Пользователи** выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе вы с помощью панели доступа выполните проверку конфигурации единого входа Azure AD.

Щелкнув элемент **IBM Kenexa Survey Enterprise** на панели доступа, вы автоматически войдете в приложение IBM Kenexa Survey Enterprise.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png

 
