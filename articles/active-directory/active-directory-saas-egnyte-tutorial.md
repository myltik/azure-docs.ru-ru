---
title: Учебник. Интеграция Azure Active Directory с Egnyte | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: ea9cb831845a4f99929deac5bc4c63f8c53e5238
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Руководство. Интеграция Azure Active Directory с Egnyte

В этом руководстве описано, как интегрировать Egnyte с Azure Active Directory (Azure AD).

Интеграция Egnyte c Azure AD обеспечивает следующие преимущества:

- Вы можете контролировать доступ к Egnyte с помощью Azure AD
- Вы можете включить автоматический вход пользователей в Egnyte (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Egnyte, вам потребуется:

- подписка Azure AD;
- Подписка с поддержкой единого входа Egnyte

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Egnyte из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-egnyte-from-the-gallery"></a>Добавление Egnyte из коллекции
Чтобы настроить интеграцию Egnyte с Azure AD, необходимо добавить Egnyte из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Egnyte из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Egnyte**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_search.png)

5. На панели результатов выберите **Egnyte** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Egnyte для тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Egnyte соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Egnyte.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Egnyte.

Чтобы настроить и проверить единый вход Azure AD в Egnyte, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Egnyte](#creating-an-egnyte-test-user)** требуется для создания пользователя Britta Simon в Egnyte, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Egnyte.

**Чтобы настроить единый вход Azure AD в Egnyte, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Egnyte** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_samlbase.png)

3. В разделе **Домены и URL-адреса Egnyte** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него необходимо указать фактический URL-адрес для входа. Для получения этого значения обратитесь в [службу поддержки клиентов Egnyte](https://www.egnyte.com/corp/contact_egnyte.html). 
 
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-egnyte-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Egnyte** щелкните **Настроить Egnyte**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_configure.png) 

7. В другом окне браузера войдите на корпоративный веб-сайт Egnyte в качестве администратора.

8. Щелкните **Параметры**.
   
   ![Параметры](./media/active-directory-saas-egnyte-tutorial/ic787819.png "Параметры")

9. В меню выберите пункт **Параметры**.

   ![Параметры](./media/active-directory-saas-egnyte-tutorial/ic787820.png "Параметры")

10. Откройте вкладку **Configuration** (Конфигурация) и нажмите элемент **Security** (Безопасность).

    ![Безопасность](./media/active-directory-saas-egnyte-tutorial/ic787821.png "Безопасность")

11. В разделе **Проверка подлинности единого входа** выполните следующие действия.

    ![Аутентификация единого входа](./media/active-directory-saas-egnyte-tutorial/ic787822.png "Аутентификация единого входа")   
    
    a. Выберите для параметра **Single sign-on authentication** (Аутентификация единого входа) значение **SAML 2.0**.
   
    Б. Выберите для параметра **Identity provider** (Поставщик удостоверений) значение **AzureAD**.
   
    c. Вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure, в поле **URL-адрес входа поставщика удостоверений**.
   
    d. Вставьте значение **Идентификатора сущности SAML**, скопированное на портале Azure, в поле **Идентификатор сущности поставщика удостоверений**.
      
    д. Откройте в блокноте сертификат в кодировке Base-64, скачанный с портала Azure, скопируйте его в буфер обмена и вставьте в текстовое поле **Сертификат поставщика удостоверений**.
   
    f. Выберите для параметра **Default user mapping** (Сопоставление пользователя по умолчанию) значение **Email address** (Адрес электронной почты).
   
    ж. Выберите для параметра **Use domain-specific issuer value** (Использовать значение издателя домена) значение **disabled** (отключено).
   
    h. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-an-egnyte-test-user"></a>Создание тестового пользователя Egnyte

Чтобы пользователи Azure AD могли выполнять вход в Egnyte, они должны быть подготовлены для Egnyte. В случае с Egnyte подготовка выполняется вручную.

**Чтобы подготовить учетные записи пользователей, выполните следующие действия.**

1. Войдите на корпоративный веб-сайт **Egnyte** в качестве администратора.

2. Последовательно выберите пункты **Settings \> Users & Groups** (Параметры > Пользователи и группы).

3. Нажмите **Добавить пользователя**, а затем выберите тип для пользователя, которого желаете добавить.
   
   ![Пользователи](./media/active-directory-saas-egnyte-tutorial/ic787824.png "Пользователи")

4. В разделе **Новый обычный пользователь** выполните следующие действия.
   
   ![Новый обычный пользователь](./media/active-directory-saas-egnyte-tutorial/ic787825.png "Новый обычный пользователь")   

   a. Введите значения **Адрес электронной почты**, **Имя пользователя** и другие данные действительной учетной записи Azure Active Directory, которую хотите подготовить.
   
   Б. Выберите команду **Сохранить**.
    
    >[!NOTE]
    >Владелец учетной записи Azure Active Directory получит уведомление по электронной почте.
    >

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя Egnyte или API, предоставляемые Egnyte для подготовки учетных записей пользователя AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon, предоставив этому пользователю доступ к Egnyte.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon для приложения Egnyte, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Egnyte**.

    ![Настройка единого входа](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Egnyte на панели доступа, вы автоматически войдете в приложение Egnyte.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_203.png

