---
title: "Руководство по интеграции Azure Active Directory с песочницей Salesforce | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Salesforce Sandbox."
services: active-directory
documentationCenter: na
author: MarkusVi
manager: mtillman
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: markvi
ms.openlocfilehash: d5c2b74995e0c0794628e2c79f66969835e0fabb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2018
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Учебник. Интеграция Azure Active Directory с песочницей Salesforce

В этом руководстве описано, как интегрировать Salesforce Sandbox с Azure Active Directory (Azure AD).

Интеграция Azure AD с Salesforce Sandbox обеспечивает следующие преимущества:

- С помощью Azure AD можно управлять доступом к Salesforce Sandbox.
- Вы можете включить автоматический вход пользователей в Salesforce Sandbox (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Salesforce Sandbox, вам потребуется:

- подписка Azure AD;
- подписка Salesforce Sandbox с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Salesforce Sandbox из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Добавление Salesforce Sandbox из коллекции
Чтобы настроить интеграцию Salesforce Sandbox с Azure AD, нужно добавить Salesforce Sandbox из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Salesforce Sandbox из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. В верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Salesforce Sandbox**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_search.png)

5. На панели результатов выберите **Salesforce Sandbox** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Salesforce Sandbox с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Salesforce Sandbox соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Salesforce Sandbox.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Salesforce Sandbox.

Чтобы настроить и проверить единый вход Azure AD в Salesforce Sandbox, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Salesforce Sandbox](#creating-a-salesforce-sandbox-test-user)** требуется для того, чтобы в Salesforce Sandbox существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Salesforce Sandbox.

**Чтобы настроить единый вход Azure AD в Salesforce Sandbox, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Salesforce Sandbox** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. В разделе **Домен и URL-адреса Salesforce Sandbox** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_url.png)

     В текстовом поле **URL-адрес для входа** введите значение в следующем формате: `https://<subdomain>.my.salesforce.com`.

    > [!NOTE] 
    > Это значение приведено для примера. Замените это значение фактическим URL-адресом входа. Обратитесь к [группе поддержки клиентов Salesforce Sandbox](https://help.salesforce.com/support) для получения этого значения.


4. Если вы уже настроили единый вход для другого экземпляра Salesforce Sandbox в каталоге, необходимо также настроить параметр **Идентификатор**, задав для него то же значение, что и для параметра **URL-адрес входа**. 
    
    >[!Note]
    >Поле **Идентификатор** можно найти, установив флажок **Показать дополнительные настройки** на странице **Настроить URL-адрес приложения** диалогового окна. 


5. В разделе **Сертификат подписи SAML** щелкните **Сертификат**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_400.png)

7. В разделе **Конфигурация Salesforce Sandbox** щелкните **Настроить Salesforce Sandbox**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_configure.png) 
<CS>
8. Откройте новую вкладку в браузере и войдите в учетную запись администратора Salesforce Sandbox.

9. В верхнем меню щелкните **Настройка**.

    ![Настройка единого входа](./media/active-directory-saas-salesforcesandbox-tutorial/IC781024.png)
10. В области навигации слева щелкните **Security Controls** (Средства управления безопасностью), а затем — **Параметры единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-salesforcesandbox-tutorial/IC781025.png)
11. В разделе "Single Sign-On Settings" (Параметры единого входа) сделайте следующее. ![Настройка единого входа](./media/active-directory-saas-salesforcesandbox-tutorial/IC781026.png)
     
     a.  Установите флажок **SAML включен**. 

     Б.  Нажмите кнопку **Создать**.

12. В разделе «Параметры единого входа SAML» выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-salesforcesandbox-tutorial/IC781027.png)

    В текстовое поле "Name" (Имя) введите имя конфигурации (например, *SPSSOWAAD\_Test*). 

    Б. Вставьте значение **SAML Entity ID** (Идентификатор сущности SAML) в поле **Issuer** (Издатель).

    c. В текстовом поле **Entity Id** (Идентификатор сущности) введите **https://test.salesforce.com**, если это первый экземпляр Salesforce Sandbox, добавляемый в каталог. Если вы уже добавили экземпляр Salesforce Sandbox, то для параметра **Идентификатор сущности** введите значение **URL-адрес входа** в следующем формате: `http://company.my.salesforce.com`  
 
    d. Чтобы отправить скачанный сертификат, нажмите кнопку **Обзор**.  

    д. В поле **SAML Identity Type** (Тип удостоверения SAML) выберите значение **Assertion contains the Federation ID from the User object** (Проверочное утверждение содержит идентификатор федерации из объекта User).
 
    f. В поле **SAML Identity Location** (Расположение удостоверения SAML) выберите значение **Identity is in the NameIdentifier element of the Subject statement** (Удостоверение находится в элементе NameIdentifier оператора Subject).

    ж. Вставьте **URL-адрес службы единого входа** в текстовое поле **Identity Provider Login URL** (URL-адрес входа поставщика удостоверений). 

    h. SFDC не поддерживает выход SAML.  Чтобы обойти эту проблему, вставьте значение https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0 в текстовое поле **URL-адрес для выхода поставщика удостоверений**.

    i. В поле **Service Provider Initiated Request Binding** (Связывание запросов, инициированных поставщиком услуг) выберите значение **HTTP POST**. 

    j. Выберите команду **Сохранить**.

### <a name="enable-your-domain"></a>Включение домена
В этом разделе предполагается, что вы уже создали домен.  Дополнительные сведения см. в разделе [Define Your Domain Name](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US) (Определение доменного имени).

**Выполните следующие действия, чтобы включить домен:**

1. В области навигации слева щелкните **Управление доменами**, затем — **Мой домен**.
   
     ![Настройка единого входа](./media/active-directory-saas-salesforcesandbox-tutorial/IC781029.png)
   
   >[!NOTE]
   >Убедитесь в правильности настройки домена. 

2. В разделе **Login Page Settings** (Параметры страницы входа) щелкните **Изменить**, а затем для параметра **Authentication Service** (Служба проверки подлинности) выберите имя параметра единого входа SAML из предыдущего раздела, после чего щелкните **Сохранить**.
   
   ![Настройка единого входа](./media/active-directory-saas-salesforcesandbox-tutorial/IC781030.png)

После настройки домена для входа в песочницу Salesforce пользователям необходимо будет использовать URL-адрес домена.  

Чтобы получить значение URL-адреса, щелкните профиль единого входа, созданный в предыдущем разделе.    

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-salesforce-sandbox-test-user"></a>Создание тестового пользователя Salesforce Sandbox

В этом разделе вы создадите в Salesforce Sandbox пользователя с именем Britta Simon. Salesforce Sandbox поддерживает JIT-подготовку. Эта функция включена по умолчанию.
В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в Salesforce Sandbox еще не существует, он создается при попытке доступа к Salesforce Sandbox.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Salesforce Sandbox.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Salesforce Sandbox, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Salesforce Sandbox**.

    ![Настройка единого входа](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа можно найти в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Руководство по настройке Google Apps для автоматической подготовки пользователей](active-directory-saas-salesforce-sandbox-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_203.png

