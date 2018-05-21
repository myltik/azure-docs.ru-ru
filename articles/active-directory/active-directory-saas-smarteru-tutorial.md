---
title: Руководство по интеграции Azure Active Directory с SmarterU | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и SmarterU.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: a6845b023548c86ef90cac3aab35f22069daf34d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Учебник. Интеграция Azure Active Directory со SmarterU

В этом учебнике описано, как интегрировать SmarterU с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением SmarterU обеспечивает следующие преимущества.

- С помощью Azure AD можно управлять доступом к SmarterU.
- Вы можете включить автоматический вход пользователей в SmarterU (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с SmarterU, вам потребуется:

- подписка Azure AD;
- подписка SmarterU с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SmarterU из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-smarteru-from-the-gallery"></a>Добавление SmarterU из коллекции
Чтобы настроить интеграцию SmarterU с Azure AD, необходимо добавить SmarterU из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SmarterU из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **SmarterU**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smarteru-tutorial/tutorial_smarteru_search.png)

5. На панели результатов выберите **SmarterU** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smarteru-tutorial/tutorial_smarteru_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение SmarterU с использованием тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходимо знать, какому пользователю в Azure AD соответствует пользователь в SmarterU. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SmarterU.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SmarterU.

Чтобы настроить и проверить единый вход Azure AD в SmarterU, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SmarterU](#creating-a-smarteru-test-user)** требуется для создания в SmarterU пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении SmarterU.

**Чтобы настроить единый вход Azure AD в SmarterU, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **SmarterU** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-smarteru-tutorial/tutorial_smarteru_samlbase.png)

3. В разделе **Домен и URL-адреса приложения SmarterU** выполните следующие действия. 

    ![Настройка единого входа](./media/active-directory-saas-smarteru-tutorial/tutorial_smarteru_url.png)

    В текстовом поле **Идентификатор** введите URL-адрес: `https://www.smarteru.com/`

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-smarteru-tutorial/tutorial_smarteru_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-smarteru-tutorial/tutorial_general_400.png)

6. В другом окне веб-браузера войдите на сайт SmarterU своей компании в качестве администратора.

7. На панели инструментов вверху щелкните **Параметры учетной записи**.
   
    ![Параметры учетной записи](./media/active-directory-saas-smarteru-tutorial/IC777326.png "параметры учетной записи")

8. На странице конфигурации учетной записи выполните следующие действия.
   
    ![Внешняя авторизация](./media/active-directory-saas-smarteru-tutorial/IC777327.png "Внешняя авторизация") 
 
      a. Установите флажок **Включить внешнюю авторизацию**.
  
      Б. В разделе **Master Login Control** (Управление универсальным именем для входа) щелкните вкладку **SmarterU**.
  
      c. В разделе **User Default Login** (Имя для входа пользователей по умолчанию) щелкните вкладку **SmarterU**.
  
      d. Выберите **Включить Okta**.
  
      д. Скопируйте содержимое скачанного файла метаданных и вставьте его в текстовое поле **Метаданные Okta** .
  
      f. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smarteru-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smarteru-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smarteru-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-smarteru-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-smarteru-test-user"></a>Создание тестового пользователя SmarterU

Чтобы пользователи Azure AD могли входить в SmarterU, их необходимо подготовить в SmarterU.

В случае SmarterU подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход в клиент **SmarterU** .

2. Перейдите в раздел **Пользователи**.

3. В разделе «Пользователи» выполните следующие действия:
   
    ![Новый пользователь](./media/active-directory-saas-smarteru-tutorial/IC777329.png "Новый пользователь")  

    a. Щелкните **+ Пользователь**.
    
    Б. Введите соответствующие значения атрибутов учетной записи Azure AD в следующие текстовые поля: **Primary Email** (Основной электронный адрес), **Employee ID** (Идентификатор сотрудника), **Password** (Пароль), **Verify Password** (Проверка пароля), **Given Name** (Имя) и **Surname** (Фамилия).
    
    c. Нажмите **Активный**. 
    
    d. Выберите команду **Сохранить**.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователей SmarterU или API, предоставляемые SmarterU для подготовки учетных записей пользователей AAD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к SmarterU.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению SmarterU, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SmarterU**.

    ![Настройка единого входа](./media/active-directory-saas-smarteru-tutorial/tutorial_smarteru_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.
 
Щелкнув плитку SmarterU на панели доступа, вы автоматически войдете в приложение SmarterU.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-smarteru-tutorial/tutorial_general_203.png

