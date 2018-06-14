---
title: Руководство по интеграции Azure Active Directory с SAP Cloud for Customer | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в SAP Cloud for Customer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 18172fb634c369fb4add5fe7c55e5fec08df7670
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352309"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Руководство по интеграции Azure Active Directory с SAP Cloud for Customer

В этом руководстве описано, как интегрировать SAP Cloud for Customer с Azure Active Directory (Azure AD).

Интеграция SAP Cloud for Customer с Azure AD обеспечивает следующие преимущества:

- с помощью Azure AD вы можете контролировать доступ к приложению SAP Cloud for Customer;
- вы можете включить автоматический вход пользователей в SAP Cloud for Customer (единый вход) под учетной записью Azure AD;
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с SAP Cloud for Customer, вам потребуется:

- подписка Azure AD;
- подписка SAP Cloud for Customer с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SAP Cloud for Customer из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Добавление SAP Cloud for Customer из коллекции
Чтобы настроить интеграцию SAP Cloud for Customer с Azure AD, необходимо добавить SAP Cloud for Customer из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SAP Cloud for Customer из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **SAP Cloud for Customer**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_search.png)

5. На панели результатов выберите **SAP Cloud for Customer** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в SAP Cloud for Customer с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SAP Cloud for Customer соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SAP Cloud for Customer.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SAP Cloud for Customer.

Чтобы настроить и проверить единый вход Azure AD в SAP Cloud for Customer, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SAP Cloud for Customer](#creating-a-sap-cloud-for-customer-test-user)** требуется для того, чтобы в SAP Cloud for Customer существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении SAP Cloud for Customer.

**Чтобы настроить единый вход Azure AD в SAP Cloud for Customer, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **SAP Cloud for Customer** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_samlbase.png)

3. В разделе **Домен и URL-адреса приложения SAP Cloud for Customer** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<server name>.crm.ondemand.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<server name>.crm.ondemand.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). 

4. В разделе **Атрибуты пользователя** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_attribute.png)

    a. Из списка **Идентификатор пользователя** выберите функцию **ExtractMailPrefix**.

    Б. В списке **Почта** выберите атрибут пользователя, который вы хотите использовать в своей реализации.
    Например, если в качестве уникального идентификатора пользователя вы хотите использовать EmployeeID и сохранили значение атрибута в ExtensionAttribute2, выберите user.extensionattribute2.  

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_400.png)

7. В разделе **Конфигурация SAP Cloud for Customer** щелкните **Настроить SAP Cloud for Customer**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_configure.png) 

8. В разделе "Конфигурация единого входа" выполните следующие действия:
   
    a. Войдите на портал SAP Cloud for Customer с правами администратора.
   
    Б. Перейдите к элементу **Application and User Management Common Task** (Общие задачи управления приложением и пользователем) и откройте вкладку **Поставщик удостоверений**.
   
    c. Щелкните **New Identity Provider** (Новый поставщик удостоверений) и выберите XML-файл метаданных, скачанный с портала Azure. Импортируя метаданные, система автоматически отправляет требуемые сертификаты подписи и шифрования.
   
    ![Настройка единого входа](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    г) В запросе SAML для Azure Active Directory требуется URL-адрес службы обработчика утверждений, поэтому установите флажок **Include Assertion Consumer Service URL** (Добавить URL-адрес службы обработчика утверждений).
   
    д. Щелкните **Activate Single Sign-On**(Активировать единый вход).
   
    f. Сохраните изменения.
   
    ж. Перейдите на вкладку **My System** (Моя система).
   
    ![Настройка единого входа](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. В текстовое поле **Azure AD Sign On URL** (URL-адрес входа Azure AD) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
   
    ![Настройка единого входа](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. Укажите, может ли сотрудник вручную выбрать вход с помощью учетных данных (идентификатора пользователя и пароля) или единый вход, щелкнув **Manual Identity Provider Selection**(Выбор поставщика удостоверений вручную).
   
    j. В разделе **SSO URL** (URL-адрес единого входа) укажите URL-адрес, который будет использоваться вашими сотрудниками для входа в систему. 
    В списке **URL Sent to Employee** (URL-адрес для отправки сотруднику) можно выбрать следующие параметры:
   
    **Non-SSO URL**
   
    Система отправит сотруднику обычный URL-адрес системы. Сотрудник не сможет входить в систему с помощью единого входа. Вместо этого ему необходимо использовать пароль или сертификат.
   
    **SSO URL** 
   
    Система отправит сотруднику URL-адрес единого входа. Сотрудник сможет входить в систему с помощью единого входа. Запрос на проверку подлинности перенаправляется через поставщик удостоверений.
   
    **Automatic Selection**
   
    Если функция единого входа неактивна, система отправит сотруднику обычный URL-адрес системы. Если функция единого входа активна, система проверит, есть ли у сотрудника пароль. При наличии пароля сотруднику будет отправлен URL-адрес единого входа и URL-адрес другого метода входа. Если пароля нет, сотруднику будет отправлен только URL-адрес единого входа.
   
    k. Сохраните изменения.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-sap-cloud-for-customer-test-user"></a>Создание тестового пользователя в SAP Cloud for Customer

В этом разделе описано, как создать пользователя Britta Simon в приложении SAP Cloud for Customer. Чтобы добавить пользователей на платформу SAP Cloud for Customer, обратитесь к [группе поддержки SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). 

> [!NOTE]
> Значение идентификатора имени должно совпадать с полем имени пользователя на платформе SAP Cloud for Customer.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к SAP Cloud for Customer.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении SAP Cloud for Customer, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SAP Cloud for Customer**.

    ![Настройка единого входа](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент SAP Cloud for Customer на панели доступа, вы автоматически войдете в приложение SAP Cloud for Customer.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_203.png

