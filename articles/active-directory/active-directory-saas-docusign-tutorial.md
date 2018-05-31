---
title: Учебник. Интеграция Azure Active Directory с DocuSign | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 28c46e7071841552c5bcbf3df38d9d7771b0cfe6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345754"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Учебник. Интеграция Azure Active Directory с DocuSign

В этом руководстве описано, как интегрировать DocuSign с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением DocuSign обеспечивает перечисленные ниже преимущества.

- С помощью Azure AD вы можете контролировать доступ к DocuSign.
- Вы можете включить автоматический вход пользователей в DocuSign (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с DocuSign, вам потребуется:

- подписка Azure AD;
- подписка DocuSign с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление DocuSign из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-docusign-from-the-gallery"></a>Добавление DocuSign из коллекции
Чтобы настроить интеграцию DocuSign с Azure AD, необходимо добавить DocuSign из коллекции в список управляемых приложений SaaS.

**Чтобы добавить DocuSign из коллекции, выполните указанные ниже действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. В верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **DocuSign**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_search.png)

5. На панели результатов выберите **DocuSign** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение DocuSign с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в DocuSign соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в DocuSign.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в DocuSign.

Чтобы настроить и проверить единый вход Azure AD в DocuSign, вам потребуется выполнить действия в перечисленных ниже стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя DocuSign](#creating-a-docusign-test-user)** требуется для создания в DocuSign пользователя Britta Simon, связанного с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении DocuSign.

**Чтобы настроить единый вход Azure AD в DocuSign, выполните указанные ниже действия.**

1. На портале Azure на странице интеграции с приложением **DocuSign** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_samlbase.png)

3. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_certificate.png) 

4. В разделе **Настройка DocuSign** на портале Azure щелкните **Настроить DocuSign**, чтобы открыть окно "Настройка единого входа". Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.
    
    ![Настройка единого входа](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_configure.png)

5. В другом окне браузера войдите на **портал администрирования DocuSign** с правами администратора.

6. В меню навигации слева щелкните **Domains**(Домены).
   
    ![Настройка единого входа][51]

7. На правой панели щелкните **Claim Domain**(Зарезервировать домен).
   
    ![Настройка единого входа][52]

8. В диалоговом окне **Claim a domain** (Резервирование домена) в текстовом поле **Domain Name** (Доменное имя) введите соответствующее доменное имя своей компании и нажмите кнопку **Claim** (Зарезервировать). Обязательно проверьте домен. Состояние домена должно быть "Активный".
   
    ![Настройка единого входа][53]

9. На панели навигации слева щелкните **Identity Providers**  
   
    ![Настройка единого входа][54]
10. На панели справа нажмите кнопку **Add identity provider**(Добавить поставщик удостоверений). 
   
    ![Настройка единого входа][55]

11. На странице **параметров поставщика удостоверений** выполните следующие действия.
   
    ![Настройка единого входа][56]

    a. В соответствующее текстовое поле введите уникальное **имя** конфигурации. Не используйте пробелы.

    Б. Вставьте **идентификатор сущности SAML** в текстовом поле **Издатель поставщика удостоверений**.

    c. Вставьте **URL-адрес службы единого входа SAML** в текстовое поле **URL-адрес для входа поставщика удостоверений**.

    d. Вставьте **URL-адрес выхода** в текстовое поле **URL-адрес для выхода поставщика удостоверений**.

    д. Выберите **Sign AuthN Request**(Подпись запроса авторизации).

    f. Для параметра **Send AuthN request by** (Как отправлять запрос авторизации) выберите значение **POST**.

    ж. Для параметра **Send logout request by** (Как отправлять запрос на выход) выберите значение **GET**.

12. В разделе **Custom Attribute Mapping** (Сопоставление настраиваемого атрибута) выберите поле, которое нужно сопоставить с утверждением Azure AD. В этом примере утверждение **emailaddress** сопоставляется со значением **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Это имя утверждения по умолчанию из Azure AD, которое используется для утверждения на электронную почту. 
   
    > [!NOTE]
    > Чтобы сопоставить пользователя Azure AD с пользователем Docusign, используйте соответствующий **идентификатор пользователя**. Выберите соответствующее поле и введите подходящее значение на основе параметров вашей организации.
          
    ![Настройка единого входа][57]

13. В разделе **Identity Provider Certificate** (Сертификат поставщика удостоверений) нажмите кнопку **Add Certificate** (Добавить сертификат) и отправьте сертификат, который вы скачали на портале Azure AD.   
   
    ![Настройка единого входа][58]

14. Выберите команду **Сохранить**.

15. В разделе **Identity Providers** (Поставщики удостоверений) нажмите кнопку **Actions** (Действия), а затем выберите **Endpoints** (Конечные точки).   
   
    ![Настройка единого входа][59]
 
16. На **портале администрирования DocuSign** в разделе **View SAML 2.0 Endpoints** (Просмотр конечных точек SAML 2.0) выполните указанные ниже действия.
   
    ![Настройка единого входа][60]
   
    a. Скопируйте значение **Service Provider Issuer URL** (URL-адрес издателя поставщика услуг), а затем вставьте его в текстовое поле **Идентификатор** в разделе **Домены и URL-адреса приложения DocuSign** на портале Azure, используя следующий шаблон: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    Б. Скопируйте значение **Service Provider Login URL** (URL-адрес для входа поставщика услуг), а затем вставьте его в текстовое поле **URL-адрес для входа** в разделе **Домены и URL-адреса приложения DocuSign** на портале Azure, используя следующий шаблон: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/`.

    ![Настройка единого входа](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_url.png)
      
    c.  В нижней части страницы нажмите кнопку **Close**
    
17. На портале Azure нажмите кнопку **Сохранить**.
    
    ![Настройка единого входа](./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-docusign-test-user"></a>Создание тестового пользователя DocuSign

Приложение поддерживает **JIT-подготовку пользователей**, поэтому после проверки подлинности пользователи будут созданы в приложении автоматически.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к DocuSign.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в DocuSign, выполните указанные ниже действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **DocuSign**.

    ![Настройка единого входа](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку DocuSign на панели доступа, вы автоматически войдете в приложение DocuSign.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Руководство по настройке Google Apps для автоматической подготовки пользователей](active-directory-saas-docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_203.png

