---
title: Учебник. Интеграция Azure Active Directory с LinkedInSalesNavigator | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в LinkedInSalesNavigator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: jeedes
ms.openlocfilehash: 2c8dcf4ad49b8f261814c2d442bfdded221e8cbf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349939"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Руководство по интеграции Azure Active Directory с LinkedIn Sales Navigator

В этом руководстве описано, как интегрировать приложение LinkedIn Sales Navigator с Azure Active Directory (Azure AD).

Интеграция Azure AD с LinkedIn Sales Navigator обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к LinLinkedIn Sales Navigator.
- Вы можете включить автоматический вход пользователей в LinkedIn Sales Navigator (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее об интеграции приложений SaaS с Azure AD можно узнать в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с LinkedIn Sales Navigator, вам потребуется:

- подписка Azure AD;
- подписка LinkedIn Sales Navigator с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление LinkedIn Sales Navigator из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Добавление LinkedIn Sales Navigator из коллекции
Чтобы настроить интеграцию LinkedIn Sales Navigator с Azure AD, нужно добавить LinkedIn Sales Navigator из коллекции в список управляемых приложений SaaS.

**Чтобы добавить LinkedIn Sales Navigator из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. В верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **LinkedIn Sales Navigator**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_search.png)

5. На панели результатов выберите **LinkedIn Sales Navigator** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение LinkedIn Sales Navigator с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD нужно знать, какой пользователь в LinkedIn Sales Navigator соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в LinkedIn Sales Navigator.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в LinkedIn Sales Navigator.

Чтобы настроить и проверить единый вход Azure AD в LinkedIn Sales Navigator, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя LinkedIn Sales Navigator](#creating-a-linkedin-sales-navigator-test-user)** требуется для создания пользователя Britta Simon в LinkedIn Sales Navigator, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении LinkedIn Sales Navigator.

**Чтобы настроить единый вход Azure AD в LinkedIn Sales Navigator, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **LinkedIn Sales Navigator** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_samlbase.png)

3. В другом окне веб-браузера войдите на свой веб-сайт **LinkedIn Sales Navigator** в качестве администратора.

4. В **Account Center** (Центр учетных записей) в разделе **Settings** (Параметры) щелкните **Global Settings** (Глобальные параметры). Также выберите **Sales Navigator** в раскрывающемся списке.

    ![Настройка единого входа](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

5. Щелкните **OR Click Here to load and copy individual fields from the form** (Или щелкните здесь, чтобы загрузить и скопировать отдельные поля из формы) и скопируйте значения **Entity Id** (Идентификатор сущности) и **Assertion Consumer Service (ACS) Url** (URL-адрес службы обработчика утверждений (ACS)).

    ![Настройка единого входа](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

6. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения LinkedIn Sales Navigator** портала Azure выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url1.png)

    a. В текстовом поле **Идентификатор** введите **идентификатор сущности**, скопированный с портала LinkedIn. 

    Б. В текстовом поле **URL-адрес ответа** введите **URL-адрес службы обработчика утверждений (ACS)**, скопированный с портала LinkedIn.

7. Установите флажок **Показать дополнительные параметры URL-адресов**, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**.

    ![Настройка единого входа](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url2.png)

    В текстовом поле **URL-адрес для входа** введите значение в следующем формате: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`.

8. Приложение **LinkedIn Sales Navigator** ожидает утверждения SAML в определенном формате, а для этого вам нужно добавить настраиваемые сопоставления атрибутов в конфигурацию атрибутов токена SAML. На следующем снимке экрана показан пример. По умолчанию **идентификатор пользователя** имеет значение **user.userprincipalname**, но для LinkedIn Sales Navigator требуется сопоставить это значение с адресом электронной почты пользователя. Можно использовать атрибут **user.mail** из списка или соответствующее значение атрибута, основанное на конфигурации организации. 

    ![Настройка единого входа](./media/active-directory-saas-linkedinsalesnavigator-tutorial/updateusermail.png)
    
9. В разделе **Атрибуты пользователя** установите флажок **Просмотреть и изменить все другие атрибуты пользователей** и задайте эти атрибуты. Пользователь должен добавить четыре утверждения — **email**, **department**, **firstname** и **lastname**, а их значения должны быть сопоставлены с **user.mail**, **user.department**, **user.givenname** и **user.surname**, соответственно

    | Имя атрибута | Значение атрибута |
    | --- | --- |    
    | email| user.mail |
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/userattribute.png)
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно атрибута.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_attribute_04.png)
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_attribute_05.png)
   
    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

10. Выполните следующие действия с атрибутом **name**.

    a. Щелкните атрибут, чтобы открыть окно **Изменить атрибут**.

    ![Настройка единого входа](./media/active-directory-saas-linkedinsalesnavigator-tutorial/url_update.png)

    Б. Удалите значение URL-адреса из **пространства имен**.
    
    c. Нажмите кнопку **ОК**, чтобы сохранить настройки.

11. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_certificate.png) 

12. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_400.png)

13. Перейдите в раздел **LinkedIn Admin Settings** (Параметры администратора LinkedIn). Щелкните **Upload XML file** (Отправить XML-файл), чтобы отправить XML-файл метаданных, скачанный с портала Azure.

    ![Настройка единого входа](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

14. Нажмите кнопку **On** (Включить), чтобы включить единый вход. Состояние единого входа изменяется с **Not Connected** (Не подключено) на **Connected** (Подключено.)

    ![Настройка единого входа](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)


> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_01.png) 

2. Перейдите в раздел **Пользователи и группы** и выберите **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-linkedin-sales-navigator-test-user"></a>Создание тестового пользователя LinkedIn Sales Navigator

Приложение LinkedIn Sales Navigator поддерживает JIT-подготовку пользователей, поэтому после проверки подлинности пользователи создаются в приложении автоматически. Включите параметр **Automatically assign licenses** (Автоматически назначать лицензии), чтобы назначить лицензию пользователю.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к LinkedIn Sales Navigator.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в LinkedIn Sales Navigator, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **LinkedIn Sales Navigator**.

    ![Настройка единого входа](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При щелчке элемента LinkedIn Sales Navigator на панели доступа должна открыться страница организации, где нужно указать сведения о личной учетной записи LinkedIn. В результате ваша личная учетная запись связывается с вашей рабочей учетной записью LinkedIn. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_203.png

