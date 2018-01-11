---
title: "Руководство по интеграции Azure Active Directory с SuccessFactors | Документация Майкрософт"
description: "Подробные сведения о настройке единого входа в Azure Active Directory и SuccessFactors."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: b9545599b4ac02927c38931777a3cee623a4e940
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Руководство. Интеграция Azure Active Directory с SuccessFactors

В этом учебнике вы узнаете, как интегрировать SuccessFactors с Azure Active Directory (Azure AD).

Интеграция SuccessFactors с Azure AD обеспечивает следующие преимущества.

- Можно управлять в Azure AD, который имеет доступ к SuccessFactors.
- Позволяет пользователям автоматически получить вошедшего в SuccessFactors (Single Sign-On) с помощью своих учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграцию Azure AD с SuccessFactors, вам потребуется:

- подписка Azure AD;
- SuccessFactors единый вход включен подписки

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SuccessFactors из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-successfactors-from-the-gallery"></a>Добавление SuccessFactors из коллекции
Чтобы настроить интеграцию SuccessFactors с Azure AD, необходимо добавить SuccessFactors из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SuccessFactors из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SuccessFactors**выберите **SuccessFactors** из панели результатов щелкните **добавить** кнопку, чтобы добавить приложение.

    ![SuccessFactors в списке результатов](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе настройки и тестирования в Azure AD единого входа с SuccessFactors, в зависимости от тестового пользователя с именем «Britta Simon».

Azure AD для единого входа для работы, необходимо знать пользователя аналога в SuccessFactors с пользователем в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SuccessFactors.

В SuccessFactors, присвойте значение **имя пользователя** в Azure AD в качестве значения **Username** для установления связи.

Чтобы настроить и проверить единый вход Azure AD в SuccessFactors, вам потребуется выполнить действия, описанные в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SuccessFactors](#create-a-successfactors-test-user)**  — на аналог Саймон Britta в SuccessFactors, связанного с представлением Azure AD пользователя.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе включения Azure AD единого входа на портале Azure и настройки единого входа в приложение SuccessFactors.

**Чтобы настроить единый вход Azure AD в SuccessFactors, выполните следующие действия.**

1. На портале Azure на **SuccessFactors** странице интеграции приложения щелкните **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_samlbase.png)

3. На **URL-адреса и домена SuccessFactors** выполните следующие действия:

    ![URL-адреса и SuccessFactors домена единого входа сведения](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Обратитесь к [группе поддержки SuccessFactors клиента](https://www.successfactors.com/en_us/support.html) для получения этих значений. 

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-successfactors-tutorial/tutorial_general_400.png)
    
6. На **конфигурации SuccessFactors** щелкните **Настройка SuccessFactors** Открытие **Настройка входа** окна. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_configure.png) 

7. В другом окне браузера, войдите в ваш **портал администрирования SuccessFactors** с правами администратора.
    
8. Откройте раздел **Application Security** (Безопасность приложения) и найдите в нем **функцию единого входа**. 

9. Введите любое значение в поле **Reset Token** (Сброс маркера) и нажмите кнопку **Save Token** (Сохранить маркер), чтобы включить единый вход SAML.
   
    ![Настройка единого входа на стороне приложения][11]

    > [!NOTE] 
    > Это значение используется как переключатель вкл. / выкл. Если здесь сохранено любое значение, единый вход SAML включен. Если сохранено пустое значение, единый вход SAML отключен.

10. Собственного на приведенном ниже снимке экрана и выполните следующие действия:
   
    ![Настройка единого входа на стороне приложения][12]
   
    a. Установите переключатель **SAML v2 SSO** (Единый вход SAML версии 2).
   
    Б. Задать **имени субъекта утверждения SAML**(например, издатель SAML + название компании).
   
    c. В **URL-адрес издателя** вставьте **идентификатор сущности SAML** значение, которое вы скопировали на портале Azure.
   
    d. Выберите значение **Response(Customer Generated/IdP/AP)** (Ответ (создается клиентом/IdP/AP)) для параметра **Require Mandatory Signature** (Требуется обязательная подпись).
   
    д. Выберите значение **Enabled** (Включено) для параметра **Enable SAML Flag** (Включить флаг SAML).
   
    f. Выберите значение **No** (Нет) для параметра **Login Request Signature (SF Generated/SP/RP)** (Подпись запроса на вход (создается SF/SP/RP)).
   
    ж. Выберите значение **Browser/Post Profile** (Браузер/пост-профилирование) для параметра **SAML Profile** (Профиль SAML).
   
    h. Выберите значение **No** (Нет) для параметра **Enforce Certificate Valid Period** (Применять период действия сертификата).
   
    i. Скопировать содержимое файла сертификата, загруженный из портала Azure, а затем вставьте его в **сертификата проверки SAML** текстового поля.

    > [!NOTE] 
    > Содержимое сертификата должно включать открывающий и закрывающий теги сертификата.

11. Перейдите в раздел SAML V2 и выполните следующие действия.
   
    ![Настройка единого входа на стороне приложения][13]
   
    a. Выберите значение **Yes** (Да) для параметра **Support SP-initiated Global Logout** (Поддержка глобального выхода, инициированного SP).
   
    Б. В **URL-адрес (назначение LogoutRequest) глобального выхода служба** вставьте **URL-адрес выхода** значение, которое вы скопировали форме портала Azure.
   
    c. Выберите значение **No** (Нет) для параметра **Require sp must encrypt all NameID element** (Требуется шифрование всех элементов NameID на стороне SP).
   
    d. Выберите значение **unspecified** (Не определено) для параметра **NameID Format** (Формат идентификатора имени).
   
    д. Выберите значение **Yes** (Да) для параметра **Enable sp initiated login (AuthnRequest)** (Включить вход по требованию SP).
   
    f. В **запрос на отправку в масштабе организации издателя** вставьте **SAML единого входа URL-адрес службы** значение, которое вы скопировали на портале Azure.

12. Выполните следующие действия, чтобы сделать имена входа без учета регистра.
   
    ![Настройка единого входа][29]
    
    a. Найдите в нижней части экрана раздел **Company Settings**(Параметры компании).
   
    Б. Установите флажок **Enable Non-Case-Sensitive Username** (Имя пользователя без учета регистра).
   
    в) Щелкните **Save**(Сохранить).
   
    > [!NOTE] 
    > При попытке включить эту возможность, система проверяет, если он создает повторяющееся имя входа SAML. Предположим, что у клиента есть пользователи с именами User1 и user1. Если отменить учет регистра, эти имена будут считаться одинаковыми. Система предоставляет сообщение об ошибке и не включает функцию. Пользователь должен изменить одно из имен пользователей, поэтому оно написано другой.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-successfactors-test-user"></a>Создание тестового пользователя SuccessFactors

Чтобы пользователи Azure AD для входа SuccessFactors, их необходимо подготовить в SuccessFactors.  
В случае SuccessFactors подготовка пользователей осуществляется вручную.

Чтобы создать пользователей в SuccessFactors, необходимо обратиться в [службу поддержки SuccessFactors](https://www.successfactors.com/en_us/support.html).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите Саймон Britta для использования Azure единого входа, предоставление доступа к SuccessFactors.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в SuccessFactors, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **SuccessFactors**.

    ![SuccessFactors ссылку в список приложений](./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку SuccessFactors на панели доступа, вы автоматически войдете в приложение SuccessFactors.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

[100]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_203.png

