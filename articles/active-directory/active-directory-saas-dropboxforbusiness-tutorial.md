---
title: "Руководство по интеграции Azure Active Directory с Dropbox for Business | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Dropbox for Business."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: a56a5af171eaca259db29f25fee4331a77313420
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Руководство. Интеграция Azure Active Directory с Dropbox for Business

В этом руководстве описано, как интегрировать Dropbox for Business с Azure Active Directory (Azure AD).

Интеграция Dropbox for Business с Azure AD обеспечивает перечисленные ниже преимущества.

- С помощью Azure AD вы можете контролировать доступ к приложению Dropbox for Business.
- Вы можете включить автоматический вход пользователей в Dropbox for Business (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Dropbox for Business, вам потребуется:

- подписка Azure AD;
- подписка Dropbox for Business с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Dropbox for Business из коллекции
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Добавление Dropbox for Business из коллекции
Чтобы настроить интеграцию Dropbox for Business с Azure AD, необходимо добавить Dropbox for Business из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Dropbox for Business из коллекции, выполните указанные ниже действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Приложения][2]
    
3. В верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Приложения][3]

4. В поле поиска введите **Dropbox for Business**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_search.png)

5. В области результатов выберите **Dropbox for Business** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Dropbox for Business с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Dropbox for Business соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Dropbox for Business.

Чтобы установить эту связь, следует указать **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Dropbox for Business.

Чтобы настроить и проверить единый вход Azure AD в Dropbox for Business, вам потребуется выполнить действия в перечисленных ниже стандартных блоках.

1. **[Настройка единого входа в Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Dropbox for Business](#creating-a-dropbox-for-business-test-user)** требуется для создания пользователя Britta Simon в Dropbox for Business, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Dropbox for Business.

**Чтобы настроить единый вход Azure AD в Dropbox for Business, выполните указанные ниже действия.**

1. На портале Azure на странице интеграции с приложением **Dropbox for Business** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Dropbox for Business** выполните указанные ниже действия.

    а. Войдите в клиент Dropbox for Business. 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769509.png "Настройка единого входа")
   
    b. На панели навигации слева щелкните **Консоль администрирования**. 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769510.png "Настройка единого входа")
   
    c. В **консоли администрирования** выберите пункт **Проверка подлинности** в левой области навигации. 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769511.png "Настройка единого входа")
   
    г) В разделе **Единый вход** установите флажок **Включить единый вход** и нажмите кнопку **Дополнительно**, чтобы развернуть этот раздел.  
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769512.png "Настройка единого входа")
   
    д. Скопируйте URL-адрес рядом с пунктом **Users can sign in by entering their email address or they can go directly to**(Пользователи могут входить, указывая адрес электронной почты, или напрямую переходить по адресу). 
    
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/ic769513.png)
    
    f. На портале Azure в текстовом поле **URL-адрес входа** вставьте URL-адрес.

    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_url.png)

     В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://www.dropbox.com/sso/<id>`

    > [!NOTE] 
    > Это значение приведено для примера. Измените его на фактический URL-адрес входа, полученный в разделе "Единый вход". Для получения этого значения обратитесь в [службу поддержки клиентов Dropbox for Business](https://www.dropbox.com/business/contact). 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Dropbox for Business** щелкните **Настроить Dropbox for Business**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_configure.png) 

7. Чтобы настроить единый вход на стороне **Dropbox for Business**, перейдите в клиент Dropbox for Business, а затем в разделе **Единый вход** на странице **Проверка подлинности** выполните указанные ниже действия. 
   
    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Настройка единого входа")
   
    а. Установите флажок **Обязательно**.
   
    b. На портале Azure в окне **Настройка единого входа** скопируйте значение в поле **URL-адрес службы единого входа SAML** и вставьте его в текстовое поле **Sign-in URL** (URL-адрес для входа).

    c. Щелкните **Choose certificate** (Выбрать сертификат), а затем выберите **файл сертификата в кодировке Base64**.

    г) Щелкните **Сохранить изменения**, чтобы завершить настройку клиента Dropbox for Business.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_01.png) 

2.  Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-dropboxforbusiness-tutorial/create_aaduser_04.png) 

    а. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Щелкните **Создать**.
 
### <a name="creating-a-dropbox-for-business-test-user"></a>Создание тестового пользователя Dropbox for Business

В этом разделе вы создадите в Dropbox for Business пользователя с именем Britta Simon. Приложение Dropbox for Business поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в Dropbox for Business еще не существует, он создается при попытке доступа к приложению Dropbox for Business.

>[!Note]
>Если вам нужно вручную создать пользователя, обратитесь в [службу поддержки клиентов Dropbox for Business](https://www.dropbox.com/business/contact). 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Dropbox for Business, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Dropbox for Business, выполните указанные ниже действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Dropbox for Business**.

    ![Настройка единого входа](./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_dropboxforbusiness_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При выборе плитки Dropbox for Business на панели доступа должна появиться страница входа приложения Dropbox for Business.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Руководство по настройке Google Apps для автоматической подготовки пользователей](active-directory-saas-dropboxforbusiness-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dropboxforbusiness-tutorial/tutorial_general_203.png

