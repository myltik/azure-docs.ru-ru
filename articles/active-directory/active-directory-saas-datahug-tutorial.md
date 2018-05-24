---
title: Руководство по интеграции Azure Active Directory с Datahug | Документы Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Datahug.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5c0dc1ea-7ff4-4554-b60b-0f2fa9f5abaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: jeedes
ms.openlocfilehash: 929b63ea3d78aafe6b96af40b9dce4decbd295b1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-datahug"></a>Руководство. Интеграция Azure Active Directory с Datahug

В этом руководстве описано, как интегрировать приложение Datahug с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Datahug обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Datahug.
- Вы можете включить автоматический вход пользователей в Datahug (единый вход) с применением учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Datahug, вам потребуется:

- подписка Azure AD;
- подписка Datahug с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Datahug из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-datahug-from-the-gallery"></a>Добавление Datahug из коллекции
Чтобы настроить интеграцию Datahug с Azure AD, нужно добавить Datahug из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Datahug из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Datahug**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_search.png)

5. На панели результатов выберите **Datahug** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Datahug с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD нужно знать, какой пользователь в Datahug соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Datahug.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Datahug.

Чтобы настроить и проверить единый вход Azure AD в Datahug, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Datahug](#creating-a-datahug-test-user)** требуется для создания пользователя Britta Simon в Datahug, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Datahug.

**Чтобы настроить единый вход Azure AD в Datahug, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Datahug** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Datahug** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_ur1.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://apps.datahug.com/identity/<uniqueID>`

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://apps.datahug.com/identity/<uniqueID>/acs`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**.

    ![Настройка единого входа](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_url2.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в формате `https://apps.datahug.com/`.
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения идентификатора и URL-адреса ответа. Мы рекомендуем использовать уникальное значение строки идентификатора и URL-адреса ответа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Datahug](http://datahug.com/about/contact-us/). 

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_certificate.png) 

6.  Установите флажок **Показать дополнительные параметры подписывания сертификатов** и выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_cert.png)

    a. В поле **Вариант подписывания** выберите **Утверждение знака SAML**.
    
    Б. В поле **Алгоритм подписывания** выберите **SHA1**.
 
7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-datahug-tutorial/tutorial_general_400.png)
    
8. В разделе **Конфигурация Datahug** щелкните **Настроить Datahug**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML** и **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_configure.png) 

9. Чтобы настроить единый вход на стороне **Datahug**, нужно отправить скачанный **XML-файл метаданных**, **идентификатор сущности SAML** и **URL-адрес службы единого входа SAML** в [службу поддержки Datahug](http://datahug.com/about/contact-us/). Это позволит службе поддержки правильно настроить подключение единого входа SAML для приложения на обоих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-datahug-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-datahug-test-user"></a>Создание тестового пользователя Datahug

Чтобы пользователи Azure AD могли выполнять вход в Datahug, они должны быть подготовлены для Datahug.  
Эта подготовка для Datahug выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на веб-сайт организации Datahug в качестве администратора.

2. Наведите указатель мыши на **шестеренку** в верхнем правом углу и выберите **Settings** (Параметры).
   
   ![Добавление сотрудника](./media/active-directory-saas-datahug-tutorial/1.png)

3. Выберите элемент **People** (Люди) и откройте вкладку **Add Users** (Добавление пользователей).

    ![Добавление сотрудника](./media/active-directory-saas-datahug-tutorial/2.png)

4. Введите адрес электронной почты человека, для которого вы хотите создать учетную запись, и нажмите кнопку **Добавить**.

    ![Добавление сотрудника](./media/active-directory-saas-datahug-tutorial/3.png)

    > [!NOTE] 
    > Вы можете отправить пользователю сообщение о регистрации, установив флажок **Send welcome email** (Отправить приветственное сообщение).  
    > При создании учетной записи для Salesforce не отправляйте приветственное сообщение.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Datahug.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Datahug, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Datahug**.

    ![Настройка единого входа](./media/active-directory-saas-datahug-tutorial/tutorial_datahug_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.
Щелкнув элемент Datahug на панели доступа, вы автоматически войдете в приложение Datahug. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-datahug-tutorial/tutorial_general_203.png

