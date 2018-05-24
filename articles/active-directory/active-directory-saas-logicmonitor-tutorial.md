---
title: Учебник. Интеграция Azure Active Directory с LogicMonitor | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в приложении LogicMonitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 8812d2794c08d2ab7a50f07835cda77c159471c2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Руководство. Интеграция Azure Active Directory с LogicMonitor

В этом руководстве описано, как интегрировать LogicMonitor с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением LogicMonitor обеспечивает следующие преимущества.

- С помощью Azure AD можно управлять доступом к LogicMonitor.
- Вы можете включить автоматический вход пользователей в LogicMonitor (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с LogicMonitor, вам потребуется:

- подписка Azure AD;
- подписка LogicMonitor с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление LogicMonitor из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-logicmonitor-from-the-gallery"></a>Добавление LogicMonitor из коллекции
Чтобы настроить интеграцию LogicMonitor с Azure AD, необходимо добавить LogicMonitor из коллекции в список управляемых приложений SaaS.

**Чтобы добавить LogicMonitor из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **LogicMonitor**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_search.png)

5. На панели результатов выберите **LogicMonitor**, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в LogicMonitor с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в LogicMonitor соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в LogicMonitor.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в LogicMonitor.

Чтобы настроить и проверить единый вход Azure AD в LogicMonitor, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя LogicMonitor](#creating-a-logicmonitor-test-user)** требуется для создания в LogicMonitor пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении LogicMonitor.

**Чтобы настроить единый вход Azure AD в LogicMonitor, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **LogicMonitor** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_samlbase.png)

3. В разделе **Домен и URL-адреса приложения LogicMonitor** выполните следующие действия.

    ![Настройка единого входа](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.logicmonitor.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<companyname>.logicmonitor.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов LogicMonitor](https://www.logicmonitor.com/contact/). 
 


4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_400.png)

6. Выполните вход на веб-сайт компании **LogicMonitor** в качестве администратора.

7. В верхнем меню нажмите пункт **Параметры**.
   
   ![Параметры](./media/active-directory-saas-logicmonitor-tutorial/ic790052.png "Параметры")

8. В расположенной слева области навигации нажмите **Единый вход**
   
   ![Единый вход](./media/active-directory-saas-logicmonitor-tutorial/ic790053.png "Единый вход")

9. В разделе **Параметры единого входа** выполните следующие действия.
   
   ![Параметры единого входа](./media/active-directory-saas-logicmonitor-tutorial/ic790054.png "Параметры единого входа")
   
   a. Выберите пункт **Включить единый вход**.

   Б. Выберите для параметра **Default Role Assignment** (Назначение ролей по умолчанию) значение **readonly** (только для чтения).
   
   c. Откройте скачанный файл метаданных в Блокноте и вставьте содержимое этого файла в текстовое поле **Метаданные поставщика удостоверений** .
   
   d. Нажмите кнопку **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-logicmonitor-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-logicmonitor-test-user"></a>Создание тестового пользователя LogicMonitor

Чтобы пользователи AAD могли войти систему, они должны быть подготовлены для приложения LogicMonitor с использованием их имен пользователей Azure Active Directory.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Выполните вход на веб-сайт компании LogicMonitor в качестве администратора.

2. В меню вверху щелкните **Settings** (Параметры), а затем выберите **Roles and Users** (Роли и пользователи).
   
   ![Roles and Users](./media/active-directory-saas-logicmonitor-tutorial/ic790056.png "Roles and Users") (Роли и пользователи)

3. Щелкните **Добавить**.

4. В разделе **Добавить учетную запись** выполните следующие действия.
   
   ![Add an account](./media/active-directory-saas-logicmonitor-tutorial/ic790057.png "Add an account") (Добавление учетной записи)
   
   a. В соответствующие текстовые поля введите значения **имени пользователя**, **адреса электронной почты**, **пароля** и **подтверждения пароля** пользователя Azure Active Directory, которого требуется подготовить.
   
   Б. Выберите **Роли**, **Просмотр разрешений** и **Состояние**.
   
   c. Нажмите кнопку **Submit**(Отправить).

>[!NOTE]
>Вы можете использовать любые другие средства создания учетной записи пользователя LogicMonitor или API, предоставляемые LogicMonitor для подготовки учетных записей пользователя Azure Active Directory. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к LogicMonitor.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению LogicMonitor, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **LogicMonitor**.

    ![Настройка единого входа](./media/active-directory-saas-logicmonitor-tutorial/tutorial_logicmonitor_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.
 
Щелкнув плитку LogicMonitor на панели доступа, вы автоматически войдете в приложение LogicMonitor.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-logicmonitor-tutorial/tutorial_general_203.png

