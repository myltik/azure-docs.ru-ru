---
title: Руководство по интеграции Azure Active Directory с Tableau Server | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: e2db0f36be5c102ebf87a2ca2ed4bb53b81cb742
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Учебник. Интеграция Azure Active Directory с Tableau Server

В этом руководстве описано, как интегрировать Tableau Server с Azure Active Directory (Azure AD).

Интеграция Tableau Server с Azure AD дает приведенные далее преимущества:

- С помощью Azure AD вы можете контролировать доступ к Tableau Server.
- Вы можете включить автоматический вход пользователей в Tableau Server (единый вход) с применением учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением Tableau Server, вам потребуется следующее:

- подписка Azure AD;
- подписка Tableau Server с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление сервера Tableau Server из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-tableau-server-from-the-gallery"></a>Добавление сервера Tableau Server из коллекции
Чтобы настроить интеграцию Tableau Server с Azure AD, необходимо добавить Tableau Server из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Tableau Server из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Tableau Server**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_search.png)

5. На панели результатов выберите **Tableau Server** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Tableau Server для тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Tableau Server соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Tableau Server.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Tableau Server.

Чтобы настроить и проверить единый вход Azure AD в Tableau Server, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Tableau Server](#creating-a-tableau-server-test-user)** требуется для создания в Tableau Server пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Tableau Server.

**Чтобы настроить единый вход Azure AD в Tableau Server, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Tableau Server** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. В разделе **Домены и URL-адреса Tableau Server** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://azure.<domain name>.link`
    
    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://azure.<domain name>.link`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://azure.<domain name>.link/wg/saml/SSO/index.html`.
     
    > [!NOTE] 
    > Приведенные выше значения используются только для примера. Их необходимо заменить на фактические URL-адрес и идентификатор, которые можно найти на странице конфигурации Tableau Server. 

4. Приложение Tableau Server ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.
    
    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/3.png)
    
5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия:
    
    | Имя атрибута | Значение атрибута |
    | ---------------| --------------- |    
    | Имя пользователя | *user.mailnickname* |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_04.png)

    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_officespace_05.png)
    
    Б. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.


6. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. Чтобы единый вход был настроен для вашего приложения, войдите в клиент Tableau Server с правами администратора.
   
   a. В окне конфигурации Tableau Server откройте вкладку **SAML** .
  
    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   Б. Установите флажок **Использовать SAML для единого входа**.
   
   c. URL-адрес возврата Tableau Server — URL-адрес для доступа пользователей Tableau Server, например http://tableau_server. Не рекомендуем использовать http://localhost. URL-адреса с конечной косой чертой (например, http://tableau_server/) не поддерживаются. Скопируйте **URL-адрес возврата Tableau Server** и вставьте его в текстовое поле **URL-адрес входа** Azure AD в разделе **Домены и URL-адреса Tableau Server**.
   
   d. Идентификатор сущности SAML — идентификатор сущности однозначно определяет установку Tableau Server для поставщика удостоверений. Если нужно, здесь можно еще раз ввести URL-адрес Tableau Server, но это необязательно должен быть URL-адрес Tableau Server. Скопируйте **Идентификатор сущности SAML** и вставьте его в текстовое поле **Идентификатор** Azure AD в разделе **Домены и URL-адреса Tableau Server**.
     
   д. Нажмите кнопку **Экспортировать файл метаданных** и откройте его в текстовом редакторе. Найдите URL-адрес службы обработчика утверждений с Http Post и индексом 0 и скопируйте URL-адрес. Вставьте его в текстовое поле **URL-адрес ответа** в разделе **Домены и URL-адреса Tableau Server**.
   
   f. Найдите файл метаданных федерации, скачанный с портала Azure, и отправьте его в разделе **файл метаданных поставщика удостоверений SAML**.
   
   ж. Нажмите кнопку **ОК** на странице конфигурации Tableau Server.
   
    >[!NOTE] 
    >Клиенту необходимо отправить любой сертификат во время настройки единого входа SAML Tableau Server, и этот сертификат будет проигнорирован в потоке единого входа.
    >Более подробные сведения о настройке SAML в Tableau Server см. в статье о [настройке SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).
    >
<CE>

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-tableau-server-test-user"></a>Создание тестового пользователя Tableau Server

Цель этого раздела — создать в приложении Tableau Server пользователя с именем Britta Simon. Необходимо подготовить всех пользователей в приложении Tableau Server. 

Имя пользователя должно совпадать со значением, настроенным в пользовательском атрибуте Azure AD **username**. В случае правильного сопоставления интеграция должна обеспечить [настройку единого входа в Azure AD](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Если необходимо создать пользователя вручную, обратитесь к администратору Tableau Server в вашей организации.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon и предоставить этому пользователю доступ к Tableau Server.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Tableau Server, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Tableau Server**.

    ![Настройка единого входа](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Tableau Server на панели доступа, вы автоматически войдете в приложение Tableau Server.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png

