---
title: Учебник. Интеграция Azure Active Directory с Lifesize Cloud | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Lifesize Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 84af3a9b726d990e585e2b12b1c0a6f4609fcb7e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344424"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Учебник. Интеграция Azure Active Directory с Lifesize Cloud

В этом учебнике описано, как интегрировать приложение Lifesize Cloud с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Lifesize Cloud обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Lifesize Cloud.
- Вы можете включить автоматический вход пользователей в Lifesize Cloud (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Lifesize Cloud, вам потребуется:

- подписка Azure AD;
- подписка Lifesize Cloud с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Lifesize Cloud из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Добавление Lifesize Cloud из коллекции
Чтобы настроить интеграцию Lifesize Cloud с Azure AD, необходимо добавить Lifesize Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Lifesize Cloud из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Lifesize Cloud**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_search.png)

5. На панели результатов выберите **Lifesize Cloud** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Lifesize Cloud с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Lifesize Cloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Lifesize Cloud.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Lifesize Cloud.

Чтобы настроить и проверить единый вход Azure AD в Lifesize Cloud, выполните действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Lifesize Cloud](#creating-a-lifesize-cloud-test-user)** требуется для создания в Lifesize Cloud пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Lifesize Cloud.

**Чтобы настроить единый вход Azure AD в Lifesize Cloud, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Lifesize Cloud** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Lifesize Cloud** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://login.lifesizecloud.com/ls/?acs`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://login.lifesizecloud.com/<companyname>`

     
4. Установите флажок **Показывать дополнительные параметры URL-адреса** и выполните следующие действия:    
   
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_url1.png)

    В текстовое поле **Состояние ретранслятора** введите URL-адрес в следующем формате: `https://webapp.lifesizecloud.com/?ent=<identifier>`
   
   > [!NOTE] 
   >Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо заменить эти значения фактическим URL-адресом для входа и идентификатором, значением состояния ретранслятора и идентификатора. Обратитесь в [службу поддержки клиентов Lifesize Cloud](https://www.lifesize.com/support) для получения URL-адреса входа и значений идентификаторов, а также значения состояния ретрансляции из конфигурации единого входа, которое описывается далее в этом учебнике.

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Lifesize Cloud** щелкните **Настроить Lifesize Cloud**, чтобы открыть окно **Настройка входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_configure.png) 

7. Для настройки единого входа в вашем приложении необходимо войти в приложение Lifesize Cloud с правами администратора.

8. В правом верхнем углу страницы щелкните свое имя и выберите **Advance Settings** (Дополнительные параметры).
   
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

9. В меню Advance Settings (Дополнительные параметры) щелкните ссылку **SSO Configuration** (Конфигурация единого входа). Откроется страница "SSO Configuration" (Конфигурация единого входа) для вашего экземпляра.
   
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

10. Теперь настройте следующие значения в пользовательском интерфейсе единого входа.    
   
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
    
    a. В текстовое поле **Издатель поставщика удостоверений** вставьте значение **идентификатора сущности SAML**, скопированное на портале Azure.

    Б.  В текстовое поле **Login URL** (URL-адрес входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    c. Откройте в блокноте сертификат в кодировке Base-64, скачанный с портала Azure, скопируйте его в буфер обмена и вставьте в текстовое поле **Сертификат X.509**.
  
    d. В разделе SAML Attribute Mappings (Сопоставления атрибутов SAML) в текстовом поле First Name (Имя) введите значение в следующем формате: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    
    д. В разделе SAML Attribute Mappings (Сопоставления атрибутов SAML) в текстовом поле **Last Name** (Фамилия) введите значение в следующем формате: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    
    f. В разделе SAML Attribute Mappings (Сопоставления атрибутов SAML) в текстовом поле **Email** (Электронная почта) введите значение в следующем формате: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

11. Чтобы проверить конфигурацию, можно нажать кнопку **Test** (Проверить).
   
    >[!NOTE]
    >Чтобы проверка прошла успешно, необходимо завершить работу мастера настройки в Azure AD, а также предоставить доступ пользователям или группам, которые будут выполнять эту проверку.

12. Чтобы включить единый вход, установите флажок **Enable SSO** (Включить единый вход).

13. Затем нажмите кнопку **Update** (Обновить), чтобы сохранить все параметры. При этом будет создано значение RelayState. Скопируйте значение RelayState, созданное в текстовом поле, и вставьте его в поле **Состояние ретранслятора** в разделе **URL-адреса и домены Lifesize Cloud**. 

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-lifesize-cloud-test-user"></a>Создание тестового пользователя Lifesize Cloud

В этом разделе описано, как создать пользователя Britta Simon в приложении Lifesize Cloud. Lifesize Cloud поддерживает автоматическую подготовку пользователей. После успешной аутентификации в Azure AD для пользователя будет автоматически выполняться подготовка в приложении. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Lifesize Cloud.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в FileCloud, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Lifesize Cloud**.

    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesize-cloud_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

После щелчка элемента Lifesize Cloud на панели доступа откроется страница входа в приложение Lifesize Cloud.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png

