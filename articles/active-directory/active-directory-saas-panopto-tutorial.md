---
title: Руководство по интеграции Azure Active Directory с Panopto | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Panopto.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: fdf04642816c9b0e6ca452263cc1bc04aca81d2e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>Учебник. Интеграция Azure Active Directory с Panopto

В этом руководстве описано, как интегрировать Panopto с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Panopto обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Panopto.
- Вы можете включить автоматический вход пользователей в Panopto (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Panopto, вам потребуется:

- подписка Azure AD;
- подписка Panopto с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Panopto из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-panopto-from-the-gallery"></a>Добавление Panopto из коллекции
Чтобы настроить интеграцию Panopto с Azure AD, необходимо добавить Panopto из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Panopto из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Panopto**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-panopto-tutorial/tutorial_panopto_search.png)

5. На панели результатов выберите **Panopto** и щелкните **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-panopto-tutorial/tutorial_panopto_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.

В этом разделе описана настройка и проверка единого входа Azure AD в Panopto с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какому пользователю в Azure AD соответствует пользователь в Panopto. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Panopto.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Panopto.

Чтобы настроить и проверить единый вход Azure AD в Panopto, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Panopto](#creating-a-panopto-test-user)** требуется для создания в Panopto пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в Panopto.

**Чтобы настроить единый вход Azure AD в Panopto, выполните следующее:**

1. На портале Azure на странице интеграции с приложением **Panopto** выберите **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-panopto-tutorial/tutorial_panopto_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Panopto** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-panopto-tutorial/tutorial_panopto_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<tenant-name>.panopto.com`

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес входа. Для получения этого значения обратитесь в [службу поддержки клиентов Panopto](mailto:support@panopto.com‎). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-panopto-tutorial/tutorial_panopto_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-panopto-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Panopto** выберите **Настроить Panopto**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-panopto-tutorial/tutorial_panopto_configure.png) 

7. В другом окне веб-браузера войдите на сайт Panopto своей компании в качестве администратора.

8. На панели слева щелкните **System** (Система), а затем щелкните **Identity Providers** (Поставщики удостоверений).
   
   ![Система](./media/active-directory-saas-panopto-tutorial/ic777670.png "Система")
9. Щелкните **Добавить поставщик**.
   
   ![Поставщики удостоверений](./media/active-directory-saas-panopto-tutorial/ic777671.png "Поставщики удостоверений")
   
10. В разделе сведений поставщика SAML выполните следующие действия:
   
    ![Конфигурация SaaS](./media/active-directory-saas-panopto-tutorial/ic777672.png "Конфигурация SaaS")
    
    a. В списке **Provider Type** (Тип поставщика) выберите пункт **SAML20**.    
    
    Б. В текстовом поле **Имя экземпляра** введите имя экземпляра.

    c. В текстовом поле **Понятное описание** введите понятное описание.
    
    d. В текстовое поле **Bounce Page Url** (URL-адрес страницы возврата) вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.

    д. В текстовое поле **Issuer** (Издатель) вставьте значение **идентификатора сущности SAML**, скопированное на портале Azure.

    f. Откройте в блокноте сертификат в кодировке Base-64, скачанный на портале Azure, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Public Key** (Открытый ключ).

11. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-panopto-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-panopto-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-panopto-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-panopto-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-panopto-test-user"></a>Создание тестового пользователя приложения Panopto

Действие для настройки подготовки пользователей в Panopto отсутствует.  
Когда назначенный пользователь пытается войти в Panopto с помощью панели доступа, Panopto проверяет, существует ли данный пользователь.  

Если учетная запись пользователя отсутствует, Panopto автоматически создает ее.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя Panopto или API, предоставляемые Panopto для подготовки учетных записей пользователя AAD.
>
>

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Panopto, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении** , выполните следующие действия:

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Panopto**.

    ![Настройка единого входа](./media/active-directory-saas-panopto-tutorial/tutorial_panopto_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Panopto на панели доступа, вы автоматически войдете в приложение Panopto.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-panopto-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-panopto-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-panopto-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-panopto-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-panopto-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-panopto-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-panopto-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-panopto-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-panopto-tutorial/tutorial_general_203.png

